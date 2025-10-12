---
date: 2025-10-12
time: 15:29
author:
title: "MemoryBank: Enhancing Large Language Models with Long-Term Memory"
created-date: 2025-10-12
tags:
paper:
code: https://github.com/zhongwanjun/MemoryBank-SiliconFriend
zks-type: lit
is_public: true
---
Ebbinghaus forgetting curve on chat agent [ebbinghaus-forgetting-curve](../concepts/ebbinghaus-forgetting-curve.md)

## Description of result
- eval on simulated conversations 
	- across 15 virtual users and 10 days of convos, 450 topics
	- 194 memory probing qns (split equally between English n Chinese)
![](assets/Pasted%20image%2020251012163907.png)

---
## How it compares to previous work


---
## Main strategies used to obtain results
![](assets/Pasted%20image%2020251012155052.png)
- retrieval via ebd (FAISS), DPR style
### mem dynamics
- Reinforcement: increase memory strength by 1 during retrieval
- Forgetting: Use exponential decay with time of last interaction (addition, retrieval) to forget memories permanently
### mem storage
- hierarchical event summary (summarize daily events/ global events)
- summarize user personality traits and emotions per dialog
- prompt for summary of personality over timeseries of above per-dialog summaries
### ft
- ft via LoRA on a dataset of 38k psychological dialogues (not provided in repo)