---
date: 2024-04-01
time: 12:45
author: 
title: LANGUAGE AGENT TREE SEARCH UNIFIES REASONING ACTING AND PLANNING IN LANGUAGE MODELS
created-date: 2024-04-01
tags:
  - AI
  - decision-procedures
paper: 
code: https://github.com/andyz245/LanguageAgentTreeSearch
zks-type: lit
---

MCTS style for planning-based decision making (eg ToT)

# Description of result

## HotPotQA
multihop reasoning QA. Use API calls to search and lookup info as in ReAct. Evaluate on subset of 100 qns

![](assets/Pasted%20image%2020240403121612.png)

- increasing $n$ helps
- combining ext feedback in augmenting reasoning helps

## Programming
- HumanEval (full 164 qns), MBPP (subset of 397 probs)
- use LLM to generate synthetic test suite of assert statements for each question as in CodeT
- "reasoning and acting baselines share an action space, but acting methods are able to incorporate observations as additional context."
- "For LATS, since each action corresponds to a complete solution, we skip the simulation step of LATS and directly use the percentage of passed tests as the backpropagated reward."
- After the search is completed, select the one with the highest value for eval
- "both search and semantic feedback are crucial for better performance"
![](assets/Pasted%20image%2020240403121627.png)

## WebShop
- score: reflecting the percentage of user-specified attributes met by the selected product
- success rate: exact match for all conditions

![](assets/Pasted%20image%2020240429211015.png)

- "Sampling k = 30 trajectories with ReAct and Reflexion results in a similar performance, suggesting the semantic feedback is not as helpful in complex environments like WebShop"
- "like in Shinn et al. (2023), we find that generated reflections are often generic and do not provide useful feedback, resulting in a tendency for the agent to become stuck in local minima."


---
# How it compares to previous work
![](assets/Pasted%20image%2020240403121519.png)

(shouldn't Beam Search have a tick instead of cross for Planning?)

![](assets/Pasted%20image%2020240403125257.png)

![](assets/Pasted%20image%2020240403134207.png)

## Limitation of prev works
- Base prompting methods (CoT or ReAct) autoregressively sample from the LM, neglecting potential alternative continuations from specific states.
- Reasoning-based methods (CoT, RAP, or ToT) rely solely on the internal representations of the LM and  do not consider external observations. Vulnerable to hallucination and error propagation
- Current planning frameworks (RAP or ToT) use simple search algorithms such as BFS or cannot leverage environmental feedback to improve planning.
- agent is static and cannot reuse previous experience or learn from trial and error. While RAP also adopts MCTS, it is constrained to tasks where the LM can become a world model and accurately predict states.

## Baselines used in results
Internal reasoning (rely solely on the agent’s existing knowledge)
- ToT with DFS search and LM score
- CoT
- CoT-SC
- ToT
- RAP
- LATS reasoning-only ver with CoT base prompt

acting-based methods ("augment the agent with the interactive API environment and primarily evaluate its information retrieval abilities")
- ReAct
- Reflexion
- Combination: LATS w CoT base prompt, then switch to ReAct base prompt upon failure ("This is closer to how humans might approach this task, by using tools to lookup additional information only when the answer is not already known.")

## Drawbacks
- due to multiple API calls in search tree, costs are usually higher than other methods

---
# Main strategies used to obtain results
MCTS where LLMs act as as an agent, value function, and optimizer (feedback generator / critic?).

## MCTS Prelude

MCTS runs for k episodes; for each episode, it starts from the root (i.e., initial state) and iteratively conducts
1) Expansion, where multiple children states s are explored from the current parent state $p$ by sampling $n$ actions
2) Selection, where the children with the highest UCT is selected for expansion in the next iteration:

$$
UCT(s) = V (s) + w
\sqrt{\frac{ln\:N(p)}
{N(s)}}
,
$$

where $N(s)$ is the number of visits to a node $s$, $V (s)$ is the value fn from the subtree of $s$, $w$ is the exploration weight, and $p$ is the parent node of $s$.

Backprop at the end of each episode: the return $r$ is used for updating every $V (s)$ along the path via:
$$
V (s) \leftarrow \frac{V(s)(N(s)−1)+r}
{N(s)}
$$
## LM agent
- Follow ReAct instantiation where action space consists of permissible actions and language space of reasoning traces (thoughts)
- sample $n$ actions from LM: "This is based on the intuition that for complex decision-making tasks, there is likely to be a range of potential trajectories or reasoning paths that are correct (Evans, 2010)."
	- mitigates stochastic nature of LM generation
	- greater exploration in both decision and reasoning space

## LATS

![](assets/Pasted%20image%2020240403135447.png)

- each node is a state $s$ comprising the original input $x$ and the action and obs sequence

**Selection**: starting from root node, a child node is selected at each tree level until a leaf node is reached, via UCT algo above

**Expansion**: sample $n$ actions to run in the env (so $n$ new child nodes added to tree). tree stored in external LT mem structure

**Evaluation**: LM gives a score to the trajectory

**Simulation**: "expands the currently selected node until a terminal state is reached. At each depth level we sample and evaluate nodes with the same operations, but prioritize nodes of highest value.". Search terminates upon task success, else the next 2 steps are performed:

**Backpropagation**: For each node in the trajectory from root to leaf, backprop the values via eqn above, except they update $N$ first before updating $V$

**Reflection**: when terminated unsuccessfully, prompt with traj and final reward for self-reflection that summarizes the errors in the reasoning or acting process and propose better alternatives. failed trajectories and reflections are stored in memory, to be used in subsequent iterations for additional context to agent and value fn.

![](assets/Pasted%20image%2020240403121705.png)


---

# Other
- tree-based search in LMs is convenient as backing up to any state is just reusing stored prompts, while in RL reverting to a past state may not be as easy depending on env
- advantage: Modularity: "The base LM agent, reflection generator, and value function can be independently altered and adapted to individual LM properties."

## Ablations
section 5.4 and Appendix C
### HotPotQA
![](assets/Pasted%20image%2020240429211031.png)
- ablating self reflection causes a 0.05 performance drop. "This is a smaller gain Reflexion (Shinn et al., 2023) observes over ReAct (Yao et al., 2023b) as shown in Tab. 2, suggesting overlap between the types of questions where there is an improvement with self reflection and search."
- search algorithm (MCTS) impt: when replaced with DFS, performance drops by 0.08
- Most qns can be answered within 4 steps, and increasing the steps can force the agent into local minima n rarely improves success
- Removing the LM value fn results in a ==0.24 drop in performance== (EM), showing its importance
#### comparing complexity and cost
![](assets/Pasted%20image%2020240429223709.png)

### HumanEval
- vary $k$ (num traj sampled) on HumanEval as effect is more noticable
- LATS scales better than Reflexion
![](assets/Pasted%20image%2020240429223424.png)