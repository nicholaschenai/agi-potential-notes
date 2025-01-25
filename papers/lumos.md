---
date: 2024-08-03
time: 19:54
author: Da Yin, Faeze Brahman, Abhilasha Ravichander, Khyathi Chandu, Kai-Wei Chang, Yejin Choi, Bill Yuchen Lin
title: "Agent Lumos: Unified and Modular Training for Open-Source Language Agents"
created-date: 2024-08-03
tags: 
paper: https://arxiv.org/abs/2311.05657
code: 
zks-type: lit
---
==DRAFT MODE==

"open-source agent learning framework that trains LLMs with unified data, aimed at unifying complex interactive tasks and enhancing generalization on unseen tasks with new environments and actions"
# Description of result
- their 13B can surpass other larger models and even GPT 4 sometimes on various tasks
- performs well on unseen tasks, suggesting generalization
![](assets/Pasted%20image%2020240803200814.png)

---
# How it compares to previous work
foo

---
# Main strategies used to obtain results
![](assets/Pasted%20image%2020240803200654.png)

## Planning
decompose into high level subgoals via LM

## Grounding

Translate subgoals into actions (see table 8) via LM

![](assets/Pasted%20image%2020240803200743.png)

## Execution
APIs that perform the grounding actions

---

# Other
## Discussion
- From Tab. 3, both LUMOS-I and LUMOS-O outperform CoT Training3. They also exceed the integrated formulation based on a single module to operate planning and grounding. 
	- ==highlights the importance of disentangling subgoal planning and action grounding skills during the agent training.==
- **Would adopting low-level subgoals be more effective than using high-level subgoals**? No.