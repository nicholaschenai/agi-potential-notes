---
date: 2024-08-30
time: 08:22
author: 
title: "AriGraph: Learning Knowledge Graph World Models\r with Episodic Memory for LLM Agents"
created-date: 2024-08-30
tags: 
paper: https://arxiv.org/abs/2407.04363
code: https://github.com/AIRI-Institute/AriGraph
zks-type: lit
---
# Description of result
- semantic + episodic construction of KG while exploring env
- "outperforms established methods
such as full-history, summarization, and Retrieval-Augmented Generation in
various tasks, including the cooking challenge from the First TextWorld Problems
competition and novel tasks like house cleaning and puzzle Treasure Hunting"
- also outperforms RL baselines like GATA, LTL-GATA, and EXPLORER
- comparable to humans in terms of scores and speed
- total cost 900 USD
- recent repo includes evals on QA

---
# How it compares to previous work
foo

---
# Main strategies used to obtain results
![](assets/Pasted%20image%2020240830084506.png)
## KG
### definitions
- semantic nodes related by semantic edge of triplets (subj rel obj)
- episodic nodes are obs, episodic "hyperedges" (see their footnote for caveat) connect all episodic n semantic nodes that occur at the same time
### construction
- entities extracted from LLM given obs and plan.
	- entities are accompanied w importance score but not used
- relations extracted from LLM given obs and prev triplets

### maintenance
- when new triplets introduced, entities in them are used to retrieve associated triplets (edges from entities in new triplets)
- associated triplets and new triplets sent to LLM to determine of any triplet needs replacing
- outdated semantic rel edges detected by comparing with ablating the semantic edge. after clearing, new nodes are added
## Retrieval
![](assets/Pasted%20image%2020240830084052.png)

![](assets/Pasted%20image%2020240830085648.png)
- semantic search for triplets via Contriever model. impt as nodes may be similar (eg 'grill' and 'grilling') but are not captured by rigid (eg keyword) search
- episodic search: given triplets, return most relevant episodic vertices

## Decision making
via ReAct

## Planning
"uses content of working memory to create new or update existing plan as a series of task-relevant
sub-goals, each accompanied by a concise description. The planning module also evaluates the
outcomes of actions based on feedback from the environment after each action at step t−1, adjusting
the plan accordingly."

- LLM classify if subgoal requires exploring


---

# Other
## unexplored exit detection
Current implementation requires expert knowledge
![](assets/Pasted%20image%2020240906115744.png)