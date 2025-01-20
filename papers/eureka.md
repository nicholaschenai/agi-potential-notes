---
date: 2024-08-04
time: 09:03
author: 
title: "Eureka: Human-Level Reward Design via Coding Large Language Models"
created-date: 2024-08-04
tags: 
paper: https://arxiv.org/abs/2310.12931
code: https://github.com/eureka-research/Eureka
zks-type: lit
site: https://eureka-research.github.io
---
Iterative process where LLM designs reward functions, evaluates in RL env and reflects to design better reward functions
## Description of result
- benchmark: 29 RL environments across IsaacGym and Dexterity
- EUREKA outperforms human experts on 83% of the tasks, leading to an average normalized improvement of 52%. (main model: GPT-4-0314)
- Solves dexterous manipulation tasks that were previously not feasible by manual reward engineering (eg shadow hand pen spinning)
- Can also incorporate human feedback, combining Eureka with human feedback performs the best
- GPT 3.5: usually matches humans and occasional beat

---
## How it compares to previous work
Language to rewards (Yu et. al. 2023)

---
## Main strategies used to obtain results
![](assets/Pasted%20image%2020240804090510.png)

![](assets/Pasted%20image%2020240927165944.png)

In simpler terms:

training
- for $N$ (=5) iterations
	- prompt LLM $K$ (=16) times to get $K$ reward functions (temp=1) (reward function might have errors so multiple samples increases the chances of at least one executable reward fn). prompt inputs:
		- reward fn from previous iter best (Markov)
		- reflection over reward
		- env code (excluding reward code) / state info -- potential limitation
			- has automatic scripts to extract impt parts
		- mutation prompt

	- in parallel, run $K$ trials and pick the best
	- based on best reward fn and results, LLM reflection to try and improve reward model
	- update global best

eval
- For _ iterations
	- take global best reward fn and train model
- average best score across checkpoints


---

## Other
- Trained checkpoints and WSL instructions [here](https://github.com/nicholaschenai/eureka_exploration)