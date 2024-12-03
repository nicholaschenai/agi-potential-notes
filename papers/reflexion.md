---
date: 2024-12-02
time: 17:56
author: 
title: "Reflexion: Language Agents with Verbal Reinforcement Learning"
created-date: 2024-12-02
tags: 
paper: https://arxiv.org/abs/2303.11366
code: https://github.com/noahshinn024/reflexion
zks-type: lit
---
# Description of result
foo

---
# How it compares to previous work
foo

---
# Main strategies used to obtain results
![](assets/Pasted%20image%2020241202175927.png)

## Components
### Actor
### Evaluator
### Self-reflection
### Memory
#### Long term memory
- Limit to sliding window with max capacity, but encourage future work to use more advanced structures like vector / SQL db

## Coding workflow
nonzero temperature
- while not solved and $k$ not reached (for pass@$k$)
	- generate internal tests
	- generate first implementation. (direct prompt)
	- execute. if success, evaluate, exit early
	- else, for max_iters,
		- generate self reflection. message thread:
			- sys prompt
			- user (in 1 msg)
				- example of implementation + unit test result + reflection
				- previous function implementation
				- unit test result
				- hint to self reflect
		- generate implementation. message thread:
			- system prompt
			- example from user
			- assistant's previous implementation
			- user: unit test results of prev implementation
			- assistant's self reflection
			- user: request for improved implementation
		- execute. if success, evaluate, break

---

## Other
- see [usaco-bench](../../ai-for-code/papers/usaco-bench.md) for enhanced versions of reflexion