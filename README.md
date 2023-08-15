# Brief notes on AGI potential
Really quick notes for my own reference so pardon the untidyness. Will occasionally copy paste directly from the papers!

Notes focused on papers which in my opinion are delivering interesting algorithmic insights/ advancements towards AGI.

---
# Personal Observations

- Decomposing tasks beats end-to-end deep learning
- Having a memory component separate from the foundation models is important 
---
# Benchmarks
- [MineDojo](https://minedojo.org/)

---

# Paper Notes

### Voyager
[[Code](https://github.com/MineDojo/Voyager)]
[[Paper](https://arxiv.org/pdf/2305.16291.pdf)]
- Builds on top of MineDojo, from the same team
- "continuously improves itself by writing, refining, committing, and retrieving *code* from a skill library"
- " “training” is code execution rather than gradient descent. “Trained model” is a codebase of skills that Voyager iteratively composes, rather than matrices of floats"

- 3 key components
    - Iterative prompting mechanism
        - Write program to achieve a goal, iterate program with:
            - error messages from execution
            - environment feedback: eg “I cannot make an iron chestplate because I need: 7 more iron ingots”
            - self-verification: Use another instance of GPT4 as a critic to evaluate feasibility of program given current state (after code  execution) and task, and provide feedback if task fails
        - iterate till self-verification validates task competion, then add skill to the library. 
        - if agent gets stuck after 4 rounds of code generation, switch to another task from automatic curriculum
    - Skills library: Store ebd of docstring of successful programs in a vector DB to build a skills library. 
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
    - Automatic curriculum: suggests tasks based on current state and agent's skill level, for open ended exploration
        - query GPT for a suggestion to solve task, then query skills DB to identify top 5 relevant skills
        - prompt also includes
            - directives to encourage diverse behaviors and constraints, eg "... discover as many things as possible ... should not be too hard since i may not have the necessary resources"
            - agent's current state (eg inventory, nearby entities, time, position)
            - previously completed and failed tasks
            - additional context: GPT-3.5 (for cost efficiency) to self-ask qns based on agent's current state and exploration progress, self-answer qns with wiki KB for additional context to GPT4
                - Note: cant seem to find the retrieval from wiki implementation on the github repo.
- experimental details:
    - Note: APIs are are mix of author-implemented ones with those provided by Mineflayer
    - temp 0 for all tasks except automatic curriculum (0.1) for diversity
    - baselines: ReAct, Reflexion, AutoGPT. needs to be reinterpreted to be executable in MineDojo
- results: 
    - Better exploration: "3.3× more unique items, travels 2.3× longer distances, and unlocks key tech tree milestones up to 15.3× faster than prior methods"
    - skill library aids Voyager to solve unseen tasks faster. skills library boosts AutoGPT since code is transferrable. advantage: skills are interpretable, composable, temporally extended and amenable to lifelong learning
    - ablation studies clearly suggest that their key components are all important
        - Automatic curriculum crucial for agent's consistent progress, discovered item count drops by 93% if curriculum is replaced by a random one
        - without skills library, agent perfomance plateaus
        - self verification is the most impt among the 3 feedback types, removal leads to 73% drop in discovered item count
        - GPT 4 significantly outperforms GPT 3.5 in code generation, obtains 5.7x more unique items
- limitations and future work
    - current model is text only, can be augmented by visual feedback, maybe from human input
    - occasionally, self-verification fails
        - sometimes JSON not parsed properly so theres a fn to correct for it
    - hallucination as usual. examples: invalid inputs to functions, calling non-existent functions
    - My thoughts
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


### Ghost in the Minecraft: Generally Capable Agents for Open-World Environments via Large Language Models with Text-based Knowledge and Memory
[[Code](https://github.com/OpenGVLab/GITM)]
[[Paper](https://arxiv.org/abs/2305.17144)]
- Results
    - 100% completion rate on minecraft tech tree while prev agents only achieve 30% max
    - Efficient: 2 days on 32 CPU (not GPU!) cores, purely GPT-3.5, not GPT-4
    - SOTA on `ObtainDiamond` challenge, beating DreamerV3, DEPS, VPT by a huge margin (67.5% vs 20% success rate for obtaining diamonds). Fixed an episode of gameplay to the strictest: 10 minutes (12,000 steps at 20Hz control)
    - Observation: Leveraging existing knowledge (GITM) is an advantage over RL agents which learn from scratch. Ties into MineDojo's point of learning from existing sources rather than _tabula rasa_. Also, task destructuring is crucial here -- RL struggles with extremely long horizons, short tasks after destructuring is a simpler subproblem
        - A*, BFS, DFS hardcoded in the action implementation. Not sure if this is a good comparison since the RL agents might need to learn about these techniques from scratch, but then again the point of this paper is to show how existing knowledge can be used rather than learnt
    - Ablation studies on goal decomposition, feedback, external info and memory suggests the importance of each of these elements. Max num of LLM queries capped at 30
- Overview: "decompose goal into sub-goals, then into structured actions, and finally into keyboard/mouse operations", each stage utilizes LLM (their implementation uses gpt-3.5-turbo)
- Decomposer: via text-based knowledge from internet
    - Objective is in a highly structured format: (Object, Count, Material, Tool, Info)
    - will need to adjust this to generalize to more tasks (beyond minecraft)
    - standard vector db retrieval of most relevant facts for a goal via sentence ebd, then LLM identifies reqd materials, tools and related info
- NOTE: The action space is heavily condensed:
    - Start with 3141 predefined tasks on MineDojo
    - use LM to break down each task into a tree of dependencies
        - Set at depth of 2 (empirical)
    - extract action tuple (verb, object, tools, materials) from each sentence of leaf nodes via LM
    - structured actions are extracted by selecting frequent actions and merging actions with similar functionalities (full process not detailed. is this part onwards handcrafting?)
    - this set (cardinality 9) of structured actions (Name, Arguments, Description) define the action space of the paper
- Planner: Plans structured actions for each sub goal, records and summarizes successful action lists into text-based memory.
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
- Interface: execute actions via reading keyboard/ mouse inputs and receiving raw observations, including failure and reason for it
- Additional details
    - Incremental testing: start with 20 runs, then upping to 50, 100, 200 (max) until the success count is nonzero to report the success rate, in order to limit the API calls etc
    - Additional studies: 
        - adding task decomposition to RL based methods boosts performance, reinforcing the point on long-term horizon weakness
        - "Our proposed GITM makes survival and the nether exploration possible in Minecraft which has never been accomplished by existing agents."
    - Code currently not available
- Thoughts
    - No coding required by LM, more like planning/ tool use/ compositions
    - Needs more study on the structured action set selection. what if the set size changes/ actions are different/ erroneous?
---

# TODO

### Toolformer: Language Models Can Teach Themselves to Use Tools
[[Paper](https://arxiv.org/abs/2302.04761)]
- Notes heavily adapted from stanford cs224n 2023 L15
- self supervised approach to teach models to use new tools
    - Start with a few examples of each tool and larger dataset for the task without tool use
    - in context learning to insert candidate API calls in training examples
    - Call APIs, evaluate whether the result decreases perplexity of the rest of the solution
    - Fine tune model on cases where it does

### CODE4STRUCT: Code Generation for Few-Shot Structured Prediction from Natural Language
[[Code](https://github.com/xingyaoww/code4struct)]
- Event Argument Extraction (NLP task) via generating code
- has some of the neuro symbolic stuff i wanna do
