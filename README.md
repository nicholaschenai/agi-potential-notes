# Brief notes on understanding intelligence
Really quick notes for my own reference so pardon the untidyness. Will occasionally copy paste directly from the papers!

Notes focused on papers which in my opinion are delivering interesting algorithmic insights/ advancements towards understanding intelligence.

---
# Observations
see [OBSERVATIONS](OBSERVATIONS.md)

---
# Benchmarks
- [MineDojo](https://minedojo.org/)

---

# Paper Notes

### Cognitive Architectures for Language Agents
[Summary](papers/coala.md)

### Voyager: An Open-Ended Embodied Agent with Large Language Models
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
        - iterate till self-verification validates task completion, then add skill to the library. 
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


### Generative Agents: Interactive Simulacra of Human Behavior
[[Code](https://github.com/joonspk-research/generative_agents)]
[[Paper](https://arxiv.org/abs/2304.03442)]
- Spin up multiple instances of LMs + augmented with memory, each representing an individual character. Let them loose in a Sims-style 2D world, where humans can influence / interact with the environment
- Ablation studies on access to memory, reflection and planning -- each of them are important
- Their 'Related Work' section did mention cognitive architectures and its pitfalls at that time: "However, their space of action was limited to manually crafted procedural knowledge, and they did not offer a mechanism through which the agents could be inspired to seek new behavior"
- Emergent behaviors
    - Info diffusion (ie agents chat and info spreads)
    - Relationship / interaction memory (their claim, but it seems explicit in their memory module so it is probably not emergent)
    - Coordination
- Agent architecture (see `persona.py`)
    - Memory: `persona/memory_structures`
        - Associative
            - holds the Memory stream, a comprehensive log of observations, reflections and plans
            - ConceptNode to handle event, chat and thought. subj, predicate, object
        - Scratch
            - some hyperparams, eg in perception, need to specify vision radius, attention bandwith, retention.
        - Spatial
            - About getting accessible areas, game objects
        - a lot of hard coded rules. I wonder if these can be learnt?
    - Main loop in `persona.py`, `.move()` method. Details in `persona/cognitive_modules`:
        - Perceive: `perceived = self.perceive(maze)`
            - sequential memory triplet
            - if new event (observation)
                - ask LM to rate importance (poignancy)
        - Retrieve: `retrieved = self.retrieve(perceived)`
            - `retrieve(perceived)`: related events n thoughts with same keyword. will be performed after every perception step, for planning
            - `new_retrieve(focal_points, n_count=30)`: the main memory retreival method, centered around `focal_points`, scored based on recency, importance (poignancy) and relevance (dense ebd)
        - Plan: `plan = self.plan(maze, personas, new_day, retrieved)`
            - Why? avoid local believability at the expense of global believability, eg having lunch twice. plans are recursively decomposed
            - Relationships between agents need to be explicitly retrieved. (a form of associative memory)
            - make plans via LM and personal traits
            - `new_retrieve` with focal points on persona's plan, use it n LM to revise plan
            - after revision, plan is a thought on its own
            - then get action, use affordances (accessible areas), ask GPT which to go
            - if there was new observation, decide a random one (can we improve this?), then use LM to decide if agent should react to it or continue their existing plan
                - react: this then adjusts the current action n schedule n decomposes it
        - if sum of importance for the latest events exceeds a thr, `run_reflect` (under `reflect.py`)
            - this is impt as raw observations are not as informative; agents need to make inferences over them
            - What to reflect on? generate focal points via LM on the recent (`scratch.importance_ele_n=100`) accessed thoughts and events, centered on subjects
            - `new_retrieve` based on these focal points (what to reflect on)
            - from these statements, use LM to generate insights and cite the evidence, linking them to the old statements. Thus reflections follow a tree like structure, leaf nodes are observations and the level of abstraction increases as we go up the tree
            - each of them r stored as thoughts in memory stream with poignancy
        - Execute: `self.execute(maze, personas, plan)`
- A lot of environment implementation details which I will revisit later
- Eval via interview!
    - self knowledge, memory, planning, reactions, reflections
    - Evaluators rank the believability of the agents response to qns wrt human authored ones
    - Ablation over reflection, planning and observation
	    - Without any of these 3 factors, agents are less believable than humans; Inclusion of at least one makes the agents more believable than humans.
- Emergence eval: social network density increases over time!
- Limitations    
    - Failure to retrieve relevant memories
    - Hallucination: of the mild kind, eg embellishment, but not extreme ones like claiming something that never happened
    - Interference from pre-existing knowledge: eg confusion of Adam Smith (neighbor) with an economist of the same name
    - Less believability as knowlege expands eg upon learning that there is a bar nearby, some agents habitually go there for lunch
    - Social norms not well grasped (esp when agents are largely language based)
    - Agents speak quite formally, most likely due to instruction ft
    - scaling: this expt costs thousands in credits for GPT3.5 for 25 agents over 2 days
- Engineering tips
    - gpt response validation at each step + failsafe response incase of failure. Need to log this

### The Rise and Potential of Large Language Model Based Agents: A Survey
[[Repo (lit review)](https://github.com/WooooDyy/LLM-Agent-Paper-List)]

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
