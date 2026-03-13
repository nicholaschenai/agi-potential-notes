---
date: 2025-05-13
time: 15:11
author: Yichao Liang, Nishanth Kumar, Hao Tang, Adrian Weller, Joshua B. Tenenbaum, Tom Silver, João F. Henriques, Kevin Ellis
title: "VisualPredicator: Learning Abstract World Models with Neuro-Symbolic Predicates for Robot Planning"
created-date: 2025-05-13
tags:
  - neuro-symbolic
paper: https://arxiv.org/abs/2410.23156
code: 
zks-type: lit
---
## Description of result
- Introduce Neuro-Symbolic Predicates: snippets of python code, state representation that combines both symbolic representation and VLM use to query perceptual properties of the state. 
	- interpretable and compositional
![](assets/Pasted%20image%2020260216212520.png)
Note: def-op is the high level (lifted) operator, option is the Hierarchical RL term (not PDDL) which points to the low level skill policy that executes in the env
- Outline an online algo for inventing NSPs and operators (High Level Actions (HLAs)), building up a world model
	- potential for continual/lifelong learning
- Eval in various pybullet envs to achieve higher solve rates with lower compute in general

![](assets/Pasted%20image%2020260220172120.png)

---
## How it compares to previous work
- Traditional robot task planning uses hard-coded symbolic world models, brittle and doesnt generalize well. VisualPredicator takes the learning approach to generalize better
- **Recent Works with Limited Learning:** While some recent works have introduced learning, they often **restrict perceptual and logical abstractions** and necessitate **demonstration data** rather than enabling the robot to explore autonomously. The presented NSPs offer a much broader set of perceptual primitives (anything a VLM can perceive) and deeper logical structures (anything computable in Python).
- **Comparison to Silver et al. (2023):** This research builds upon predicate learning but **advances by inventing open-ended, visually and logically rich concepts without relying on hand-selected features**, and it learns **purely online** unlike their demonstration-based approach.

### Benchmark
![](assets/Pasted%20image%2020260220172015.png)
![](assets/Pasted%20image%2020260220211142.png)
- 5 robotic envs in Pybullet, averaged over 5 random seeds.
- per seed, sample 50 test tasks that feature more objs and more complex goals than those during training
- 10 tasks in Cover Heavy and Balance, and 20 tasks in Blocks. The planning budget $n_{abstract}$ is set to 8 for all domains except Coffee, where it is set to 100

### Baselines
![](assets/Pasted%20image%2020260220211212.png)

### Results

![](assets/Pasted%20image%2020260220172032.png)
- consistently outperforms HRL and VLM baselines MAPLE and ViLa, achieving near perfect solve rates
- MAPLE struggles to perform well even on in-distribution tasks
- ViLa demonstrates limited planning capabilities
	- tends to attempt redundant actions (overfitting?)
	- in more complex domains, generates infeasible plans (limited planning capabilities)
- Comparison w No invent suggests benefits of learning predicate abstractions over initial underspecified repr
- Similar solve rates and efficiency to Oracle, suggests their algo can autonomously discover abstractions as good as human experts
- Comparison with Sym pred shows how derived predicates and VLM usage (for perception to infer state) are impt.
	- without derived predicates, struggles with Balance task as it needs higher level concept
- Comparison w Ablate Op shows how conservative leads to overfitting (learns unnecessarily complex preconditions that overfit early), and thus optimistic is preferred for data-scarce, exploration-based learning



![](assets/Pasted%20image%2020260220172144.png)


---
## Main strategies used to obtain results
### Definitions
#### Task
Objects, initial state, goal. 
- state includes raw RGB image and associated obj features like 3D obj pos
#### Predicate (learnt)
given $m$ objects, return bool of a state
#### Skills (Options)
Predefined low-level motor controllers parameterized by objects (e.g., `Pick(?block)`, `Place(?block, ?table)`). **Given, not learned.** Each HLA wraps exactly one skill.
#### High-Level Action (HLA) / Operator (derived from predicates)
Augments a skill $\pi$ with structure for planning

$$\underline{\omega} = \langle \pi, \text{PRE}, \text{EFF}^+, \text{EFF}^- \rangle$$

- **PRE**: preconditions: which predicates must hold for the skill to apply
- **EFF$^+$**: add effects: predicates that become true after execution
- **EFF$^-$**: delete effects: predicates that become false after execution

**Lifted** ($\omega \in \Omega$): general template with typed object variables.  
**Grounded** ($\underline{\omega} \in \Omega_\mathcal{O}$): applied to specific objects.
#### Planning
- A **high-level plan** is a sequence of grounded HLAs $\underline{\omega}_1, \dots, \underline{\omega}_n$.
- Converted to a **low-level plan** by executing each HLA's corresponding skill.

### Neuro-Symbolic Predicates
![](assets/Pasted%20image%2020260220171928.png)
- Combine programming lang constructs with API calls to VLMs to evaluate visually-grounded NL assertions
- can be grounded in visual perception and also in proprioceptive and object-tracking features e.g. object poses
#### Primitive NSPs
evaluated directly on raw state. 
##### APIs
- `get_object(t: Type)`: returns all objects in the state of a type $t$. 
- `get(o: Object, f: str)` retrieves the feature with name $f$ for object $o$. 
- `crop_to_objects(os: Sequence[Object], ...)`: crop the state observation image to include just the specified list of objects to reduce the complexity for downstream visual reasoning. 
- `evaluate_simple_assertion(a: str, i: Image)` for evaluating the natural language assertion $a$ in the context of image $i$ using a VLM.

##### Evaluation
Since single img may not uniquely identify the state (e.g. due to occlusion), extra context is provided to VLM:
- previous visual observation, action (action comes after obs)
- previous truth values for queried ground atom

![](assets/Pasted%20image%2020260220172057.png)

Also, set of marks prompting (each obj has unique ID overlaid)

#### Derived NSPs
- determine truth value based on that of other NSPs, 
- analogous to derived predicates in planning
- are not in HLA postcondition as it can be calculated from primitive NSPs, tho it can be in HLA precondition

### Hierarchical planning
- The learned abstract world model is used to generate a **high-level plan (sequence of HLAs)**, which is then translated into a low-level action sequence by calling corresponding skill policies.
	- High lvl planning uses fast symbolic planners e.g. A* search with heuristics like LM-Cut
- Two failure modes drive learning:
	- **Infeasible**: a constituent skill fails to execute.
	- **Not satisficing**: all skills execute successfully, but the goal is not achieved.
- when solving a task, generate a stream of high lvl plans and execute till a satisficing plan (achieves the goal) is generated or planning budged $n$ reached
### Online predicate invention algorithm -- Learning abstract world model from env interaction

![](assets/Pasted%20image%2020260220171941.png)
- $\Psi$ predicate set, initialized with goal predicates
- $\Omega$ set of HLAs, empty init
- $\mathcal{E}$ env
- $\mathcal{T}$ tasks
- $\mathcal{D}$ dataset

#### Exploration (Section 5.1)
- The agent plans using its current predicates and HLAs and executes these plans.  
	- HLA initially mostly empty but authors mention alternatively can explore thru random skill selection
- collects data by attempting the tasks (generate and execute plans till task solved or budget reached), recording 
	- successfully executed skills (positive transitions), 
	- failed skill executions (negative state-action tuples), 
	- and satisficing plans.

#### Predicate Proposal (Section 5.2) 
VLMs are prompted using these strategies:
- **Strategy #1 (Discrimination):** Prompts VLMs with examples of skill success and failure to discover good preconditions for skills.
- **Strategy #2 (Transition Modeling):** Prompts VLMs with "before" and "after" snapshots of successful skill execution to describe properties that changed, helping discover postconditions.
- **Strategy #3 (Unconditional Generation):** Prompts VLMs to propose logical extensions (e.g., negation, universal quantification, transitive closure, disjunction) of existing predicates, for creating derived predicates.

#### Predicate Set Selection (Section 5.3, appendix B.3)
- Problem: Typically, one might not find any satisficing plans early in learning predicates, especially so when space of possible plans is large (e.g. long horizon and many potential actions)
	- so need predicate score fn that doesnt rely on satisficing plans, which they introduce below
		- based on classification accuracy of HLAs and simplicity bias
- Assume no demonstration data, so techniques that require expert demonstrations arent suitable early on
	- e.g. Propose-then-select (Silver et al. 2023), an objective that considers planning efficiency and simplicity
- Once enough satisficing plans are found, score switches to Propose-then-select
	- Note: this hyperparam ('enough' satisficing plans) wasnt stated in the paper
- A **greedy best-first search** is used to select the best predicate set, using either score function as the heuristic
	- experiments: authors found that search space small enough that enumeration takes a few mins on single CPU. suggests local hill climbing for larger search spaces

##### The objective (eqn 5):

$$
J(\Psi) = \frac{1}{|\mathcal{D}_\Psi|} \left[ \sum_{(s^{(k)}, \pi^{(k)}, s^{(k)}_\pi) \in \mathcal{D}_\Psi^+} \mathbb{1}\!\left(\exists\, \omega.\pi = \pi^{(k)},\; \omega.\text{PRE} \subseteq s\right) + \sum_{(s^{(k)}, \pi^{(k)}, \text{FAIL}) \in \mathcal{D}_\Psi^-} \mathbb{1}\!\left(\nexists\, \omega.\pi = \pi^{(k)},\; \omega.\text{PRE} \subseteq s\right) \right] + \alpha \cdot |\Psi|
$$

| Symbol               | Meaning                                     |
| -------------------- | ------------------------------------------- |
| $\Psi$               | The candidate predicate set being scored    |
| $\Omega_\Psi$        | The HLAs learned using predicate set $\Psi$ |
| $\mathcal{D}_\Psi^+$ | Successful transitions                      |
| $\mathcal{D}_\Psi^-$ | Failed transitions                          |

- first term: For each successful transition, is there **some** learned HLA for that skill whose preconditions are satisfied? (+1 if so) ("The model correctly predicts this skill should work here.")
- second term: For each failed transition, is there **no** learned HLA for that skill whose preconditions are satisfied? (+1 if so) ("The model correctly predicts this skill should fail here.")
- last term is a penalty term, prefer fewer predicates (eqn seems to have typo or $\alpha$ is negative)

#### Learning High-Level Actions (HLAs) (Section 5.4, Appendix B.2)
- extends the **cluster and intersect operator learning algorithm** (Chitnis et al., 2022)
	- cluster and intersect assumes given demo trajectories and learns restricted preconditions so the plans are most similar to the demos, but we do not have demo trajectories
- This extension improves the precondition learner 
##### Aim (B.2 eqn 3)
To learn high level actions $\Omega$ such that ...

###### Line 1
- $\forall (s^{(k)}, \pi^{(k)}, s^{(k)}_\pi) \in \mathcal{D}_\Psi$  "For every successful transition in the dataset..."
- $\exists\, \omega \in \Omega_\mathcal{O}^{\pi^{(k)}}$  "...there must exist at least one HLA (for that skill) such that..."
- $\omega.\text{PRE} \subseteq s^{(k)}$ "...the HLA's preconditions were satisfied in the starting state..."
- $s^{(k)}_\pi - s^{(k)} = \omega.\text{EFF}^+$  "...the predicates that became newly true match the HLA's add effects." (Set difference: what's in the new state but wasn't in the old state.)
- $s^{(k)} - s^{(k)}_\pi = \omega.\text{EFF}^-$ "...the predicates that stopped being true match the HLA's delete effects."

**In plain English:** Every success must be accounted for by some rule.

###### Line 2
- "**any** rule that *claims* to apply ($\omega.\text{PRE} \subseteq s$) must make correct predictions ($\Rightarrow$ the effects match)."

**In plain English:** Rules don't make false predictions on the training data.

Lines 1 and 2 prevent contradictions you can't have an HLA whose preconditions are met but whose effects are wrong.

###### Line 3
**In plain English:** If it failed, no rule should have predicted it would work.

...

while minimizing syntactic complexity of HLA via preferring fewer preconditions and effects

##### Partitioning
- ==TODO: review this part again, mainly the split dataset according to skills, then split each skill into HLA, need to check==
- Split dataset according to skills as each HLA is only associated with one skill
- Then split each skill into one or multiple HLA by 
- One skill (e.g., `Pick`) might behave differently in different situations. (e.g. Picking a block off a table vs. picking a block off another block have different effects.), so you might need multiple HLAs for one skill.


$$
\mathcal{D}_\Psi^\omega = \left\{ d : d \in \mathcal{D}_\Psi \;\wedge\; d.\pi = \omega.\pi \;\wedge\; d.s^{(k)}_\pi - d.s^{(k)} = \omega.\text{EFF}^+ \;\wedge\; d.s^{(k)} - d.s^{(k)}_\pi = \omega.\text{EFF}^- \right\}
$$

$$
\text{where } \omega = \omega(o_1, o_2, \ldots), \text{ for all } o_i \in \mathcal{O}
$$

- $d : d \in \mathcal{D}_\Psi$ : "all data points $d$ from the dataset, such that..."
- $d.\pi = \omega.\pi$ : "the skill used in this data point is the same as this HLA's skill." If the HLA is about `Pick`, only look at `Pick` transitions.
- $d.s^{(k)}_\pi - d.s^{(k)} = \omega.\text{EFF}^+$ : "the predicates that became newly true match this HLA's add effects."
- $d.s^{(k)} - d.s^{(k)}_\pi = \omega.\text{EFF}^-$ : "the predicates that stopped being true match this HLA's delete effects."

The **`where` line** reminds you that these HLAs are *grounded* -- the abstract variables like `?block` are filled in with concrete objects like `block3`.

**In plain English:** $\mathcal{D}_\Psi^\omega$ is the subset of transitions that this specific HLA explains -- same skill *and* same effects.

Postconditions learned from unifying and lifting the effects of data in $\mathcal{D}_\Psi^\omega$ (See cluster and intersect paper Chitnis et al. 2022)

##### Precondition learning (their contribution)
maximize

$$
J(\omega.\text{PRE}) = \frac{1}{|\mathcal{D}_\Psi^{\omega.\pi}|} \left[ \sum_{d \in \mathcal{D}_\Psi^{\omega}} \mathbb{1}\!\left(\omega.\text{PRE} \subseteq d.s^{(k)}\right) + \sum_{d \in (\mathcal{D}_\Psi^{\omega.\pi} - \mathcal{D}_\Psi^{\omega})} \mathbb{1}\!\left(\omega.\text{PRE} \not\subseteq d.s^{(k)}\right) \right] + \alpha \cdot |\omega.\text{PRE}|
$$

- where the first sum counts: from the transitions that belongs to **this** HLA, how many of them have the preconditions satisfied in the starting state ("Correctly predict that this HLA applies in states where it actually does.")
- the second sum counts: For transitions using the same skill but **not** explained by this HLA (either they failed or had different effects), are the preconditions **not** satisfied? ("Correctly predict that this HLA does NOT apply in states where it shouldn't.")
- last term is a regularizing term (alpha should be negative) to prefer fewer preconditions, making HLA applicable in more states, making the model **optimistic** and encouraging exploration of untested situations.

- foo
	- ==TODO== to ensure all data in a partition is modeled by the associated HLA and that the precondition is not satisfied if a skill is inapplicable or has different effects. 

- Precondition learner ensures each data in transition dataset is modeled by only one HLA


---

## Other
TODO: LM cut

