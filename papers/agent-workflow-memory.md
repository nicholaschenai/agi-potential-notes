---
date: 2024-10-24
time: 09:28
author: Zora Zhiruo Wang, Jiayuan Mao, Daniel Fried, Graham Neubig
title: AGENT WORKFLOW MEMORY
created-date: 2024-10-24
tags: 
paper: 
code: https://github.com/zorazrw/agent-workflow-memory
zks-type: lit
---
Learning of workflows: reusable template trajectories (procedural learning)
# Description of result
- 24.6% relative improvement on Mind2Web success rate
- 51.1% relative improvement on WebArena success rate
- Generalizes cross-task, website and domain evals, "surpassing baselines from 8.9 to 14.0 absolute points as train-test task distribution gaps widen"

---
# How it compares to previous work


![](assets/Pasted%20image%2020241024142448.png)

![](assets/Pasted%20image%2020241024142531.png)

![](assets/Pasted%20image%2020241024142608.png)
![](assets/Pasted%20image%2020241024142620.png)


---
# Main strategies used to obtain results
![](assets/Pasted%20image%2020241024142226.png)
![](assets/Pasted%20image%2020241024142258.png)![](assets/Pasted%20image%2020241024142310.png)

## LM-based Workflow induction
- enhance generality by abstracting out example-specific contexts (eg replacing product names with placeholder `{product-name}`), similar to how [soar](soar.md) replaces specifics with variables when it learns during chunking
- All induced workflows in prompt
### Offline scenario
Specific to WebArena

Loop:
- Inference to generate trajectories
- From trajectories, extract thought and action (so no observation), group them by WebArena `template_id` (tasks are created from templates)
- For each `template_id`, sample `n` samples
- Entire thing is passed to LLM to create a set of workflows

comment: reliant on templates / task attributes for proper data balance
### Online scenario
- start from default memory
- given `t`-th test instruction
- agent generates action trajectories
- use LM-based evaluation to classify success or fail from `t`-th experience (test instruction and generated action traj at that time)
- if predicted as success, transform into workflow


---

# Other

![](assets/Pasted%20image%2020241024142514.png)


Compositional buildup of procedural skills, as seen in [voyager](voyager.md)
![](assets/Pasted%20image%2020241024142549.png)

## Rule based workflow induction
- experience deduplication
- invalid action filtering
- mixed and marginal changes over using LM