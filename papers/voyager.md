---
date: 2024-03-31
time: 14:51
author: 
title: "Voyager: An Open-Ended Embodied Agent with Large Language Models"
created-date: 2024-03-31
tags: 
paper: https://arxiv.org/pdf/2305.16291.pdf
code: https://github.com/MineDojo/Voyager
zks-type: lit
---
- Builds on top of MineDojo, from the same team
- "continuously improves itself by writing, refining, committing, and retrieving *code* from a skill library"
- " “training” is code execution rather than gradient descent. “Trained model” is a codebase of skills that Voyager iteratively composes, rather than matrices of floats"
# Description of result
- Better exploration: "3.3× more unique items, travels 2.3× longer distances, and unlocks key tech tree milestones up to 15.3× faster than prior methods"
- skill library aids Voyager to solve unseen tasks faster. skills library boosts AutoGPT since code is transferrable. advantage: skills are interpretable, composable, temporally extended and amenable to lifelong learning
- ablation studies clearly suggest that their key components are all important
	- Automatic curriculum crucial for agent's consistent progress, discovered item count drops by 93% if curriculum is replaced by a random one
	- without skills library, agent perfomance plateaus
	- self verification is the most impt among the 3 feedback types, removal leads to 73% drop in discovered item count
	- GPT 4 significantly outperforms GPT 3.5 in code generation, obtains 5.7x more unique items

---
# How it compares to previous work
- baselines: ReAct, Reflexion, AutoGPT. needs to be reinterpreted to be executable in MineDojo

---
# Main strategies used to obtain results
3 key components
## Iterative prompting mechanism
- Write program to achieve a goal, iterate program with:
	- error messages from execution
	- environment feedback: eg “I cannot make an iron chestplate because I need: 7 more iron ingots”
	- self-verification: Use another instance of GPT4 as a critic to evaluate feasibility of program given current state (after code  execution) and task, and provide feedback if task fails
- iterate till self-verification validates task completion, then add skill to the library. 
- if agent gets stuck after 4 rounds of code generation, switch to another task from automatic curriculum
## Skills library
Store ebd of docstring of successful programs in a vector DB to build a skills library. 
- Prompt components
	- code gen guidelines (eg “Your function will be reused for building more complex functions. Therefore, you should make it generic and reusable.”)
	- "Control primitive APIs, and relevant skills retrieved from the skill library"
	- "The generated code from the last round, environment feedback, execution errors, and critique"
	- agent's current state
	- CoT prompting before code gen
- Complex skills are composed of simpler skills
	- Example: `collectBamboo.js` is an async fn of bot (the API) which calls the various bot methods and fns in sequence (`exploreUntil`, `findBlocks`, `blockAt`, `dig`, `pathfinder.goto`)
	- Includes description, ebd used as key in skills library. eg "The function is about collecting 10 bamboo plants. It equips the iron sword and uses the `exploreUntil` function to find 10 bamboo plants within a certain distance. If enough bamboo plants are found, it breaks them using the iron sword and collects the dropped bamboo items by moving to their location. If not enough bamboo plants are found, it returns an error message"
- Refined skill denoted with V2, V3 etc in fn name
	- interesting note: between versions, can have differences like writing '1' in a ver then spelling 'one' in another
## Automatic curriculum
suggests tasks based on current state and agent's skill level, for open ended exploration
- query GPT for a suggestion to solve task, then query skills DB to identify top 5 relevant skills
- prompt also includes
	- directives to encourage diverse behaviors and constraints, eg "... discover as many things as possible ... should not be too hard since i may not have the necessary resources"
	- agent's current state (eg inventory, nearby entities, time, position)
	- previously completed and failed tasks
	- additional context: GPT-3.5 (for cost efficiency) to self-ask qns based on agent's current state and exploration progress, self-answer qns with wiki KB for additional context to GPT4
		- Note: cant seem to find the retrieval from wiki implementation on the github repo.

#### Voyager pseudocode
```python
# agent_state: (inventory, equipment, nearby_blocks, recent_seen_blocks, nearby_entities, seen_chests, biome, time, Health, Hunger, position)
# Instantiate env, curriculum_agent, action_agent, critic_agent, skill_manager, agent_state

while iteration < max_iterations:
    exploration_progress = curriculum_agent.get_exploration_progress(curriculum_agent.get_completed_tasks(), curriculum_agent.get_failed_tasks())
    task = curriculum_agent.propose_next_task(agent_state , exploration_progress)
    # instantiate variables code, env_feedback, execution_errors, critique, success

    for i in range(4):
        skills = skill_manager.retrieve_skills(task, env_feedback)
        code = action_agent.generate_code(task, code, env_feedback, execution_errors, critique, skills)
        agent_state, env_feedback, execution_errors = env.step(code)
        success, critique = critic_agent.check_task_success(task, agent_state)
        if success:
            break
    
    if success:
        skill_manager.add_skill(code)
        curriculum_agent.add_completed_task(task)
    else:
        curriculum_agent.add_failed_task(task)
```


---

# Other

## experimental details:
- APIs are are mix of author-implemented ones with those provided by Mineflayer
- temp 0 for all tasks except automatic curriculum (0.1) for diversity


## limitations and future work
- current model is text only, can be augmented by visual feedback, maybe from human input
- occasionally, self-verification fails
	- sometimes JSON not parsed properly so theres a fn to correct for it
- hallucination as usual. examples: invalid inputs to functions, calling non-existent functions

## My thoughts
- some parts require handcrafting 
	- some APIs are handcrafted by team
	- prompt engineering (which includes knowledge of minecraft)
	- handcrafting of 'resetting placed blocks if failed' setting, useful for building tasks
- Description of all APIs are in context, so primitive library might not scale that well -- can we RAG this? 
- Prompt needs to specify not to write infinite loops or recursive fns -- is there a way to mitigate this from the start?
- skill description is generated by LM and requires some (1-shot) prompting -- another failure mode if description is poor 
- self verification requires some prompt engineering -- is there a way to automatically check some conditions?
- still requires GPT4 for code, reasoning esp task destructuring; GPT3.5's ability is way weaker
- still requires LM to have knowledge about the game (without wiki, which is the current implementation on github)
