---
date: 2024-03-31
time: 14:54
author: 
title: "Generative Agents: Interactive Simulacra of Human Behavior"
created-date: 2024-03-31
tags: 
paper: https://arxiv.org/abs/2304.03442
code: https://github.com/joonspk-research/generative_agents
zks-type: lit
---
Spin up multiple instances of LMs + augmented with memory, each representing an individual character. Let them loose in a Sims-style 2D world, where humans can influence / interact with the environment
# Description of result
- Ablation studies on access to memory, reflection and planning -- each of them are important
- Eval via interview!
    - self knowledge, memory, planning, reactions, reflections
    - Evaluators rank the believability of the agents response to qns wrt human authored ones
    - Ablation over reflection, planning and observation
	    - Without any of these 3 factors, agents are less believable than humans; Inclusion of at least one makes the agents more believable than humans.
- Emergence eval: social network density increases over time!
- Emergent behaviors
    - Info diffusion (ie agents chat and info spreads)
    - Relationship / interaction memory (their claim, but it seems explicit in their memory module so it is probably not emergent)
    - Coordination
---
# How it compares to previous work
Their 'Related Work' section did mention cognitive architectures and its pitfalls at that time: "However, their space of action was limited to manually crafted procedural knowledge, and they did not offer a mechanism through which the agents could be inspired to seek new behavior"

---
# Main strategies used to obtain results
see Agent architecture in `persona.py`

## Memory: `persona/memory_structures`
a lot of hard coded rules. I wonder if these can be learnt?
### Associative
- holds the Memory stream, a comprehensive log of observations, reflections and plans
- ConceptNode to handle event, chat and thought. subj, predicate, object
### Scratch
- some hyperparams, eg in perception, need to specify vision radius, attention bandwith, retention.
### Spatial
About getting accessible areas, game objects

## Main loop
in `persona.py`, `.move()` method. Details in `persona/cognitive_modules`
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

---

# Other
##  Limitations    
- Failure to retrieve relevant memories
- Hallucination: of the mild kind, eg embellishment, but not extreme ones like claiming something that never happened
- Interference from pre-existing knowledge: eg confusion of Adam Smith (neighbor) with an economist of the same name
- Less believability as knowlege expands eg upon learning that there is a bar nearby, some agents habitually go there for lunch
- Social norms not well grasped (esp when agents are largely language based)
- Agents speak quite formally, most likely due to instruction ft
- scaling: this expt costs thousands in credits for GPT3.5 for 25 agents over 2 days
## Engineering tips
- gpt response validation at each step + failsafe response incase of failure. Need to log this
