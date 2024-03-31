# Brief notes on understanding intelligence
Really quick notes for my own reference so pardon the untidyness. Will occasionally copy paste directly from the papers!

Notes focused on papers which in my opinion are delivering interesting algorithmic insights/ advancements towards understanding intelligence.

---
# Benchmarks / Envs
## Open ended game
- [MineDojo](https://minedojo.org/)
## QA

| Benchmark                       | Desc                                                                                                       | SOTA |
| ------------------------------- | ---------------------------------------------------------------------------------------------------------- | ---- |
| MMLU (Hendrycks et al., 2020)   | diverse QA, including STEM                                                                                 |      |
| GSM8K                           | STEM                                                                                                       |      |
| TimeQA (Chen et al., 2021)      | SituatedQAtime sensitive knowledge                                                                         |      |
| SituatedQA (Zhang & Choi, 2021) | "open-retrieval QA dataset requiring the model to answer questions given temporal or geographical context" |      |
| MuSiQue (Trivedi et al., 2022)  | multihop reasoning                                                                                         |      |
| StrategyQA (Geva et al., 2021)  | multihop reasoning, open domain                                                                            |      |



---
# LLM capabilities and challenges
- see LLM planning capability survey  [here](https://arxiv.org/pdf/2402.02716.pdf)

---

# Learning
## Curriculum Learning

## Active learning
Biological inspiration (from Minedojo interactivity example): cats learn better visual repr when it explores on its own.
- (also curriculum learning) [voyager](papers/voyager.md) uses LM to pick suitable task based on current abilities

## Of action space
- [GITM](../../gh-notes/agi-potential/papers/GITM.md) constrained actions via breaking down of task into dependency trees, for each leaf node extract tuples, get the most common actions

# Task Decomposition
usually beats end-to-end deep learning
- [GITM](papers/GITM.md): prompt based RAG (from wiki n minedojo recipies) decomposition, recursively till no more prerequisites

# Planning
- Reference plan from memory (eg [GITM](papers/GITM.md))
- LLM based planning for a (sub)goal via sequence of structured actions n args (eg [GITM](papers/GITM.md))
# Memory
## Maintaining internal knowledge
- pure LM based merging of successful sequences eg [GITM](papers/GITM.md)

# Reasoning
- Broad picture concepts eg [step-back-prompting](papers/step-back-prompting.md)
- Reflection: [gen-agents](papers/gen-agents.md) use LM to choose focal point, retrieve based off it, then LM to draw conclusions on the retrieved, citing evidence
## Analogical reasoning
Drawing analogies (across domains, contexts) to make sense of new situations from past exp (something like transfer learning)
- via [prompting](papers/llm-analogical.md)
- evaluating LLM's [capacity](https://eecsnews.engin.umich.edu/researchers-investigate-language-models-capacity-for-analogical-reasoning-in-groundbreaking-study/) to do so. “They are good at this one task, but there is a lot more to human reasoning that is missing in these models.”

## Operator based
- [soar](papers/soar.md) "functions is represented as independent rules, which ==fire in parallel== when they match the current situation"

# Decision procedures
- LATS: selection (MCTS), expansion(MCTS), evaluation via LM, simulation(MCTS), backprop (MCTS), reflection to store failed traj as context

# Data
Tinystories: quality data is impt

---
# Paper Notes
- Cognitive Architectures for Language Agents [coala](papers/coala.md)
- Voyager: An Open-Ended Embodied Agent with Large Language Models [voyager](papers/voyager.md)
- [oscopilot](papers/oscopilot.md)
- [soar](papers/soar.md)
- [GITM](papers/GITM.md)
- Generative Agents: Interactive Simulacra of Human Behavior [gen-agents](papers/gen-agents.md)
- The Rise and Potential of Large Language Model Based Agents: A Survey [[Repo (lit review)](https://github.com/WooooDyy/LLM-Agent-Paper-List)]

---
# [TODO](TODO.md)
