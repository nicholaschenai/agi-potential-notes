---
date: 2024-02-27
time: 17:35
author: 
title: "Ghost in the Minecraft: Generally Capable Agents for Open-World Environments via Large Language Models with Text-based Knowledge and Memory"
created-date: 2024-02-27
tags:
  - AI
paper: https://arxiv.org/abs/2305.17144
code: https://github.com/OpenGVLab/GITM
---

# Description of result
- 100% completion rate on minecraft tech tree while prev agents only achieve 30% max
- Efficient: 2 days on 32 CPU (not GPU!) cores, purely GPT-3.5, not GPT-4
- SOTA on `ObtainDiamond` challenge, beating DreamerV3, DEPS, VPT by a huge margin (67.5% vs 20% success rate for obtaining diamonds). Fixed an episode of gameplay to the strictest: 10 minutes (12,000 steps at 20Hz control)
- Observation: Leveraging existing knowledge (GITM) is an advantage over RL agents which learn from scratch. Ties into MineDojo's point of learning from existing sources rather than _tabula rasa_. Also, task destructuring is crucial here -- RL struggles with extremely long horizons, short tasks after destructuring is a simpler subproblem
	- A*, BFS, DFS hardcoded in the action implementation. Not sure if this is a good comparison since the RL agents might need to learn about these techniques from scratch, but then again the point of this paper is to show how existing knowledge can be used rather than learnt
- Ablation studies on goal decomposition, feedback, external info and memory suggests the importance of each of these elements. Max num of LLM queries capped at 30
---
# How it compares to previous work
foo

---
# Main strategies used to obtain results

## Decomposer: via text-based knowledge from internet
- Objective is in a highly structured format: (Object, Count, Material, Tool, Info)
- will need to adjust this to generalize to more tasks (beyond minecraft)
- standard vector db retrieval of most relevant facts for a goal via sentence ebd, then LLM identifies reqd materials, tools and related info
## Structured action space
- Start with 3141 predefined tasks on MineDojo
- use LLM to break down each task into a tree of dependencies
	- Set at depth of 2 (empirical)
- extract action tuple (verb, object, tools, materials) from each sentence of leaf nodes via LM
- structured actions are extracted by selecting frequent actions and merging actions with similar functionalities (full process not detailed. is this part onwards handcrafting?)
- this set (cardinality 9) of structured actions (Name, Arguments, Description) define the action space of the paper
- the actions are then implemented (i assume that the decomposition is to identify which actions should appear, so its a form of data analysis. then, the authors need to write the code for each action)
## Planner
Plans structured actions for each sub goal, records and summarizes successful action lists into text-based memory.
- goal recursively decomposed to sub goals (prereq items etc) until each subgoal has no prerequisites.
- execution sequence planned thru post-order traversal
- Feedback via past history of success and faliures for executed actions
- Planning in detail:
	- `Instruction`: guidelines for LLMs to follow when planning
		- `Action Interface` provides functional descriptions of the structured actions and their parameters; 
		- `Query Illustration` clarifies the structure and meaning of user queries; 
		- `Response Format` requires LLM to return responses in the format of {Explanation, Thought, Action List}           
			- `Explanation` requires LLMs to explain the reason for action failure, 
			- `Thought` requires LLM to use natural language to plan before outputting action sequences as a chain-of-thought (CoT) mechanism
			- `Action List` outputs a list of structured actions to be executed
		- `Interaction Guideline` guides LLMs to correct failed actions based on the feedback message
	- `User Query` provides the specific query to LLMs for a given goal, including 
		- `Goal` represents the objective (object, count...) in text
		- `Feedback` is the feedback information of the abstract interface; 
		- `Reference Plan` provides a common reference plan for the current goal retrieved from the text-based memory.
- Memory: during each game episode, once the goal is achieved, the entire execution action list is stored in memory. 
	- skills consolidation: LLM used to summarize essential actions from multiple plans, which is used as a reference for future plans when a similar goal is encountered. Their implementation: when the num of action sequences reaches _N_ (they use _N_=5), they are popped from the list, summarized and pushed to the front of the list (i.e. consolidate/ generalize _N_ implementations of a skill into 1). If the subgoal has a memory attached to it, the first action sequence is used as the reference plan, and if it succeeds, the new executed action sequence gets pushed to the back of the list
## Interface: execute actions via reading keyboard/ mouse inputs and receiving raw observations, including failure and reason for it


---

# Other
- Overview: "decompose goal into sub-goals, then into structured actions, and finally into keyboard/mouse operations", each stage utilizes LLM (their implementation uses gpt-3.5-turbo)


- Additional details
    - Incremental testing: start with 20 runs, then upping to 50, 100, 200 (max) until the success count is nonzero to report the success rate, in order to limit the API calls etc
    - Additional studies: 
        - adding task decomposition to RL based methods boosts performance, reinforcing the point on long-term horizon weakness
        - "Our proposed GITM makes survival and the nether exploration possible in Minecraft which has never been accomplished by existing agents."
    - Code currently not available


- Thoughts
    - No coding required by LM, more like planning/ tool use/ compositions
    - Needs more study on the structured action set selection. what if the set size changes/ actions are different/ erroneous?