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
- [learning-grounded-action](papers/learning-grounded-action.md) Construct task-specific representations (DSL) from LLMs with execution feedback

## Reward functions
- [eureka](papers/eureka.md) uses LLM to design reward functions, execute in RL envs and reflect to iteratively improve

## Task Decomposition

^c7f968

usually beats end-to-end deep learning
- [GITM](papers/GITM.md): prompt based RAG (from wiki n minedojo recipies) decomposition, recursively till no more prerequisites

# Planning
- Reference plan from memory (eg [GITM](papers/GITM.md))
- LLM based planning for a (sub)goal via sequence of structured actions n args (eg [GITM](papers/GITM.md))
- LUMOS shows importance of disentangling subgoal planning and action grounding skills during the agent training

---
# Memory
- [memory-maintenance](strategies/memory-maintenance.md)

## Working Memory (Short term)

## Long term memory (Generic)
- [Evaluation of](concepts/lt-mem-eval.md)
## Procedural Memory
- Code as skills (eg [voyager](papers/voyager.md), [oscopilot](papers/oscopilot.md))
- Workflows (reusable trajectory templates), e.g. [agent-workflow-memory](papers/agent-workflow-memory.md)

## Semantic memory
- subject - rel - object graphs (eg [arigraph](papers/arigraph.md))
- [zep](papers/zep.md) declarative mem KG

## Episodic memory
- Episodic observation - semantic knowledge "hypergraph" ([arigraph](papers/arigraph.md))
- [zep](papers/zep.md) declarative mem KG

---
# Retrieval
- [procedural-retrieval](strategies/procedural-retrieval.md)
- [episodic-retrieval](strategies/episodic-retrieval.md)

## Generic
- Importance score by LLM (eg [gen-agents](papers/gen-agents.md))
- Dense embeddings: Vector similarity
- Lexical (keyword) search
- Sparse vectors like BM25
- Frequency of access (eg [soar](papers/soar.md))
- Tiered: 
	- cheap methods like BM25 when filtering from bulk documents, 
	- then rerank those retrieved w more expensive but better methods like ebd or LM. 
	- Eg [TiM](https://arxiv.org/abs/2311.08719)uses locality sensitive hashing as a first pass (sublinear time!) before using vector similarity on its nearest group of neighbors
- Combination of the above

## From semantic
- Graph based retrieval (eg [arigraph](papers/arigraph.md))
---

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

---
# Decision procedures
- [LATS](papers/LATS.md): selection (MCTS), expansion(MCTS), evaluation via LM, simulation(MCTS), backprop (MCTS), reflection to store failed traj as context. see their comparisons vs other decision procedures like ToT, ReAct, Reflexion

