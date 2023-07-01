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
    - Iterative prompting mechanism: Write program to achieve a goal, iterate program with error messages from execution and environment feedback. Use GPT4 as a critic to evaluate feasibility of program. iterate till self-verification validates task competion, then add skill to the library. if agent gets stuck after 4 rounds of code generation, switch to curriculum of another task
    - Skills library: Store ebd of docstring of successful programs in a vector DB to build a skills library. Complex skills are composed of simpler skills
    - Automatic curriculum: suggests tasks based on current state and agent's skill level, for open ended exploration
        - query GPT for a suggestion to solve task, then query skills DB to identify top 5 relevant skills
- experimental details:
    - temp 0 for all tasks except automatic curriculum (0.1) for diversity
    - baselines: ReAct, Reflexion, AutoGPT. needs to be reinterpreted to be executable in MineDojo
- results: 
    - Better exploration: "3.3× more unique items, travels 2.3× longer distances, and unlocks key tech tree milestones up to 15.3× faster than prior methods"
    - skill library aids Voyager to solve unseen tasks faster. skills library boosts AutoGPT since code is transferrable. advantage: skills are interpretable, composable, temporally extended and amenable to lifelong learning
    - ablation studies clearly suggest that their key components are all important
        - self verification is the most impt
        - GPT 4 significantly outperforms GPT 3.5 in code generation, obtains 5.7x more unique items
- limitations and future work
    - current model is text only, can be augmented by visual feedback, maybe from human input
    - occasionally, self-verification fails
    - hallucination as usual. examples: invalid inputs to functions, calling non-existent functions

### Ghost in the Minecraft: Generally Capable Agents for Open-World Environments via Large Language Models with Text-based Knowledge and Memory
[[Code](https://github.com/OpenGVLab/GITM)]
[[Paper](https://arxiv.org/abs/2305.17144)]
- Results
    - 100% completion rate on minecraft tech tree while prev agents only achieve 30% max
    - Efficient: 2 days on 32 CPU (not GPU!) cores
    - SOTA on `ObtainDiamond` challenge, beating DreamerV3, DEPS, VPT by a huge margin (67.5% vs 20% success rate for obtaining diamonds). Fixed an episode of gameplay to the strictest: 10 minutes (12,000 steps at 20Hz control)
    - Observation: Leveraging existing knowledge (GITM) is an advantage over RL agents which learn from scratch. Ties into MineDojo's point of learning from existing sources rather than _tabula rasa_. Also, task destructuring is crucial here -- RL struggles with extremely long horizons, short tasks after destructuring is a simpler subproblem
        - A*, BFS, DFS hardcoded in the action implementation. Not sure if this is a good comparison since the RL agents might need to learn about these techniques from scratch, but then again the point of this paper is to show how existing knowledge can be used rather than learnt
    - Ablation studies on goal decomposition, feedback, external info and memory suggests the importance of each of these elements. Max num of LLM queries capped at 30
- Overview: "decompose goal into sub-goals, then into structured actions, and finally into keyboard/mouse operations", each stage utilizes LLM (their implementation uses gpt-3.5-turbo)
- Decomposer: via text-based knowledge from internet
    - Objective is in a highly structured format: (Object, Count, Material, Tool, Info)
    - will need to adjust this to generalize to more tasks (beyond minecraft)
    - standard vector db retrieval of most relevant facts for a goal via sentence ebd, then LLM identifies reqd materials, tools and related info
- Planner: Plans structured actions for each sub goal, records and summarizes successful action lists into text-based memory.
    - goal recursively decomposed to sub goals (prereq items etc) until each subgoal has no prerequisites.
    - execution sequence planned thru post-order traversal
    - Structured action: (Name, Arguments, Description)
    - "LLM is utilized to decompose the 3141 predefined tasks provided by MineDojo into action sequences"
    - "extract the structured actions by selecting frequent actions and merging actions with similar functionalities"
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
        - skills consolidation: LLM used to summarize essential actions from multiple plans, which is used as a reference for future plans when a similar goal is encountered. Their implementation: when the num of action sequences reaches _N_ (they use _N_=5), they are popped from the list, summarized and pushed to the front of the list. If the subgoal has a memory attached to it, the first action sequence is used as the reference plan, and if it succeeds, the new executed action sequence gets pushed to the back of the list
- Interface: execute actions via reading keyboard/ mouse inputs and receiving raw observations, including failure and reason for it
- Additional details
    - Incremental testing: start with 20 runs, then upping to 50, 100, 200 (max) until the success count is nonzero to report the success rate, in order to limit the API calls etc
    - Additional studies: 
        - adding task decomposition to RL based methods boosts performance, reinforcing the point on long-term horizon weakness
        - "Our proposed GITM makes survival and the nether exploration possible in Minecraft which has never been accomplished by existing agents."
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
