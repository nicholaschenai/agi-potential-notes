# Brief notes on AGI potential
Really quick notes for my own reference so pardon the untidyness. Will occasionally copy paste directly from the papers!

Notes focused on papers which in my opinion are delivering interesting algorithmic insights/ advancements towards AGI.

---
# Personal Observations

- 
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

---

# TODO

### CODE4STRUCT: Code Generation for Few-Shot Structured Prediction from Natural Language
[[Code](https://github.com/xingyaoww/code4struct)]
- Event Argument Extraction (NLP task) via generating code
- has some of the neuro symbolic stuff i wanna do
