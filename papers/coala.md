---
date: 2024-03-05
time: 18:46
author: 
title: Cognitive Architectures for Language Agents
created-date: 2024-03-05
tags: 
paper: https://arxiv.org/abs/2309.02427
code:
---
[[Repo (lit review)](https://github.com/ysymyth/awesome-language-agents)]
# Description of result
Concept paper which proposes Cognitive Architectures for Language Agents (CoALA), a conceptual framework to "systematize diverse methods for LLM-based reasoning, grounding, learning, and decision making as instantiations of language agents in the framework"
- Use it to retrospectively organize and understand existing architectures and works
- Also to highlight gaps and propose actionable directions

## Key arguments
- Agents used to require handcrafted rules or RL, making generalization to new envs challenging
- LLMs have decent priors from pretraining and are extremely flexible, operating over arbitrary text. This complements agents of the past and reduces requirements for human annotations or trial and error learning
## Actionable insights
### Require Modular Agents
Standardizing code and terminology
- allow for conceptual comparisons across works and to think about useful abstractions (eg memory, action and agent classes)
- standardize experience instead of interacting with a mix of agents developed by individual teams
- Suggestion: use code sparingly to implement generic algos that complement LLM limitations (eg tree search to mitigate myopic autoregressive generation)

### Agent design: Beyond simple reasoning
- can use framework as a guide to think about implementations of components. take a personalized retail assistant for example: external actions would be dialogue or returning search results to user
- Determine memory modules: retail assistant example
	- semantic: items for sale
	- episodic: customer's purchase and interaction history
	- procedural: functions to interact with datastores
	- working: tracking dialogue state
- Define action space: retail assistant example
	- read and write to episodic to store and retrieve new interactions
	- read-only for semantic and procedural
- Define decision procedure
	- tradeoff b/w performance and generalization (eg more complex procedure are better suited for specific problems like Voyager in Minecraft while simpler ones are more domain agnostic n generalizeable like ReAct)
	- Retail assistant example: retrieve user interactions from episodic for priors of their search intent, reasoning about whether the search results will satisfy intent. Defer learning to end of interaction, summarize episode prior to storing in episodic mem
### Structured reasoning beyond prompt engineering
- Use prompting frameworks to define higher level reasoning  steps and prompt crafting efforts
	- Use structured output solutions to update working mem variables
	- Define, build good working mem modules esp when needed to integrate with large-scale code infra
- Reasoning usecases can inform and reshape LLM training
	- LLMs are trained n optimized for NLP tasks by default, but agent applications require new modes of reasoning (eg self-evaluation)
	- LLMs trained or finetuned towards LLM reasoning will more likely be the backbone of future agents
		- comment: as of writing (Oct 2024), o1 has spurred an interest in the reasoning end of training LLMs
- (from v1) systematic 'reasoning' actions that update working memory variables"

### Long-term memory: thinking beyond retrieval augmentation: 
agents can read n write self-generated content: new possibilities for efficient lifelong learning

- combine existing human knowledge with self-discovered (and potentially self maintained) exp, knowledge and skills. 
	- example: coding agent starts off with human knowledge (semantic), problem solutions and test records (episodic), reflection n summaries on top of these exp (semantic), gradually expanding code lib that stores useful methods eg Quicksort (procedural). A bit like bootstrapping AlphaGo off existing games then enhancing from self play?
		- see USACO-bench which aims to facilitate this
- integrate retrieval and reasoning to ground planning: recent works suggest adaptive mechanisms of interleaving mem search and forward simulation

### Learning: thinking beyond in-context learning or finetuning
(read this entire paragraph!)
- meta-learning by modifying agent code for more efficient learning
	- learning better retrieval procedures could enable agents to make better use of experience, esp when connecting to new scenarios
	- recent expansion based techniques can allow agents to reason 'in what situations would this knowledge be useful?', and append the reasoning results to the knowledge to help later connect the knowledge to new situations / facilitate recall
	- can help go beyond human-written code but understudied due to difficulty and risk
	- (v1 phrasing) Learning new learning and decision procedures may be necessary to empower agents to go beyond the limitations of human-provided code
- new forms of (un)learning
	- explore smaller models for specific reasoning subtasks
	- unlearning!
	- combine multiple forms of learning

### Action space: thinking beyond external tools or actions.
- impt to balance tradeoff for size of action space: larger means more expressivity/ capability, but harder decision making
- Safety! 
	- some actions are inherently riskier 
		- eg learning actions that modify internal memories
		- grounding actions like shell commands that deal with filesystem
	- safety measures currently limited to task-specific heuristics 
	- as agents are increasingly integrated into systems, "it will be necessary to clearly specify and ablate the agent's action space for worst-case scenario prediction and prevention"

### Decision making: thinking beyond action generation
most works are still confined to proposing (or directly generating) a single action. "Present agents have just scratched the surface of more deliberate, propose-evaluate-select decision-making procedures" (v1: combine with grounding and LT memory)
- mixing language based reasoning and code-based planning
	- existing works either plan in NL or translate from NL to structured world models
	- future works could integrate these, just like how Soar has a physics simulator. agents might write code to simulate consequences of their plans
- extend deliberative reasoning to real-world settings (current works eval on simple tasks)
- metareasoning to improve efficiency
	- how to balance usage of LLM (due to high computation) and utility of improved plan? current works specify a search depth or budget
	- humans adaptively allocate computation, and future would could estimate utility of planning, modifying decision procedure accordingly (eg finetuning, routing to sub procedures like how ReAct routes to ToT) or direct decision procedure update
	- v1: potentially via smaller task-specific models for proposal or evaluation
- calibration and alignment: solving over-confidence and miscalibration, misalignment with human values or bias, hallucinations in self-evaluation, and lack of human-in-the-loop mechanisms in face of uncertainties"

---
# How it compares to previous work
Key contributions over previous CA
- Inclusion of LLMs leads to the addition of 'reasoning' actions, which can flexibly produce new knowledge and heuristics, replacing handcrafted rules in previous CAs
- makes text the de facto internal representation
- suggests vision-language models can simplify grounding by translating perceptual data to text

---
# Main strategies used to obtain results
![](assets/Pasted%20image%2020241024131921.png)

## Memory
### Working memory

maintains active and readily available information as symbolic variables for the current decision cycle (scratch memory). eg perceptual inputs from grounding, knowledge generated by reasoning or retrieving from long term memory, other info from previous decision cycle.

### episodic memory
exp from earlier decision cycles, eg training data, history event flows, past game trajectories. Can be retrieved into working memory to support reasoning. can write new exp frm working to episoding memory as a form of learning
### semantic memory
agent's knowledge about the world and itself. (eg initialized from external db, RAG). past examples tend to employ read-only semantic memory but  lang agents may write new knowledge from LLM reasoning into semantic memory as a form of learning.
### procedural memory 
- implicit knowledge in LLM weights, explicit knowledge in agent's code.
- explicit knowledge
	- procedures that implement actions
		- LLM accessed via reasoning action
		- code-based procedures retrieved
	- procedures that implement decision making
- must be initialized properly by designer, compared to episodic or semantic memory which can be initially empty
- possible to write to procedural memory but riskier than writing to semantic or episodic memory as this can introduce bugs or safety concerns (akin to Voyager writing wrong code in skills library)
## Actions
external actions: interact with external envs thru grounding
### Internal actions: interact with internal memories
- reasoning: update short term working memory w LLM. read from AND write to working memory. to summarize n distill insights. support learning (by writing results into LT memory) or decision making (results as additional context for subsequent LLM calls)
- retrieval: read from long term memory into working memory. various implementations eg rule based, sparse, dense retrieval
	- eg Voyager uses dense retreival for retrieving skills frm lib, gen agents retrieves relevant events from episodic mem via recency (rule-based) + importance (reasoning-based) and relevance (emb/ dense). DocPrompting uses lib docs, i.e. dense retrieval frm semantic mem
- learning: write to long term memory
	- update episodic memory w experience (eg replay buffer in RL?)
	- Updating semantic memory with knowledge: use LLM to reason about raw experiences and store these into semantic memory, like gen agents reflection
	- Updating LLM parameters (procedural memory). eg via supervised, imitation learning. RL. RLHF, RLAIF. distillation
	- Updating agent code (procedural memory).
		- updating reasoning (eg prompt templates, prompt update)
		- updating grounding (eg voyager's curriculum library)
		- updating retrieval (query / document expansion, retrieval distillation to learn better retrieval over time)
		- updating learning or decision-making: authors not aware of related works tho this direction is possible, but note alignment issues
	- language agents can select frm diversity of learning procedures, allowing them to learn rapidly (potentially faster than fixed way of learning like updating model params) by storing task-relevant language, and leverage multiple forms of learning to compound self-improvement
	- authors emphasize that most work are on additive techniques for memory, and works that are subtractive or selective (eg modifying memories) are understudied
## Choose actions via decision making
retrieval + reasoning to plan by proposing + evaluating candidate learning or grounding actions. choose n execute the best action, observe, repeat cycle

- planning
	- proposal: generate action candidates via reasoning and retrieval. possibilities: all actions if action space is small, rule based selection, GOFAI planning eg PDDL
	- eval: via heuristic rules, LLM (perpelxity) values, learned values, LLM reasoning or some combination. "Particularly, LLM reasoning can help evaluate actions by internally simulating their grounding feedback from the external world". "identifying defects, and proposing modifications that address those defects" -- some code models do this, see CodeRL
	- selection: decide via values or decide to re-iterate to some proposal or evaluate sub-state
- execution
- recent trend to "investigate more complex decision making by proposing more than one action, and with iterative proposal and evaluation", such as tree of thoughts (iteratively propose and evaluate thoughts in a systematic tree search (DFS/BFS) to return a final solution), RAP (generates partial thoughts and maintains them in a tree structure using MCTS)

## Case studies
see table 2 for comparison across existing examples

selective table

| Paper    | LT memory | ext grounding | internal actions | decision making |
| -------- | ------- | --- | ---| --- |
| ReAct | - | digital | reason | propose|
| Voyager | procedural | digital |  reason/ retrieve/ learn | propose |
| Generative Agents |  episodic/semantic | digital/agent |  reason/ retrieve/ learn | propose |

---

# Other

## Discussion
- LLMs vs VLMs: integrated repr or vision model for text, then full text reasoning?
	- integrated approach allows for more human-like behaviors eg seeing a webpage as it is vs LLMs requiring HTML
	- however coupling perception and reasoning makes agents more domain specific and difficult to update
- Why treat physical vs digital envs differently?
	- Digital envs can allow for sequential (eg via resets) and parallel trials, making exploration more scalable, so the decision procedures could be different from cognitive inspired ones
- Planning vs. execution: how much should agents plan? "balancing the cost of planning against the utility of the resulting improved plan" (ref [xkcd comic](https://xkcd.com/1445/) of plan A vs plan B vs thinking about choosing plan A or B)
- Learning vs. acting: how should agents continuously and autonomously learn? (explore-exploit for learning)
- LLMs vs. code: where should agents rely on each? "CoALA thus suggests that good design uses agent code primarily to implement classic, generic planning algorithms - and relies heavily on the LLM for action proposal and evaluation."
- How would agent design change with more powerful LLMs?
	- hard to predict
	- CoALA, cognitive science could still help organize tasks where current agents succeed or fail, and highlight areas where code-based procedures could complement the LLM for the task
	- Even if future LLMs implicitly perform all functions / components in CoALA, the conceptual framework can be a guide to discover and interpret these implicit circuits
	- LM and agent design could co-evolve (one shapes the other)
## Introduction
- Draws parallels with these ideas: _production systems_ and _cognitive architectures_ (finally!)
	- "Production systems generate a set of outcomes by iteratively applying rules"
- Fig 1, the different types of LLMs and going up the hierarchy of complexity
    - 1a. Simplest type: LLM as generic input-output fn
    - 1b. LLM as an agent, in a typical RL fashion interacting with the env by producing an action based on observation
    - 1c. Cognitive LM, same setup as 1b but the agent internally has various components like memory, retrieval learning and reasoning (i.e. RAG style)
![](assets/Pasted%20image%2020241024131605.png)
## Background: From Strings to Symbolic AGI
- "Large production systems connected to external sensors, actuators, and knowledge bases required correspondingly sophisticated control flow."
- "AI researchers defined 'cognitive architectures' that mimicked human cognition - explicitly instantiating processes such as perception, memory, planning (Adams et al., 2012) to achieve flexible, rational, real-time behaviors"
- Canonical example: Soar (fig 2A). My summary [here](soar.md)
- CAs have become less popular in the community due to the limitation to domains that can be prescribed by logical predicates, and require pre-specified rules to function
- LLMs appear well-posed to meet these challenges due to its flexibility (can operate over arbitrary text) and reduced user specification requirements (LLMs learn a distribution over productions via pretraining)
- Researchers have begun to use LLMs within CAs for their implicit world knowledge and to augment traditional symbolic approaches (see citation)
- In this paper, the authors import principles from CA to guide the design of LLM based agents
## Connections b/w LMs and production systems
- section draws analogies between the two. 
- Fig 3 shows the evolution of LMs to agents. 
	- 3a: basic LLM call. 
	- 3b: prompt chaining uses pre defined sequences of LLM calls. 
	- 3c: language agents use an interactive feedback loop with env.

## Soar overview
- Decision making: Fig 2B. proposal and evaluation phase: a set of productions is used to generate and rank a candidate set of possible actions (see operators vs rules footnote)