---
date: 2025-07-19
time: 15:11
author: Di Wu, Hongwei Wang, Wenhao Yu, Yuwei Zhang, Kai-Wei Chang, Dong Yu
title: "LongMemEval: Benchmarking Chat Assistants on Long-Term Interactive Memory"
created-date: 2025-07-19
tags: 
paper: https://arxiv.org/abs/2410.10813v2
code: https://github.com/xiaowu0162/LongMemEval
zks-type: lit
---
2 main contributions
- LongMemEval benchmark for long context QA
- Strategy to score well on it
## Description of result
### Benchmark

![](assets/Pasted%20image%2020250719151606.png)


- two standard settings:
	- LONGMEMEVAL_S (∼115k tokens/question) 
	- LONGMEMEVAL_M (500 sessions, ∼1.5M tokens).
- Tests these capabilities
	- Info extraction
	- Multi session reasoning
	- Knowledge update
	- Temporal reasoning
	- Abstaining on unanswerable qns
#### Eval
##### metrics
- LLM as judge for QA quality, 97% agreement with human experts
- Recall@k for memory recall
##### systems
- commercial: ChatGPT and Coze
- Long context LLMs
	- Results show that LLMs struggle when context becomes long (lost in the middle effects)

![](assets/Pasted%20image%2020250719153935.png)

![](assets/Pasted%20image%2020250719162415.png)
### Technique
new technique to manage memory for QA

#### Decomposing session improves RAG performance
![](assets/Pasted%20image%2020250719155304.png)
- replacing sessions/rounds w extracted summaries or facts negatively impacts QA performace due to info loss, except with multi-session reasoning questions
	- "We hypothesize this is because fact decomposition extracts the same type of information across all sessions in a more uniform and simplified format, aiding retrieval and reading"
#### Multi-key indexing improves retrieval and RAG
![](assets/Pasted%20image%2020250719155657.png)
- Condensed forms (summaries, keyphrases, user facts) as key does not enhance recall
- appending them to the main text however helps. Multi-pathway retrieval
#### Time-aware query expansion improves temporal reasoning
![](assets/Pasted%20image%2020250719155916.png)
- values indexed with timestamps, then during time-sensitive queries, filter by time range
- improves recall by 11.3% when using rounds as value, 6.8% when using sessions as value
- effectiveness depends on strong LLM to accurately infer time ranges from queries
	- Llama 8b struggles to generate accurate time ranges, even with ICL

#### Chain-of-note and structured format improves reading
![](assets/Pasted%20image%2020250719155928.png)
- even with perfect retrieval, how these memories are read can affect performance
- Use chain-of-note which first extracts info from each mem chunk and reason based on these notes

#### Additional models
![](assets/Pasted%20image%2020250719162813.png)
consistent with earlier findings, even with smaller models

#### Retriever ablations
![](assets/Pasted%20image%2020250719163120.png)
Key observations
- Dense ebd better than BM25 (sparse)
- key expansion w user fact consistently beats value as key
- key expansion w summary and keyphrases improves performance in some settings, but facts give greastest performance gain

#### Post-retrieval rank merging
![](assets/Pasted%20image%2020250719163329.png)
- currently, use extracted metadata to augment main text so can retrieve in multiple ways
- alternate strat: create separate key with retrieved info and place it in parallel as original keys "rank merging"
- rank merging underperforms the original method
	- hypothesis: it increases the index size significantly, causing poorer retrieval


---
## How it compares to previous work
![](assets/Pasted%20image%2020250719151955.png)

---
## Main strategies used to obtain results
![](assets/Pasted%20image%2020250719154135.png)

![](assets/Pasted%20image%2020250719154306.png)


### Key expansion
- summaries, keyphrases, user facts and timestamped events are extracted from values to augment index
	- like a souped up version of anthropic's contextual retrieval

### Query
- time-aware indexing -- values indexed with timestamped events, retrieval restricted to items within relevant time range

---

## Other
### Error analysis
![](assets/Pasted%20image%2020250719163730.png)
- reasonable amount of correct retrieval wrong generation -- need better reading strats
- "we observe that for the reader LLM to generate a correct answer, performing correct retrieval is necessary in ∼90% of the time" 
- "We observe that the rest 10% instances are mostly of the question type knowledge-update and the retriever was only able to identify the updated knowledge but failed to retrieved the previous information before update"

## Realism of dataset
Note that this dataset is more of a needle-in-haystack style rather than realistic trajectories of a person. 

### LM generated ans
During the construction of the LONGMEMEVAL dataset, the designers compiled chat histories in a way that prioritises length configurability and challenging memory recall across diverse information, rather than ensuring temporal coherence of topics across all chat sessions.

Here's how the chat histories are constructed, which addresses your query:

- **Compilation Process**: For each question, LONGMEMEVAL compiles a coherent user-AI chat history. This involves sampling a number of **unrelated user-AI chat sessions** and randomly inserting the specific "evidence sessions" (those containing the information needed to answer the question) into the middle.
- **Sources of Irrelevant Sessions**: The "irrelevant" or "non-evidence" sessions are drawn from two main sources:
    - **Self-chat sessions simulated based on other non-conflicting attributes**. This means if a question is about 'musical instruments', the irrelevant sessions might be about 'travel' or 'shopping', ensuring minimal direct conflict but also minimal topical coherence.
    - **Publicly released user-AI style chat data** such as ShareGPT and UltraChat.
- **Randomisation and Shuffling**: Sessions are **randomly sampled** from these sources and then **shuffled** together with the question's evidence sessions. Timestamps are then randomly assigned to sessions following their order in the history, unless evidence sessions have pre-defined timestamps.
- **Design Goal**: The design aims to create "extensible realistic chat histories with **minimal conflicts**". This structure challenges systems to retrieve and synthesise information from multiple extended evidence sessions embedded within a long, diverse context, similar to a "needle-in-a-haystack" test.

Therefore, while the benchmark includes "knowledge updates" and "temporal reasoning" abilities that require models to track **changes in user information over time** (e.g., a user buying a new item or changing their work hours), the overall structure of the compiled history, particularly concerning the non-evidence sessions, **does not guarantee that nearby chat sessions will have similar or continuous topics**. Instead, the focus is on embedding crucial information within a long, potentially topically disparate, interaction history to challenge the long-term memory capabilities of chat assistants.