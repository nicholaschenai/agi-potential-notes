---
date: 2024-02-18
time: 12:47
author: Zhiyong Wu and Chengcheng Han and Zichen Ding and Zhenmin Weng and Zhoumianze Liu and Shunyu Yao and Tao Yu and Lingpeng Kong
title: "OS-Copilot: Towards Generalist Computer Agents with Self-Improvement"
created-date: 2024-02-18
tags:
  - AI
  - autonomous-agents
paper: https://arxiv.org/abs/2402.07456
code: https://github.com/OS-Copilot/FRIDAY
---
# Description of result
Learning here refers to self-learning (curriculum)
## Benchmark: GAIA (Mialon et al., 2023b), a general AI assistants benchmark. 
Recall definition of levels:
- Level 1 questions generally require no tools, or at most one tool but no more than 5 steps.
- Level 2 question generally involve more steps, roughly between 5 and 10 and combining different tools is needed.
- Level 3 are questions for a near perfect general assistant, requiring to take arbitrarily long sequences of actions, use any number of tools, and access to the world in general.


![](assets/Pasted%20image%2020240218132035.png)

### FRIDAY vs others on GAIA, facet by domain
![](assets/Pasted%20image%2020240218132415.png)

## Benchmark: SheetCopilot-20 (Li et al., 2023)
Shows how self-learning (curriculum) boosts scores from zero to SOTA

![](assets/Pasted%20image%2020240218132211.png)

## Other
- Demos of Powerpoint and ReactJS skills
- OS-Copilot framework to build your own agent

---
# How it compares to previous work
- FRIDAY is basically Voyager++ with semantic memory and a wider action space / environment
	- Voyager didnt decompose task during training tho fn is in repo
- Most other agents (eg AutoGPT) do not have a curriculum manager like FRIDAY or Voyager

---
# Main strategies used to obtain results
GPT4-turbo-1106 in FRIDAY
## Planner
- Reason over user request, decompose complex tasks into subtasks and name, task desc, dependencies
- Existing planners are sequential, they use DAG based planner to parallelize tasks by constructing dependency tree via topo sort (code still seems serial though they did build dependency tree. couldnt find the parallel part in the repo)

### Action space
- call API
- write code
- perform QA to reason over prev tasks

## Configurator

Takes subtask, configures it to help actor complete subtask. 

### Declarative mem
Subcategory of long-term memory for storing facts and events.
- User Profile. It records user’s preference regarding conversation style, tool-use habit, music/video preference, etc. Accurate user profiling is critical for personalized problem-solving and recommendation. Although it has been widely studied in the recommendation area, personalized language agents are rarely explored. ==The User Profile module is a conceptual design at the current stage due to the lack of corresponding benchmarks.==
- Semantic Knowledge. It stores agents’ past trajectories or knowledge they acquired from the Internet, users, and OS (e.g., system version and current working directory). This module is crucial for agents to act correctly based on the current environment state and learn from past experiences.
	- Planning Module's `action_node` which stores outcomes of all actions. Each action takes in  `pre_tasks_info`, the subtask n execution outcome of dependency (previous) tasks. 

### Procedural mem
- Skill library, tool repository (both API n python file). 
- If coding agent is needed, when task is rated by critic as at least 8/10 success, code is stored just like voyager

### Working mem
na

## Actor

- executor 
	- interestingly contains a Fake Parameter Identification task!
- critic 
	- 1) Determining whether the current sub-task is completed through the analysis of execution results and the environmental state. (like voyager)
	- (2) In the event of failed completion, offering a comprehensive error analysis and providing suggestions for correction of tools or actions (how tools are called). 
	- (3) Assessing the necessity for restructuring subtasks, including the addition of new subtasks or modifications to the content and dependencies of existing subtasks.
	- if fail, reattempt till 3 times, like Voyager

## Self directed learning
Curriculum agent like Voyager. Code missing from github

---
# Misc

## Constraints / challenges 
- require vision cos code + lang alone infeasible due to closed source commercial software
- evaluating task success can be hard
- safety cos OS interaction

## Motivation for OS-Copilot
Challenge: Heterogeneity inherent in the OS ecosystem. Unified interface is required for agents to seamlessly interact with the operating system, be it through code, keyboard and mouse inputs, or APIs.

## Profiling speed
![](assets/Pasted%20image%2020240218132401.png)

## Future work
- FRIDAY excels at processing coding and file I/O, while showing weaknesses in web browsing and handling multi-modality. FRIDAY currently only supports retrieving information from a website and can not perform actions within the web, such as clicks.
- enhance FRIDAY’s web browsing capability by incorporating recent advancements in web agents (Deng et al., 2023). 
- Challenges in multi-modal tasks arise from the need for downloading and deploying local models (e.g., speech-to-text). We believe that incorporating recent efforts like HuggingfaceGPT (Shen et al., 2023) can substantially enhance the FRIDAY’s multi-modal capabilities.