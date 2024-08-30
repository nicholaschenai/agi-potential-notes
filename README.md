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
# Strategies

# Learning
## Procedural learning
- [procedural-learning](strategies/procedural-learning.md)
## Curriculum Learning
- Use LM to generate curriculum from internal knowledge and results from agent task attempts eg [voyager](papers/voyager.md)

## Active learning
Biological inspiration (from Minedojo interactivity example): cats learn better visual repr when it explores on its own.
- (also curriculum learning) [voyager](papers/voyager.md) uses LM to pick suitable task based on current abilities

## Of action space
- [GITM](papers/GITM.md) constrained actions via breaking down of task into dependency trees, for each leaf node extract tuples, get the most common actions

# Task Decomposition
usually beats end-to-end deep learning
- [GITM](papers/GITM.md): prompt based RAG (from wiki n minedojo recipies) decomposition, recursively till no more prerequisites

# Planning
- Reference plan from memory (eg [GITM](papers/GITM.md))
- LLM based planning for a (sub)goal via sequence of structured actions n args (eg [GITM](papers/GITM.md))
- LUMOS shows importance of disentangling subgoal planning and action grounding skills during the agent training
# Memory
## Maintaining internal knowledge
- pure LM based merging of successful sequences eg [GITM](papers/GITM.md)
- KG based pruning before addition [arigraph](papers/arigraph.md)

## Procedural Memory
- Code as skills (eg [voyager](papers/voyager.md), [oscopilot](papers/oscopilot.md))


## Semantic memory
- subject - rel - object graphs (eg [arigraph](papers/arigraph.md))

## Episodic memory
- Episodic observation - semantic knowledge "hypergraph" ([arigraph](papers/arigraph.md))

## Data
for training, retrieval, curriculum

Tinystories: quality data is impt
- Web search
- Document DBs (eg Wikidata in [CoK](https://arxiv.org/abs/2305.13269))
- Knowledge graphs (eg via SPARQL in [CoK](https://arxiv.org/abs/2305.13269))
- Wiki and API (as in ReAct)
- Table corpora (eg WikiTables in [CoK](https://arxiv.org/abs/2305.13269))
- Other data sets (eg ScienceQA in [CoK](https://arxiv.org/abs/2305.13269))

# Retrieval
## Generic
- Importance score by LLM (eg [gen-agents](papers/gen-agents.md))
- Relevance (eg dense vectors like ebd, sparse vectors like BM25, or hybrid)
- Frequency of access (eg [soar](papers/soar.md))
- Tiered: cheap methods like BM25 when filtering from bulk documents, then rerank those retrieved w more expensive but better methods like ebd or LM. Eg [TiM](https://arxiv.org/abs/2311.08719)uses locality sensitive hashing as a first pass (sublinear time!) before using vector similarity on its nearest group of neighbors

## From semantic
- Graph based retrieval (eg [arigraph](papers/arigraph.md))

## From episodic
- Recency score (eg [gen-agents](papers/gen-agents.md) and other episodic mem implentations)
- Graph score (eg [arigraph](papers/arigraph.md))

## From procedural
- [procedural-retrieval](strategies/procedural-retrieval.md)

# Reasoning
- Broad picture concepts eg [step-back-prompting](papers/step-back-prompting.md)
- Reflection: [gen-agents](papers/gen-agents.md) use LM to choose focal point, retrieve based off it, then LM to draw conclusions on the retrieved, citing evidence
## Utility planning in reasoning
i.e. weighing the amount of resources to put into reasoning
- search based approaches like [LATS](papers/LATS.md), ToT etc allows user to define search depth, exploration
- Increasing resource upon failure, eg [[LATS]] "first prompting with a CoT-based prompt, then switching to a ReAct-based prompt upon failure. This is closer to how humans might approach this task, by using tools to lookup additional information only when the answer is not already known."
## Analogical reasoning
Drawing analogies (across domains, contexts) to make sense of new situations from past exp (something like transfer learning)
- via [prompting](papers/llm-analogical.md)
- evaluating LLM's [capacity](https://eecsnews.engin.umich.edu/researchers-investigate-language-models-capacity-for-analogical-reasoning-in-groundbreaking-study/) to do so. “They are good at this one task, but there is a lot more to human reasoning that is missing in these models.”

## Operator based
- [soar](papers/soar.md) "functions is represented as independent rules, which ==fire in parallel== when they match the current situation"

# Decision procedures
- [LATS](papers/LATS.md): selection (MCTS), expansion(MCTS), evaluation via LM, simulation(MCTS), backprop (MCTS), reflection to store failed traj as context. see their comparisons vs other decision procedures like ToT, ReAct, Reflexion


---
# Challenges for cognitive architecture
- see [Challenges](papers/soar.md#Challenges)
- [gen-agents](papers/gen-agents.md): "However, their space of action was limited to manually crafted procedural knowledge, ..."

---
# Concepts
- [cognitive-architecture-motivation](concepts/cognitive-architecture-motivation.md)
- 

---
# Paper Notes
- Cognitive Architectures for Language Agents [coala](papers/coala.md)
- Voyager: An Open-Ended Embodied Agent with Large Language Models [voyager](papers/voyager.md)
- [oscopilot](papers/oscopilot.md)
- [soar](papers/soar.md)
- [GITM](papers/GITM.md)
- Generative Agents: Interactive Simulacra of Human Behavior [gen-agents](papers/gen-agents.md)
- [LATS](papers/LATS.md)
- [saycan](papers/saycan.md)
- [LATM](papers/LATM.md)
- AriGraph: Learning Knowledge Graph World Models with Episodic Memory for LLM Agents [arigraph](papers/arigraph.md)


---
# [TODO](TODO.md)
