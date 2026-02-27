---
date: 2025-09-20
time: 20:46
author:
title: "Memento: Fine-tuning LLM Agents without Fine-tuning LLMs"
created-date: 2025-09-20
tags:
  - episodic-memory
  - case-based-reasoning
paper: https://arxiv.org/abs/2508.16153
code: https://github.com/Agent-on-the-Fly/Memento
zks-type: lit
is_public: true
---
- explicit episodic memory  
- learning to retrieve with policy network (2 layer MLP)
- case based reasoning
## Description of result
- [GAIA](../benchmarks/GAIA.md)
	- **top-1 performance on validation set** (87.88% Pass@3) 
		- case bank accumulates over 3 iter
	- 79.40% on the private test set (4th place)
		- case bank initialized from validation set

![](assets/Pasted%20image%2020251009135216.png)
![](assets/Pasted%20image%2020251003215024.png)
![](assets/Pasted%20image%2020251126030950.png)
- DeepResearcher dataset (7 QA datasets compiled from DeepResearcher): **66.6% F1 and 80.4% PM**.
	- OOD QA: Case-based memory provided significant gains on out-of-distribution (OOD) tasks, adding 4.7% to 9.6% absolute points across Musique, Bamboogle, and PopQA.

![](assets/Pasted%20image%2020251003214856.png)

- 95.0% accuracy on SimpleQA, setting a new SOTA
- 24.4% PM on Humanity’s Last Exam (HLE), ranking second overall and nearing the performance of GPT-5.

---
## How it compares to previous work
Existing approaches for LLM agents present fundamental limitations that Memento addresses:
- lack of adaptation upon deployment
- intensive fine tuning/RL on the LLM, with risk of catastrophic forgetting

Memento provides a **scalable and efficient pathway** for developing generalist LLM agents capable of **continuous, real-time learning without gradient updates to LLM**. external memory (store episodic traces, including success and failure labels) to optimize the prompt construction process. 

![](assets/Pasted%20image%2020251010150929.png)
(note: refer to actual paper for eqns. last row is actual memento, b4 that is ablation, first 3 are other works)

- no citation of 
	- [Learning to Use Episodic Memory](https://web.eecs.umich.edu/~soar/sitemaker/docs/pubs/LearningToUseEpMem-ICCM09.pdf) (Gorski & Laird 2009 ICCM) ?
	- Auxiliary Cross Attention Network?

---
## Main strategies used to obtain results
NOTE: they define case as `(state, action, reward)` and episodic memory as `(state, case, Q-value)` (while i usually refer to the former as episodic memory). State is the task, action is the **PLAN**

![](assets/Pasted%20image%2020251003212132.png)

Memento integrates LLM agents with **Case-Based Reasoning (CBR)**, modeling the sequential decision-making process as a **Memory-Based Markov Decision Process (M-MDP)**.

It is implemented as a **planner–executor architecture** that learns on the fly.

- **Planner:** An LLM (e.g., GPT-4.1) acting as a CBR agent responsible for decomposition, planning, and replanning.
- **Executor:** A general-purpose LLM (e.g., o4-mini or o3) responsible for executing subtasks and interacting with external tools via the **Model Context Protocol (MCP)**. Different model from planner, see later counterintuitive results for explanation
- **Case Bank:** A growing external episodic memory that stores past trajectories in `(state, action, reward)` triplets for online CBR.

The system alternates between **Case-Based Planning** (retrieving cases from memory to guide the plan) and **Tool-Based Execution** (using tools to complete subtasks). 
## Framework
- Planner retrieves relevant cases `(state, action, reward)` where state is task, action is plan, and constructs a plan
- subtask memory module (working mem) orchestrates interaction b/w planner and executor, recording subtasks and outcomes
- after each iter, planner uses accumulated execution history to assess task completion
	- if false, replan based on updated context
	- if success, return final result, update case memory
- executor 
	- reads pending subtasks from subtask memory, 
	- accesses relevant history from tool memory (logs of tool interactions for each subtask)
	- determines whether to tool call or return result
- Planner and executor have their own threads per task, but within it contains a shared message history of (see `_add_to_history` usage in `client/parametric_memory_cbr.py`)
	- query
	- retrieved cases
	- plans for current query (including revisions)
	- final response from executor per subtask
### Case Memory Mechanisms
**TLDR; Learn to retrieve**
![](assets/Pasted%20image%2020251010172739.png)

- **WARNING** section 3 formalizes the setting, only to reduce it to training via binary cross entropy in section 4.2. For the practical minded-reader, ignore section 3

#### Writing to memory
- CLAIM: The `Write` operation concurrently updates a Q-function (2 layer MLP) online to learn the retrieval distribution.
	- however in the code `client/parametric_memory_cbr.py`, there doesnt seem to be any online updating of Q-function; it just collects the data while the training occurs in a separate script `memory/train_memory_retriever.py`
- only the `(state, action, and reward)` from the final step of each trajectory are written to memory
- The case memory `(query, plan, success)` for each task is accumulated and carried over to other tasks, as seen from  `client/parametric_memory_cbr.py`'s `save_memory_entry` and `client._load_memory()` at the end of each task
	- **WARNING**: In the code, they use the dict key `case` to save and load the *query* (can be confusing as in the paper, the case is the `(query, plan, success)`)
##### Saving training data
The data for training the Q function is saved via `save_training_data` at the end of each task.

It takes in `(query, retrieved_cases, is_correct)`,  and loops over retrieved_cases: each retrieved case forms 1 data point, so if retrieval k=4, we have 4 data points

when expanded we have `(query, (case_query, plan, case_success), is_correct)`, where to clarify:
- `(case_query, plan, case_success)` is the memory, i.e. at some point in the past, the agent was tasked with `case_query`, performed `plan`, and resulted in `case_success`
- `query` is the current task the agent is solving, which triggered the retrieval of the memory `(case_query, plan, case_success)`
- `is_correct` is the correctness of the agent when answering `query` GIVEN that it retrieved the case memory

#### Training Q
- code in memory/train_memory_retriever.py
- from above description of save training data, we use the `query` and a formatted version of `case_query` + `plan` (henceforth called `icl_text`) as inputs to the Q network, and train it to output `is_correct` (see `PairJsonlDataset`)
- Since the reward signal in deep research tasks is binary (${0, 1}$), the training objective is formulated as a binary classification loss (Cross-Entropy loss) to avoid the vanishing gradient problem associated with Mean Squared Error (MSE) near the boundaries. 

#### retrieval
- (initialization) The case bank is loaded via `load_pool`, which formats the `case_query` + `plan` into a single text per case
- during a `query`, the Q-function computes the score by input of `query` and the above, done for all cases
- The `Read` operation uses the learned Q-function to select the Top-K cases with the highest Q-values as planning references.
### Tool-Enabled Execution

The executor leverages a comprehensive suite of tools accessed via the Model Context Protocol (MCP), enabling dynamic reasoning and compositional tool use across multiple domains:

- **External Information Acquisition:** Includes a search toolkit (e.g., searxng metasearch engine) and a web crawler (Crawl4AI) to fetch and parse full web content.
- **Multimodal Heterogeneous Information Processing:** Automatically extracts information from various file types and modalities, such as 
	- vision-language model (VLM) for image captioning or transcription for audio.
	- conversion between formats
- **Reasoning:** Integrates tools for sandboxed code execution (supporting libraries like numpy, pandas, torch) and mathematical computation.

---

## Ablations
- Offline Executor: one static executor, no planner, case memory, tools,  
	- reflects only raw parametric knowledge from LLMs. 
- Online Executor: Offline + tools
	- reflecting the value of real-time retrieval and tool execution.
- Non-Parametric Memory, basically regular vector db
	- The `Write` operation simply appends the case (state, action, reward). 
	- `Read`: standard Top-K semantic similarity (cosine) between the current state embedding and past states using a frozen text encoder (e.g., SimCSE).

![](assets/Pasted%20image%2020251009135400.png)
![](assets/Pasted%20image%2020251009135511.png)
![](assets/Pasted%20image%2020251009135551.png)

### Retrieval k
performance improves up to k=4 for retrieval, in line w my expts and USACO

### Component-wise analysis
![](assets/Pasted%20image%2020251010153937.png)
DeepResearcher: identified data contamination (Shumailov et al., 2024), evidenced by a noticeable drop in both F1 and PM when moving from the offline executor to the online executor without planning (DeepResearcher: −18.0 F1 / −2.1 PM).

"... aligns with broader findings in the field (Sun et al., 2022, Yu et al., 2022, Zhou et al., 2025): simply using external knowledge can sometimes negatively affect the model, while the internal knowledge within the model plays an important role in QA tasks and can even outperform RAG." (for tool use, mainly search to gain semantic knowledge. NOT episodic mem!). Some explanations from other papers:
- When in conflict, model relies on external knowledge, so
- Information extraction failures: Models struggle to correctly extract relevant information from external knowledge
- Contextual interference: Irrelevant or noisy information in retrieved documents distracts the model from the core task
- Format inconsistencies: Retrieved information may be presented in ways that conflict with the model's training patterns
- Smaller models show more pronounced degradation, suggesting that limited capacity exacerbates the challenges of knowledge conflict resolution
- Semantic is still useful! If knowledge not existent in parameters

### Continual learning
- training converges quickly, without performance drops
- "With only about 3k training data, the Case Bank saturates quickly. Each additional iteration, therefore, contains progressively fewer previously unseen (and thus potentially failing) cases."
- Opportunity: Train on other envs, test on current envs for larger case bank and show generalization
- Note: w/o CBR performance improves with iter! I interpret it as 'due to accumulated history of attempts *within* the task'

![](assets/Pasted%20image%2020251010153059.png)
## Discussion & Analysis
### Tool call distribution
GAIA:
![](assets/Pasted%20image%2020251010183002.png)

### Slow thinking doesnt necessarily mean better results

![](assets/Pasted%20image%2020251009121338.png)
- Their explanation: 
	- o3 planner often either 
		- answers directly (skipping plan generation altogether) 
		- produces overly verbose plans 
	- this can mislead the executor with incomplete instructions. 
	- Additionally, in complex multi-step reasoning fields, 
		- slow planner tends to compress solutions into a single, convoluted CoT
			- (Might this be an artefact of openai not releasing CoT for reasoning models so the output tends to be a summarized version of the real CoT?)
		- while the fast planner effectively decomposes problems into manageable sub-tasks.
- They could at least show some actual traces in the paper
