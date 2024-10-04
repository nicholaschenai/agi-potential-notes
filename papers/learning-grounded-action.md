---
date: 2024-02-18
time: 15:22
author: Lionel Wong, Jiayuan Mao, Pratyusha Sharma, Zachary S. Siegel, Jiahai Feng, Noa Korneev, Joshua B. Tenenbaum, Jacob Andreas
title: "LEARNING GROUNDED ACTION ABSTRACTIONS FROM\r LANGUAGE"
created-date: 2024-02-18
tags: 
paper: https://openreview.net/pdf?id=qJ0Cfj4Ex9
code:
---
# Intro

older paper [LEARNING ADAPTIVE PLANNING REPRESENTATIONS WITH NATURAL LANGUAGE GUIDANCE](https://arxiv.org/abs/2312.08566)

Framework for automatically constructing task-specific planning representations (DSL) using task-general background knowledge from language models (LMs). 

## setting
-  multitask-reinforcement-learning
- agent can interact with an environment and attempt to solve a dataset of tasks (in natural lang) of varying complexity
- agent receives reward upon successfully completing a task. 
- assume access to a set of symbolic predicates that describe general aspects of the environment state, such as object categories and spatial relationships between objects.

![](assets/Pasted%20image%2020240224155313.png)

![](assets/Pasted%20image%2020240224155442.png)

- possible to learn policy that maps from low level states to low level actions (think atari DQN), but for long horizon and large state space situations, doing so can be inefficient / infeasible. thus use natural lang to build abstract action and state space

## objective
build a library of planning-compatible and grounded actions. Specifically, abstractions with two components:
- (1) a high-level abstract action / operator $a$ represented as a symbolic planning operator (Fikes & Nilsson, 1971) that specifies preconditions and action effects as sets of predicates; and 
- (2) a low-level local policy (controller) that maps (abstract actions, low level state) to low level actions. 

we can leverage symbolic planners such as Fast-Downward (Helmert, 2006) to first compute an abstract plan ${a_1, · · · , a_K} ∈ A^K$ that achieves the natural lang goal symbolically, and then refine the high-level plan into a low-level plan leveraging the controllers associated with each action.  ^e1c0d4

Effectively, such a bi-level learning and planning algorithm would decompose the long-horizon planning problem into several short-horizon problems. Furthermore, it can also leverage the compositionality of the high-level actions A to generalize to longer plans ^f74ec7

By leveraging the growing library of abstract actions, the agent ideally learns actions that can
be composed to efficiently make long-horizon plans for new tasks under new environment conditions

---
# Description of result

## benchmarks
All use GPT 3.5
![](assets/Pasted%20image%2020240224152752.png)

### Mini minecraft
enormous action space at each time step (>2000 actions,
considering different combinations of items to use) and very long-horizon tasks (26 steps for the most
complex task, even without path-planning.)

To focus on complex crafting, we provide a low-level move-to action to move
directly to specified locations

#### Mining 
all goals involve mining a target item from an appropriate initial resource with an appropriate tool (e.g., Mining iron from iron ore
with an axe), 

#### Crafting
goals involve crafting a target artifact (e.g., a bed), which may require mining a few target resources. 

#### Compositional
combines mining and crafting tasks, in environments that only begin with raw resources and two starting tools (axe and pickaxe). Agents may need to compose multiple skills to obtain other downstream resources

To test action generalization, we report evaluation on the Compositional using only actions learned previously in the Mining and Crafting benchmarks

### ALFRED
As with Minecraft, we provide a low-level method to move the agent directly
to specified locations. While ALFRED is typically used to evaluate detailed instruction following,
we focus on a goal-only setting that only uses the goal specifications. The human-annotated goals
introduce ambiguity, underspecification, and errors with respect to the ground-truth verifiable tasks
(eg. people refer to tables without specifying if they mean the side table, dining table, or desk; a light
when there are multiple distinct lamps; or a cabbage when they want lettuce).

sample tasks: This distribution contains simple tasks that
require picking up an object and placing it in a new location, picking up objects, applying a single
household skill to an object and moving them to a new location (e.g., Put a clean apple on the dining
table), and compositional tasks that require multiple skills (e.g., Place a hot sliced potato on the
counter). We use a random subset of n=223 tasks that we filter to remove completely misspecified
goals (which omit any mention of the target object or skill).

## Results

![](assets/Pasted%20image%2020240222204603.png)

### Learned libraries
Mini minecraft: operators learnt from mining and crafting allowed perfect direct generalization to the Compositional tasks without any additional training on these complex tasks.

ALFRED: 
- we learn operators that correctly specify valid appliances and effects for all necessary intermediate household skills. 
- We also successfully reject many proposed operators that are 
	- redundant (which would make high-level planning inefficient), 
	- inaccurate (including apriori reasonable proposals that do not fit the environment specifications, such as proposing to clean objects with just a towel, when our goal verifiers require washing them with water in a sink), 
	- or underspecified (such as those that omit key preconditions, yielding under-decomposed high-level task plans that make low-level planning difficult).

### planning and generalization
Failures on ALFRED
- The most common is goal misspecification, when the LLM does not successfully recover the formal goal predicate (often due to ambiguity in human language); 
- followed by policy inaccuracy, when the learned policies fail to account for low-level, often geometric details of the environment. 
- More rarely, we notice failures caused by operator overspecification—for instance, the learned SliceObject specifies a specific type of Knife, causing planning failures if that knife type is not available. 

Both operator and goal specification errors could be addressed with more diverse initial proposals.

---
# How it compares to previous work
## Low-level Planning Only
to parse linguistic goals into formal specifications that are solved directly by a planner as in Liu et al. (2023)

"uses an LLM to predict only the symbolic goal
specification conditioned on the high-level predicates and linguistic goal, then uses the low-level
planner to search directly for actions that satisfy that goal"

## **Subgoal Prediction**
as in Ahn et al. (2022) (saycan)

"uses an LLM to
predict a sequence of high-level subgoals (as PDDL pre/postconditions with object arguments),
conditioned on the high-level predicates, and task goal and initial environment state"

**Failure modes:** 
- While the LLM frequently predicts important high-level aspects of the task subgoal structure (as it does to propose operator definitions), 
- it frequently struggles to robustly sequence these subgoals and predict appropriate concrete object groundings that correctly obey the initial problem conditions or changing environment state. 
- These errors accumulate over the planning horizon, reflected in decreasing accuracy on the compositional Minecraft tasks 
- (on ALFRED, this baseline struggles to solve any more than the basic pick-and-place tasks, as the LLM struggles to predict subgoals that accurately track whether objects are in appliances or whether the agent’s single gripper is full with an existing tool.)

## **Code Policy Prediction**
adapts key aspects of Voyager

Instead of PDDL operators, we few-shot prompt the LLM to
predict imperative code policies in Python (with cases and control flow) over an imperative API
that can query state and execute low level actions. As FastDownward abstract planning is no longer
applicable, we also use the LLM to predict the function call sequences with arguments for each task.

**Failure modes:** 
- As with Subgoal Prediction, we find that while the LLM predicts many aspects of the task decomposition, 
- it often struggles to predict an accurate sequence of function calls parameterized with appropriate arguments, conditioned on the initial problem state. 
- In ALFRED, we generally find that relative to a low-level search, imperative code is not well-suited to account for the finer-grained geometric detail of the 3D environment (e.g., checking if various appliances are opened or closed, or figuring out whether large objects can fit in smaller spaces.) 
- The full Voyager implementation includes additional features that would likely improve performance for our baseline and our own model, including code correction given environmental feedback, and finds that more powerful LLMs (GPT-4) significantly improve performance. 
- However, the challenges revealed in our baseline implementation highlight the relative strengths of learning action representations better designed for hierarchical planning.

---
# Main strategies used to obtain results
## Overview
 ![](assets/Pasted%20image%2020240222191717.png)


![](assets/Pasted%20image%2020240222200514.png)

- Use LLM to decompose natural lang task, then propose symbolic definitions for each subtask (algo 1 line 3)
- bilevel planner to compute low level plan (algo 1 line 5), execute (algo 1 line 6)
- score operator from execution results and add to verified library if successful, like voyager (algo 1 line 8)
- To accelerate low-level planning, we simultaneously learn local subgoal-conditioned policies (i.e., the controllers for each operator) to accelerate search (algo 1 line 7)


![](assets/Pasted%20image%2020240224152648.png)

![](assets/Pasted%20image%2020240222191636.png)


## HIERARCHICAL PROMPTING TO PROPOSE OPERATOR DEFINITIONS
(algo 1 line 3)

![](assets/Pasted%20image%2020240222202228.png)
### Symbolic task decomposition
- For a given task $ℓ_t$ and a initial state $x_0$, we first translate the raw state $x_0$ into a symbolic description Φ($x_0$). 
- To constrain the length of the state description, we only include unary predicates in the abstract state (i.e., only object categories and properties).
- Subsequently, we present a few-shot prompt to the LLM and query it to generate a proposed task decomposition conditioned on the language description $ℓ_t$. 
- It generates a sequence of named high-level actions and their arguments, which explicitly can include abstract actions that are not yet defined in the current action library. 
- We then extract all the operator names proposed across tasks as the candidate abstract operators. 
- ==Note that while in principle we might use the LLM-proposed task decomposition itself as a high-level plan, we find empirically that this is less accurate and efficient than a formal planner.==

### Symbolic operator definition. 
- With the proposed operator names and their usage examples (i.e., the actions and their arguments in the proposed plans), we then few-shot prompt the LLM to generate candidate operator definitions in the PDDL format (argument types, and pre/postconditions defined based on predicates in P). 
- ==We also post-process the generated operator definitions to remove predicate names not present in P and correct syntactic errors.==

## GOAL PROPOSAL AND BI-LEVEL PLANNING
(algo 1 line 5)
### symbolic goal proposal
- reminder: each task is associated with a queryable but unknown goal predicate $g_t$ that can be represented as a first-order logic formula $f_t$ over symbolic predicates in P
- few shot prompted LLM to predict candidate goal formulas
### high level planning
for each candidate goal formula, use PDDL planner (FastDownward) to find high level plan $P_A = \{(a_1, o_{1_i}, ...), ..., (a_K, o_{K_i}, ...)\}$ where $a$ is from the current candidate operator library $A'$ with object arguments $o$, such that executing this action sequence would satisfy candidate goal formula according to operator definitions

### low level planning and env feedback
- each tuple $(a_i, o_{1_i}, ...)$ defines a local subgoal $sg_i$ ; search for sequence of low level actions $u_{i_1}, u_{i_2}...$ that satisfies local subgoal
- fixed budget per subgoal, fail early if unable to satisfy local subgoal
- if succeed in finding a complete seq to satisfy all local subgoals, execute all low level actions and query the hidden goal predicate $g_t$ to get env reward (algo 1 line 6)
- implement a basic learning procedure to simultaneously learn subgoal-conditioned controllers over time , but our formulation is general and supports many hierarchical planning schemes

## POLICY LEARNING AND GUIDED LOW-LEVEL SEARCH
algo 1 line 7
![](assets/Pasted%20image%2020240224163523.png)

## Score LLM operator proposals
algo 1 line 8

define $b$ as num of times it appears in high level plan

define $s$ as number of times it appears in successful execution sequence

each operator candidate receives a $score= \frac{s}{b}$

retain operators if $b$ and $score$ (i assume its score; the paper says $a/b$ but it doesnt make sense; probably typo) exceed thresholds

Note that this scoring procedure learns whether operators are accurate and support low-level planning
independently of whether the LLM-predicted goals $f^′_t$ matched the true unknown goal predicates $g_t$.

---
# Other

## Limitations
include core problems in general hierarchical planning not addressed here: 
- we assume access to symbolic predicates that represent the environment state; 
- do not yet tackle finer-grained motor planning
- only consider one representative pre-trained LLM (but not GPT-4, or an LLM finetuned for extended planning.)

### appendix stuff
- sampling directly from the token probabilities defined by this few-shot prompt does not produce sufficiently diverse definitions for each operator name. We instead directly prompt the LLM to produce up to N distinct operator definitions sequentially
- We find that GPT 3.5 frequently produces syntactically invalid operator proposals – proposed operators often include invent predicates and object types that are not defined in the environment vocabulary, do not obey the predicate typing rules, or do not have the correct number and types of arguments
	- While this might improve with finetuned or larger LLMs, we instead implement a simple post-processing heuristic to correct operators with syntactic errors, or reject operators altogether: 
	- as operator pre and postconditions are represented as conjunctions of predicates, we remove any invalid predicates (predicates that are invented or that specify invalid arguments); 
	- we collect all arguments named across the predicates and use the ground truth typing to produce the final args
	- we reject any operators that have 0 valid postcondition predicates. 
	- This post-processing procedure frequently leaves operators underspecified (e.g., the resulting operators now are missing necessary preconditions, which were partially generated but syntactically incorrect in the proposal); 
	- we allow our full operator learning algorithm to verify and reject these operators
## Future work

might generally tackle these problems by asking how else linguistic knowledge and increasingly powerful or multimodal LLMs could be further integrated here: 

- to propose useful named predicates over initial perceptual inputs (e.g., images)
- speed up planning by bootstrapping hierarchical planning abstractions using the approach here, but then to progressively transfer planning to another model, including an LLM, to later compose and use the learned representations

## Details

### A.1.2 POLICY LEARNING AND GUIDED LOW-LEVEL SEARCH
Concretely, we implement our policy-guided low-level action search as the following. We maintain
a dictionary $D$ that maps subgoals (a conjunction of atoms) to a set of candidate low-level action
trajectories. When planning for a new subgoal $sg$, if $D$ contains the trajectory, we prioritize trying
candidate low-level trajectories in D. (==similar to reference plan in GITM==)

Otherwise, we fall back to a brute-force breadth-first search
over all possible action trajectories. To populate D, during the BFS, we compute the difference in the
environment state before and after the agent executes any sampled trajectory and the corresponding
trajectory $t$ that caused the state change. Here the state difference can be viewed as a subgoal $sg$
achieved by executing t. Rather than directly adding the $(sg, t)$ as a key-value pair to D, we lift the
trajectory and environment state change by replacing concrete objects in sg and t by variables. Note
that we update D with each sampled trajectory in the BFS even if it doesn’t achieve the subgoal
specified in the BFS search.

When the low-level search receives a subgoal sg, we again lift it by replacing objects with variables,
and try to match it with entries in D. If D contains multiple trajectories t for a given subgoal sg, we
track how often a given trajectory succeeds for a subgoal and prioritize trajectories with the most
successes.


