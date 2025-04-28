---
date: 2011-10-31
time: 21:34
author: Andrew M. Nuxoll, John E. Laird
title: Enhancing intelligent agents with episodic memory
created-date: 2024-12-17
tags:
  - memory
  - episodic-memory
  - cog-arch
  - soar
paper: https://web.eecs.umich.edu/~soar/sitemaker/docs/pubs/1-s2.0-S1389041711000428-main.pdf
code: 
zks-type: lit
---

### Why episodic
"Episodic memory
supports a wide range of cognitive capabilities from keeping
track of the world outside immediate perception, to allowing
retrospective learning on previously encountered situations"

"there are additional functional requirements that Tulving
(1983) identified that distinguish episodic memory from
other memory and learning mechanisms"

- Automatic: agent does not need to decide creating episodic mem. assumptions:
	- deciding to remember can interfere w task based reasoning
	- unlikely that agent can effectively determine which exp will be relevant to future decisions
- AUTONOETIC: retrieved memory distinguished frm current sensing to prevent confusion
- Temporally indexed: as episodic mem describes a unique moment in time, some temporal info is a part of any episodic mem and can be part of episodic cue. does not need to be exact time but at least relative time wrt other episodes


Not included in this work:
- consolidation
- forgetting
- interference
- riming
- generalization across episodes or specific models of time

---
## Description of result
capabilities which episodic mem can provide
- action modeling
- decision making from past exp
- retroactive learning
- boost other learning mechanisms
- virtual sensing

### Benchmarks
#### Eaters
Pac-man like env. node: agent current position, with features about shape n color. has additinal relations w other nodes (north south east west). hostile for episodic cos states/mem are similar with small significant differences

#### TankSoar
2D grid tank shooter game


### action modeling
recall outcomes of past to predict outcomes of action in present by recalling similar situations

Eaters env:
- state: current sensing and score, elements related by agent's reasoning
- deciding direction to move
	- cue: current state + action to be evaluated
	- retrieve best match state and immediate next state, get difference of scores
	- select action that gives best delta, ties broken randomly
- correct evaluation: predicting correct change in score that will result for given action. if incorrect prediction or unable to predict due to failed retrieval, outcome is failure

result: agent's fraction of correct evaluation increases over time

comment: these days, common for RL to use replay buffer which encodes (obs, action, reward, next obs, done), and the Q fn is an extension for predicting reward given current state. however deep RL usually uses NN for Q, while this work performs explicit matching

### Decision-making based on past experiences 
(successes n failures) eg during planning. action modeling is for short horizon (next step), while this is for long horizon

TankSoar env (2D grid tank shooter game)
- similar to action modeling, use cue that contains heuristically selected portions of curent state + action to be evaluated
- retrieve closest match state + its trajectory (immediate 10 steps or till end of game or either agent is destroyed, whichever is shorter)
- calculate difference between first and last retrieved episode as value, with discounting
- selection of action same as prev
- evaluation: difference between episodic agent's score and control (heuristic) agent's score (margin of victory)

result: 
- margin of episodic agent starts negative (loses to control), then increases with successive games and plateaus after ~50 games ending in positive
- emergent behavior
	- learn to dodge short range attacks before they hit
	- learn to back away from enemy while firing its missiles
	- learn to move out of enemy's sight when it was in a tactically unfavorable situation

### Retroactive learning
often not possible to learn while an event is ocuring as agent lacks specific info / resources (eg computation) needed to learn. episodic mem allows previous exp to be available once resources r avail

Eaters env, algo
- warmup period: initially random action to gather episodic mem
- after warmup, retrieve mem by constructing cues consisting of agent in all possible combinations of neighboring cells
- from retrieval, use Soar's rule-learning mechanism (Laird, Rosenbloom & Newell 1986) to create new productions (which action to take from contents of neighboring cells, so like a policy) (chunking?)

result: fraction of correct actions near perfect (fluctuates around 90-100%) right after retroactive learning. occasional dips when encountering novel situation. random action results in 30% baseline

### boosting other learning mechanisms
ep mem is data for other learning mechanisms to potentially allow faster learning in new situations and even how to behave in novel situations

starting from Eaters episodic agent, apply Soar chunking so that processing that generated episodic mem retrievals are compiled and cached, eliminating number of ep retrieval over time and allows agent to eat faster

![](assets/Pasted%20image%2020241219140559.png)

can see that chunking + episodic have synergies



### virtual sensing
expand agent's sensing by providing mem of areas beyind immediate perception

TankSoar env
- Task: locate battery used to recharge ageent's energy supply
- when tank's energy runs low, agent cannot use its radar and it is blinded
- "While an
agent could deliberately construct a map while it has
energy, we wanted to demonstrate that an agent with an
episodic memory could construct a map by relying on
its episodic memory."

algo:
- move forward/backward till blocked
- if have knowledge of path (coordinate + direction) from this position to battery, turn to direction specified by that path and return to step above
- else, retrieve episodic mem of seeing battery from this position.
	- cue: current location and perceptual elements that represent seeing the battery on its radar.
	- if retrieval successful, record a path in this path in this position and move directly towards the battery, reset to first step
	- else, attempt to retrieve ep mem of being in current position and seeing another location for which it has already recorded a path to the battery (reducing the problem to one thats previously solved). 
		- if retrieval succesful, create a new path in working mem that directs agent from this position towards origin of existing path and reset to step 1
		- else, move randomly and reset to step 1

result: num moves better than random and decreases over time (notice log scale!)

![](assets/Pasted%20image%2020241219141859.png)



---
## How it compares to previous work
Episodic mem learn frm success n failures Similar to case based reasoning
![](assets/Pasted%20image%2020241219142023.png)


---
## Main strategies used to obtain results


### Working mem element repr
- identifier-attribute-value triplet
- value of one WME can be identifier of another WME so they are linked in graph structure
### Working memory element activation rules
- initialized with default activation when created
- when tested by production that fires, activation lvl increased
- when agent attempts to add an alr existing WME, existing WME activation increase
- exponentially decays over time, rate of decay determined by frequency and recency with which it has received activation increases

### Working mem
- state contains sensory info n info on agents potential actions, goals, results of inferences made about situation
### Episodic mem implementation
- records episodic mem (snapshot of working mem) at prescribed times
	- when? (rmb need automatic) each time agent takes action in the world
		- alternative implementations (e.g. recording every processing cycle) had little impact on performance other than changing total number of ep
	- what are contents of episode?
		- agent's state which includes input (sensing), internal data structures and output (actions in world)
		- but currently excludes episodic mem cues and retrievals (since this nested structure could make individual episodes quite large)
		- exclude those with activation below thr to eliminate irrelevant ones
#### storage
- data structure: conceptually a sequence of individual episodes but implementation only stores CHANGES between episodes for efficiency (cos few items change from episode to episode)
- dynamics: none at the moment. future work can be to implement forgetting or analyzing episodes for commonalities n generalization
#### retrieval
- when? 
	- deliberate, triggered when agent constructs cue in working mem
	- problem with spontaneous retrieval: eliminates ability of agent to select subset of state for cue, which is critical for focusing the retrieval on specific aspects of current state (attention!). computational load
- how is cue specified?
	- partial desc of an episode that is matched against stored episodes
	- implementation also allows agent to restrict retrieval to specific time periods(eg before time X or immediately after time Y)
- selection of retrieved episode
	- best match score if that score is above thr (return none if below thr)
	- see optimizations in Derbinsky & Laird 2009
	- most sucessful matching scores weighs 2 factors
		- cardinality of match (num elements that cue and mem have in common. also implemented in [arigraph](arigraph.md))
		- activation level of working mem elements in the cue that match the mem (see Nuxoll & Laird 2004)
- representation of retrieved
	- same format when first encoded
	- add metadata about retrieval: temporal index indicating when mem was encoded and info about strength of match b/w retrieved mem and cue to allow agent to determine if match was perfect or partial

---

## Other
### Future work
- episodic to determine input saliency / relevance by comparing to prior memories
- prevent repeated actions
- environment modeling (eg sunset timing)
- managing LT goals (eg storing previous progress when switching goals due to env demands and opportunities, reschedule)
- sense of identity so can analyze it and compare to other agents
- re-analyze given new knowledge (eg invalidate past inference and behavior)
- explaining behavior (this is done in Episodic Memory Verbalization)