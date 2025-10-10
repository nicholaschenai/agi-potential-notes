---
date: 2025-07-13
time: 21:07
author:
title: "Cradle: Empowering Foundation Agents Towards General Computer Control"
created-date: 2025-07-13
tags:
paper: https://arxiv.org/abs/2403.03186
code: https://github.com/BAAI-Agents/Cradle
zks-type: lit
---
Framework for general computer control: input screenshots and output low level keyboard and mouse actions, so it is not reliant on existing APIs
## Description of result
- first to enable foundation agents to follow the main storyline and complete 40-minute-long real missions in the complex AAA game Red Dead Redemption 2 (RDR2).
- First to target applications like CapCut (video editing), Meitu (image editing) and Feishu (office collaboration app e.g. messaging, calendar/meetings, approval workflows)
- Main benchmarks:
	- Games
	- Software
	- OSWorld


---
## How it compares to previous work
- ReAct-, Reflexion-, Voyager-like variants
- Voyager is text-only

---
## Main strategies used to obtain results
![](assets/Pasted%20image%2020250715083438.png)

![](assets/Pasted%20image%2020250718173938.png)
### Information Gathering
- perception via
	- vision model (Grounding DINO) for objects
	- SAM
	- template matching
	- VideoSubFinder for text (RDR2)
- visual prompting tricks, like drawing axes and colorful directional bands,

### Reasoning
#### Self-Reflection
- Critic: evaluate whether the last executed action was successfully carried out and whether the task was completed
	- input: sequential key screenshots from last vid obs + prev ctx for action planning and task inference
	- use LMM to reason over it. further analysis if fail
	- Per action validation through CLIP-style similarity scoring between intended/actual outcomes
- other uses of reflection: 
	- inform re-planning of the task
	- understand the factors that led to previous successes, 
	- suggest how to update or improve specific skills


#### Task Inference
- after self reflection, use LMM to estimate the highest priority task to perform and when to stop an ongoing task and start a new one

#### Skill curation
- retrieve code based skills via vec similarity of (code, comments, desc) with task desc
- also decide if need to 
	- update skills, 
	- or generate new ones
		- required to include documentations/comments within generated code
		- check whether   
			- code is valid, 
			- the format of documentation is right, 
			- any skill with the same name already exists
		- if checks pass, store in procedural mem
- can be learnt from
	- game tutorials
	- game manuals
	- self exploration
![](assets/Pasted%20image%2020250718173903.png)
#### Action Planning
- select curated skills from previous and instantiate them into a sequence of executable actions from current task n history info
	- may need to specify necessary parameters e.g. duration/position/target
- actions then fed to executor for interaction with env

### Memory
#### short term
(note: they term it 'short term episodic memory' but ill just call it short term mem)
- screenshots within the recent k=5 interactions in game playing and the corresponding information from other modules, e.g., 
	- screenshot descriptions, 
	- task guidance, 
	- actions,
	- reasoning
#### episodic
for interactions
- helps agent avoid redundant interactions
- key screenshots from each video observation, and everything useful outputted by LMMs and advanced tools, e.g., textual and visual information, actions, tasks, and reasoning from each module. 
##### recurrent summarization
- periodic text summarization of experiences in game playing, including 
	- ongoing task, 
	- past entities that the player met,
	- past behaviors of the player and NPCs.
- updates summary by using GPT 4o to summarize the combination of
	- the summarization before the current screenshot and 
	- recent screenshots with corresponding descriptions,
	- by organizing the tasks, entities, and behaviors in the time order with sentence number restriction.

#### procedural 
for learned code based skills
- can have params such as duration/position/speed
- ==can be updated (how?)==
- code + comments + description

### Putting it all together
thru these modules, the input video is processed in stages: 
- first into reasoning, 
- then into semantic skills and actions,
- finally into low-level keyboard and mouse operations

---

## Other
### Limitations
- stardew valley poor recognition for basic objects, also similar to pokemon.
	- hypothesis: might be due to scale; LMMs are likely to be trained on natural images and is conditioned to use that scale, so if games present the objects at a different scale, LMMs will struggle
- io timing: if presses r too fast, might nt register in game. Can use task completion feedback to mitigate
- change in low level context. Sometimes the same key can do different things in diff contexts, and the memory might not be updated or know how to tell the diff. 
	- could be mitigated with operator conditions
- confusion between past and present frames
#### Visual
- limitation in GPT 4V spatial recognition sometimes hindered precise ctrl in RDR2
- struggles with domain specific concepts such as icons in RDR2

### RDR2 specifics
- Task inference: GPT-4o also outputs if task is long or short horizon. After 3 interactions w newly generated task, it will return to the last long horizon task in the stack (so this is natural task decomposition)
	- has similarities to chunking in [soar](soar.md) of having subgoals
- action planning: MPC style plan multiple steps ahead but only execute the next one
- procedural mem: requires some initialized 
	- primitive skills as they are not acquirable from tutorial 
	- and composite skills as doing multiple primitive actions can be expensive, or there is some timing component which is too fast for LMMs
- need to pause game in between LMM calls

### Stardew valley specifics
- procedural mem: requires initial handcrafted procedural skills
	- partly cos requires fine-grained control and gpt-4o struggles at pixel level precision
- self reflection allows it to recognize it got stuck (moving in one direction didnt change things) and decide to move in another direction

### Cities: Skyline specifics
- Need to divide screenshot into grids as visual prompting trick

### Software use specifics
- agent can get confused by dialog popups, so it is mitigated via mandatory reasoning rules
	- (e.g. assume task already completed when it needs to confirm)
	- autosuggestion (e.g. in email address) is confused for text already typed

---

## Overall thoughts
- Still need env-specific customization for perception, self-reflection, memories