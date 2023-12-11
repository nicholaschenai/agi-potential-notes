# Brief notes on understanding intelligence
Really quick notes for my own reference so pardon the untidyness. Will occasionally copy paste directly from the papers!

Notes focused on papers which in my opinion are delivering interesting algorithmic insights/ advancements towards understanding intelligence.

---
# Personal Observations

- Decomposing tasks beats end-to-end deep learning
- Having a memory component separate from the foundation models is important 
---
# Benchmarks
- [MineDojo](https://minedojo.org/)

---

# Paper Notes


### Cognitive Architectures for Language Agents
[[Repo](https://github.com/ysymyth/awesome-language-agents)]
[[Paper](https://arxiv.org/abs/2309.02427)]
- Concept paper which proposes "Cognitive Architectures for Language Agents (CoALA), a conceptual framework to systematize diverse methods for LLM-based reasoning, grounding, learning, and decision making as instantiations of language agents in the framework"
    - Use the framework to "highlight gaps and propose actionable directions toward more capable language agents in the future."
    - Draws parallels with these ideas: _production systems_ and _cognitive architectures_ (finally!)
        - "Production systems generate a set of outcomes by iteratively applying rules"
- Fig 1, the different types of LLMs and going up the hierarchy of complexity
    - 1a. Simplest type: LLM as generic input-output fn
    - 1b. LLM as an agent, in a typical RL fashion interacting with the env by producing an action based on observation
    - 1c. Cognitive LM, same setup as 1b but the agent internally has various components like memory, retrieval learning and reasoning (i.e. RAG style)
- Background: From Strings to Symbolic AGI
    - "Large production systems connected to external sensors, actuators, and knowledge bases required correspondingly sophisticated control flow."
    - "AI researchers defined 'cognitive architectures' that mimicked human cognition - explicitly instantiating processes such as perception, memory, planning (Adams et al., 2012) to achieve flexible, rational, real-time behaviors"
    - Canonical example: Soar (fig 2A)
        - "Soar stores productions in long-term memory and executes them based on how well their preconditions match working memory. These productions specify actions that modify the contents of working and long-term memory". These actions may also be external (eg motor command)
        - Memory types: 
            - working (agent's current circumstances eg "agent's recent perceptual input, goals, and results from intermediate, internal reasoning")
            - long term
                - procedural: production system itself. "the set of rules that can be applied to working memory to determine the agent's behavior"
                - semantic: facts about the world
                - episodic: seq of agent's past behaviors
        - Grounding: Soar can be initiated in simulations or real-world systems (embodied)
        - Decision making: Fig 2B
            - Productions stored in procedural mem
            - each decision cycle: their preconditions are checked against the agent's working memory
            - proposal and evaluation phase: a set of productions is used to generate and rank a candidate set of possible actions (see operators vs rules footnote)
            - choose best action (break ties via created subgoal)
            - Another set of productions is then used to implement the action
        - Learning
            - new info can be stored directly in long-term memory
            - facts can be written to semantic memory
            - experiences can be written to episodic memory
            - info retrieved back into working memory when needed for decision making
            - RL can be used to up-weight productions that yielded good outcomes
            - Soar can write new productions into its procedural memory, effectively updating its source code!
    - CAs have become less popular in the community due to the limitation to domains that can be prescribed by logical predicates, and require pre-specified rules to function
    - LLMs appear well-posed to meet these challenges due to its flexibility (can operate over arbitrary text) and reduced user specification requirements (LLMs learn a distribution over productions via pretraining)
    - Researchers have begun to use LLMs within CAs for their implicit world knowledge and to augment traditional symbolic approaches (see citation)
    - In this paper, the authors import principles from CA to guide the design of LLM based agents
- Connections b/w LMs and production systems
    - section draws analogies between the two. 
    - Fig 3 shows the evolution of LMs to agents. 3a: basic LLM call. 3b: prompt chaining uses pre defined sequences of LLM calls. 3c: language agents use an interactive feedback loop with env.
- Cognitive Architectures for Language Agents (CoALA): A Conceptual Framework (fig 4A)
    - Key contributions over previous CA
        - Inclusion of LLMs leads to the addition of 'reasoning' actions, which can flexibly produce new knowledge and heuristics, replacing handcrafted rules in previous CAs
        - makes text the de factor internal representation
        - suggests vision-language models can simplify grounding by translating perceptual data to text
    - Decision cycle: retrieval + reasoning to plan by proposing + evaluating candidate learning or grounding actions. choose n execute the best action, observe, repeat cycle
    - Memory
        - working memory: maintains active and readily available information as symbolic variables for the current decision cycle (scratch memory). eg perceptual inputs from grounding, knowledge generated by reasoning or retrieving from long term memory, other info from previous decision cycle.
        - episodic memory: exp from earlier decision cycles, eg training data, history event flows, past game trajectories. Can be retrieved into working memory to support reasoning. can write new exp frm working to episoding memory as a form of learning
        - semantic memory: agent's knowledge about the world and itself. (eg initialized from external db, RAG). past expamples tend to employ read-only semantic memory but  lang agents may write new knowledge from LLM reasoning into semantic memory as a form of learning.
        - procedural memory: 
            - implicit knowledge in LLM weights, explicit knowledge in agent's code.
            - explicit knowledge
                - procedures that implement actions
                    - LLM accessed via reasoning action
                    - code-based procedures retrieved
                - procedures that implement decision making
            - must be initialized properly by designer, compared to episodic or semantic memory which can be initially empty
            - possible to write to procedural memory but riskier than writing to semantic or episodic memory as this can introduce bugs or safety concerns (akin to Voyager writing wrong code in skills library)
    - Actions
        - external actions: interact with external envs thru grounding
        - internal actions: interact with internal memories
            - reasoning: update short term working memory w LLM. read from AND write to working memory. to summarize n distill insights. support learning (by writing results into LT memory) or decision making (results as additional context for subsequent LLM calls)
            - retrieval: read from long term memory into working memory. various implementations eg rule based, sparse, dense retrieval
                - eg Voyager uses dense retreival for retrieving skills frm lib, gen agents retrieves relevant events from episodic mem via recency (rule-based) + importance (reasoning-based) and relevance (emb/ dense). DocPrompting uses lib docs, i.e. dense retrieval frm semantic mem
            - learning: write to long term memory
                - update episodic memory w experience (eg replay buffer in RL?)
                - Updating semantic memory with knowledge: use LLM to reason about raw experiences and store these into semantic memory, like gen agents reflection
                - Updating LLM parameters (procedural memory). eg via supervised, imitation learning. RL. RLHF, RLAIF. distillation
                - Updating agent code (procedural memory).
                    - updating reasoning (eg prompt templates, prompt update)
                    - updating grounding (eg voyager's curriculum library)
                    - updating retrieval (query / document expansion, retrieval distillation to learn better retrieval over time)
                    - updating learning or decision-making: authors not aware of related works tho this direction is possible, but note alignment issues
                - language agents can select frm diversity of learning procedures, allowing them to learn rapidly (potentially faster than fixed way of learning like updating model params) by storing task-relevant language, and leverage multiple forms of learning to compound self-improvement
                - authors emphasize that most work are on additive techniques for memory, and works that are subtractive or selective (eg modifying memories) are understudied
    - Choose actions via decision making
        - planning
            - proposal: generate action candidates via reasoning and retrieval. possibilities: all actions if action space is small, rule based selection, GOFAI planning eg PDDL
            - eval: via heuristic rules, LLM (perpelxity) values, learned values, LLM reasoning or some combination. "Particularly, LLM reasoning can help evaluate actions by internally simulating their grounding feedback from the external world". "identifying defects, and proposing modifications that address those defects" -- some code models do this, see CodeRL
            - selection: decide via values or decide to re-iterate to some proposal or evaluate sub-state
        - execution
        - recent trend to "investigate more complex decision making by proposing more than one action, and with iterative proposal and evaluation", such as tree of thoughts (iteratively propose and evaluate thoughts in a systematic tree search (DFS/BFS) to return a final solution), RAP (generates partial thoughts and maintains them in a tree structure using MCTS)
- Case studies: see table 2 for comparison across existing examples

selective table

| Paper    | LT memory | ext grounding | internal actions | decision making |
| -------- | ------- | --- | ---| --- |
| ReAct | - | digital | reason | propose|
| Voyager | procedural | digital |  reason/ retrieve/ learn | propose |
| Generative Agents |  episodic/semantic | digital/agent |  reason/ retrieve/ learn | propose |

- **Actionable insights**
    - Working memory and reasoning: thinking beyond LLM prompt engineering: "the community should think about a structured working memory and systematic 'reasoning' actions that update working memory variables"
    - Long-term memory: thinking beyond retrieval augmentation: "memory-augmented language agents can both read and write self-generated content autonomously"-- combine existing human knowledge with self-discovered and self maintained exp, knowledge and skills. example: coding agent starts off with human knowledge (semantic), problem solutions and test records (episodic), reflection n summaries on top of these exp (semantic), gradually expanding code lib that sttores useful methods eg Quicksort (procedural). A bit like bootstrapping AlphaGo off existing games then enhancing from self play?
    - Learning: thinking beyond in-context learning or finetuning. (read this entire paragraph!)
        - learning better retrieval procedures could enable agents to better connect memorized information to new scenarios
        - recent expansion based techniques may be readily applied ('in what situations would this knowledge be useful?', and append the reasoning results to the knowledge to help later connect the knowledge to new situations)
        - Learning new learning and decision procedures may be necessary to empower agents to go beyond the limitations of human-provided code
        - explore smaller models for specific reasoning needs
        - unlearning!
        - combine multiple forms of learning
    - Action space: thinking beyond external tools or actions.
        - impt to balance tradeoff for size of action space: larger means more expressivity/ capability, but harder decision making
        - Safety! currently limited to task-specific heuristics, but as agents are increasingly integrated into systems, "it will be necessary to clearly specify and ablate the agent's action space for worst-case scenario prediction and prevention"
    - Decision making: thinking beyond action generation.
        - most works are still confined to proposing (or directly generating) a single action. It would be powerful to extend deliberate propose-evaluate-select schemes to "more complicated tasks with grounding and LT memory; enable learning of decision making procedures; and reduce the cost of extensive reasoning to facilitate more deliberate planning (potentially via smaller task-specific models for proposal or evaluation)"
        - "increased usage of reasoning toward complex decision making"
        - "solving known issues such as over-confidence and miscalibration, misalignment with human values or bias, hallucinations in self-evaluation, and lack of human-in-the-loop mechanisms in face of uncertainties"
- Discussion
    - Planning vs. execution: how much should agents plan? "balancing the cost of planning against the utility of the resulting improved plan" (ref [xkcd comic](https://xkcd.com/1445/) of plan A vs plan B vs thinking about choosing plan A or B)
    - Learning vs. acting: how should agents continuously and autonomously learn? (explore-exploit for learning)
    - LLMs vs. code: where should agents rely on each? "CoALA thus suggests that good design uses agent code primarily to implement classic, generic planning algorithms - and relies heavily on the LLM for action proposal and evaluation."


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

### Generative Agents: Interactive Simulacra of Human Behavior
[[Code](https://github.com/joonspk-research/generative_agents)]
[[Paper](https://arxiv.org/abs/2304.03442)]
- Spin up multiple instances of LMs + augmented with memory, each representing an individual character. Let them loose in a Sims-style 2D world, where humans can influence / interact with the environment
- Ablation studies on access to memory, reflection and planning -- each of them are important
- Common failures during eval: Failure to retrieve relevant memories, mild hallucination, inheriting overly formal speech/ behavior from underlying LM
- Their 'Related Work' section did mention cognitive architectures and its pitfalls at that time: "However, their space of action was limited to manually crafted procedural knowledge, and they did not offer a mechanism through which the agents could be inspired to seek new behavior"
- Emergent behaviors
    - Info diffusion (ie agents chat and info spreads)
    - Relationship / interaction memory (their claim, but it seems explicit in their memory module so it is probably not emergent)
    - Coordination
- Agent architecture (see `persona.py`)
    - Memory stream: comprehensive log of observations, reflections and plans
    - From observation, architecture retrieves relevant memories to determine an action
    - Retrieved memories also used to form long term plans and higher level reflections, entered into memory stream
    - In code: `persona/memory_structures`
        - Associative
            - ConceptNode to handle event, chat and thought. subj, predicate, object
        - Scratch
            - some hyperparams, eg in perception, need to specify vision radius, attention bandwith, retention.
        - Spatial
            - About getting accessible areas, game objects
        - a lot of hard coded rules. I wonder if these can be learnt?
    - Main loop in `persona.py`, `.move()` method. Details in `persona/cognitive_modules`:
        - Perceive
            - sequential memory triplet
            - if new event (observation)
                - poignancy (ask LM to rate importance)
        - Retrieve
            - related events n thoughts with same keyword
        - Plan
            - make plans via LM and personal traits
            - `new_retrieve(importance, relevance, recency)` with focal points on persona's plan, use it n LM to revise plan
            - after revision, plan is a thought on its own
            - then get action, use affordances (accessible areas), ask GPT which to go
            - if there was new observation, decide a random one, then use LM to decide if should react
                - this then adjusts the current action n schedule n decomposes it
        - if reflection trigger, `run_reflect` (under `reflect.py`)
            - generate focal points via LM on the recent accessed thoughts and events, centered on subjects
            - `new_retrieve` based on these focal points
            - from these statements, use LM to genereate insights and evidence
            - each of them r stored as thoughts with poignancy
        - Execute
- Memory Retrieval: Scored based on recency, importance and relevance
- Reflection: Triggered when sum of importance for the latest events exceeds a thr. 
    - this is impt as raw observations are not as informative; agents need to make inferences over them
    - Determine what to reflect on by prompting the LM and augmenting with 100 most recent records, to get it to generate high level qns
    - then use these qns for retrieval, and prompt LM to extract insights and cite the sources
    - Reflections follow a tree like structure, leaf nodes are observations and the level of abstraction increases as we go up the tree
- Planning
    - used to avoid local believability at the expense of global believability, eg having lunch twice
    - Plans are recursively decomposed
    - During each new observation, agent can choose to continue or update their existing plan (potentially interacting with the new observation)
    - Relationships between agents need to be explicitly retrieved. (a form of associative memory)
- A lot of environment implementation details which I will revisit later
- Eval via interview!
    - self knowledge, memory, planning, reactions, reflections
    - Evaluators rank the believability of the agents response to qns wrt human authored ones
    - Without reflection, planning and observation, agents are less believable than humans. 
    - Inclusion of at least one of these components make the agents more believable than humans.
    - Inclusion of all 3 components result in the highest score
- Emergence eval
    - abit hard to do so
    - social network density increases over time!
- Limitations    
    - Hallucination: of the mild kind, eg embellishment, but not extreme ones like claiming something that never happened
    - Interference from pre-existing knowledge: eg confusion of Adam Smith (neighbor) with an economist of the same name
    - Less believability as knowlege expands eg upon learning that there is a bar nearby, some agents habitually go there for lunch
    - Social norms not well grasped (esp when agents are largely language based)
    - Agents speak quite formally, most likely due to instruction ft
    - scaling: this expt costs thousands in credits for GPT3.5 for 25 agents over 2 days
- Engineering tips
    - gpt response validation at each step + failsafe incase of failure. Need to log this
    - 

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
