---
date: 2024-12-16
time: 17:20
author: Nate Derbinsky
title: "MIT AGI: CA (Nate Derbinsky)"
created-date: 2024-12-16
tags: 
source: https://www.youtube.com/watch?v=bfO4EkoGh40
zks-type: lit
---
- CA specifies aspects of cognition that remain constant across lifetime of agent
	- memory systems of agent's beliefs, goals, exp
	- knowledge repr
	- functional processes that lead from perception to behavior
	- learning mechanisms
- Psychological modeling
	- ACT-R
	- CLARION
	- EPIC
- Agent functionality
	- Soar
	- Sigma (Rosenbloom)
		- reimplement soar w factor graphs n msg passing 
	- LIDA
	- ICARUS
	- Companions
- Declarative mem: named so cos you can describe (declare) what it is, vs procedural which can be harder eg 'how to perform balance'

## Soar research foci
- arch enhancement, must be
	- useful across a wide variety of agents
	- task independent
	- efficient
- agent dev
	- explore bounds of arch commitments/integration
	- solve interesting probs
## Memory research
- clue: "Rational analysis of mem" (Anderson et al. 2004): 
	- Frequency + recency of use (base level activation)
		- log decay since time of last use $ln(\sum_{j=1}^{n}t_j^{-d})$
			- implementation (since decay over many mem is computationally exp)
				- Bound $n$, approx tail effects (Petrov 2006)
				- only need relative ranking: re-compute on update of mem
- works well in word sense disambiguation (AAAI 2011)
- efficiency: new algos to scale (ICCM 2010)
- empirical: approach yielded beneficial behavior across architectural mechanisms n tasks (ACS 2012, CSR 2013)
	- Semantic LTM retrieval: word sense disambiguation
	- working mem decay: robot navication
	- procedural decay: RL value fn repr in dice game
### Related prob: mem size
- mobile robotics: Large working mem -> large episodes -> long time to reconstruct exp
- liars dice: 
	- large procedural mem = limiting agent lifetime
	- sparsely representing an enormous value fn
#### forgetting
hypothesis: rational to forget (working memory?) if
- not useful via base lvl activation and
- likely can reconstruct if necessary
##### idea: if mem below base activation thr, forget it
- efficient implementation
	- underestimate time of decay
		- approx as sum of decay of individual accesses (can compute exactly)
	- at predicted time
		- if below thr, forget
		- else, re-estimate
- result for mobile robotics: latency beats handcrafted rules with right hyperparam
- for liars dice (RL, so the state space is v large n need decay)
	- latency in the middle of prior work (KT 07) vs baseline
	- but performance (win rate) comparable to baseline (no forgetting) while KT 07 performance suffers
- risk: make change in WM but did not learn to LTM n forgetting deletes it 

## Recommended reading
- Unified theories of cognition
- The soar CA 2012
- How to build a brain by eliasmith
- How can human mind occur in the physical universe (anderson, ACT-R)
- CA: research issues and challenges (Langley et al)

## QA
- tip: only process changes (just like biology)
	- enables scaling to billions of rules