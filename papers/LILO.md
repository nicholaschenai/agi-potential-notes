---
date: 2024-02-13
time: 16:00
author: Gabriel Grand and Lionel Wong and Matthew Bowers and Theo X. Olausson and Muxin Liu and Joshua B. Tenenbaum and Jacob Andreas
title: "LILO: LEARNING INTERPRETABLE LIBRARIES BY\r COMPRESSING AND DOCUMENTING CODE"
created-date: 2024-02-13
tags:
  - AI
  - library-learning
  - program-synthesis
source: https://arxiv.org/abs/2310.19791
code: https://github.com/gabegrand/lilo
---
Neuro-symbolic framework to synthesize, compress n document code to build libraries. Our experiments are designed to simulate a “lifelong learning” setting where the learner starts from a small seed set of simple examples that it must generalize to solve a broader set of training tasks that range in complexity.

---
# Description of result
## Intro
- Eval on inductive program synthesis for 
	- string editing (REGEX), 
	- scene reasoning (CLEVR)
	- graphics composition (LOGO).

**Online synthesis**: each model runs for a fixed number of iterations, continually updating its library (if applicable) and attempting to solve test tasks. (main expt)

**Offline synthesis**: final library from each online synthesis run frozen, perform enumerative search (hyperparams of search are fixed) with no language guidance for a fixed time budget. Serves as controlled expt for show usefulness n comparison of learned libraries in the absence of language guidance.

![](assets/Pasted%20image%2020240216151403.png)

## Online synthesis
![](assets/Pasted%20image%2020240216151428.png)


## Offline synthesis: quality of libraries
![](assets/Pasted%20image%2020240217144150.png)

Baseline: Base DSL is just the base library. This baseline can also be viewed as corresponding to the performance of a non-library learning model, such as the LLM Solver, on this evaluation. 

Results show that LILO learns high-quality libraries that generalize well to downstream synthesis tasks even in the absence of language guidance.

---
# How it compares to previous work

![](assets/Pasted%20image%2020240216173555.png)

Intuitively, Eq. 2 is optimized by inventing abstractions that are both reusable, simplifying the solutions to multiple tasks in T ; and concise, ideally building on one another hierarchically so as to share logic

## vs DreamCoder
- "... search procedure is extremely computationally intensive, requiring more than two CPU-months just to learn a single domain": tractability of this approach hinges critically on the ability to do efficient refactoring
- "Much of this search time is spent 'getting off the ground': discovering a basic set of abstractions that human programmers typically already know, or might be able to grok quickly based on having solved problems in other domains."
- "... DreamCoder libraries are not necessarily interpretable, requiring both domain expertise and knowledge of lambda calculus to decipher."
- On all three domains, LILO solves more tasks than DreamCoder and learns empirically richer libraries containing abstractions that are intractable to discover with existing methods. eg concept of vowel in fig 2

## vs LAPS
- foo

## General
- Prior analyses (on [Stitch](https://stitch-bindings.readthedocs.io/en/stable/), a library compression module) were limited to static program corpora; in LILO, we perform the first experiments using Stitch as part of a program synthesis loop. We find Stitch similarly performant on our domains, typically running in seconds on a single CPU, enabling us to re-derive the entire library from base library at every iteration (Alg. 1 line 9). 
- LILO rivals symbolic search in terms of computational efficiency. Compared to enumerative search, we find that we are able to achieve faster overall wall clock runtimes with LLM-based search due to orders of magnitude better sample efficiency (see A.10). in terms of dollar cost, one round of LLM synthesis is equivalent to 48 CPU-hours of DreamCoder search.
- A key advantage of LILO is the ability to perform enumerative search, which aids in the discovery novel programs that differ structurally from existing solutions in the LLM prompts.
- main LILO variant integrates both LLM and enumerative search to achieve the highest solve rates of all models we evaluated.
- Unlike LLM-only baselines—which can solve similar tasks—LILO compresses this knowledge (concepts) into symbolic abstractions that are useful for both traditional search methods and LLM-guided synthesis.

---
# Main strategies used to obtain results
## Intro
- Thinking fast n slow: As in DreamCoder and LAPS, we use a task-conditioned PCFG (probabilistic ctx free grammar) inferred by a multi-layer perceptron to perform “slow” enumerative search in program space. Additionally, we introduce a “fast” approximate search model in string space that leverages the strong inductive biases learned by LLMs

![Pasted image 20240213192140](assets/Pasted%20image%2020240213192140.png)
- algo loop
	- Dual search (conditional distribution over programs) for synthesis: 
		1. LM guided for domain priors (algo line 5-6) and 
		2. enumerative for domain specific expressions (algo line 7)
	- STITCH symbolic compression (line 8) across large code corpora n generated solns to identify useful abstractions.
	- Auto documentation (algo line 11-13) based on usage examples

## Dual search
### LM guided search
- Start with a large model that already has strong priors over the joint distribution of language and code
- adapt the library to resemble this distribution by building up contextual examples and documentation. 
	- In contrast to simply picking a more common L (e.g., Python) to work in, this procedure enables us to learn a new L on-the-fly that is both optimized for the domain and grounded in natural language.

## Refactoring via Stitch compression
### Stitch Intro
(Bowers et al., 2023): a symbolic compression system that uses branch-and-bound search to identify reusable abstractions in large datasets of lambda calculus programs

### Problem statement
![](assets/Pasted%20image%2020240216175544.png)
- cast refactoring as a compression problem over a corpus of programs (eqn 4) where the goal is to identify abstractions with minimal description length |f| that facilitate efficient rewriting of Π. 
- necessitates an efficient search strategy
### Using Stitch
- leverage recent algorithmic advances from STITCH, 1000–10000x faster and 100x more memory efficient than DreamCoder’s compression algorithm.
- Efficiency enables rebuilding libraries from base library every iteration: While many abstractions remain stable across iterations, this “deep refactoring” allows LILO to discard suboptimal abstractions discovered early in learning.
### AutoDoc
- improves interpretability
- helps the LLM synthesizer use the library more effectively
	- Early expts: naively providing a LLM with Stitch abstractions measurably degraded its ability to solve tasks (§4.1). 
	- We hypothesized that rewriting program examples in terms of these anonymous lambdas during the compression step was acting as a form of code obfuscation
	- Unlike traditional program synthesis methods, LMs draw inferences about the semantics of programs from their lexical content (eg care what functions are named)

---
# Discussion
- Results suggests LLMs can reduce the need for exhaustive (symbolic) search in synthesis domains where language annotations are available.

## Weakness
- While the GPT models do a remarkable job at inferring program semantics, we observe various cases where they produce unclear or even incorrect documentation. For instance, in LOGO (Fig. 5), the two polygon functions (fn 27 and fn 34) are assigned relatively uninformative names that emphasize their implementation (looping move and rotate) but not their behavior (drawing polygons).
- Moreover, because AutoDoc works sequentially, it occasionally “doubles down” on particular statements that may be correct in one context but not another. For example, AutoDoc correctly notes that fn 27 works by “incrementing the angle of each side on each iteration,” but this idea is ambiguous in fn 31 (which angle?) and incorrect in fn 34 (the length is constant, not doubling). 
- In addition to affecting interpretability, these semantic errors may also impact downstream synthesis performance in LLM-guided search. Future work could adopt self-consistency and verification techniques (Wang et al., 2022; Dhuliawala et al., 2023) to improve the quality of AutoDoc generations.

## On LLMs
- LLM-only baseline also demonstrates the ability to bootstrap its performance over time
	- aligns with recent successes in automated prompting (Zhou et al., 2023; Yao et al., 2022), 
	- suggesting that transformer attention can be viewed as implementing a form of non-compressive library learning where task relevant information is accumulated in the prompt.
- challenging to scale to large software libraries: as context length grows, key information may be “lost in the middle” or ignored due to ordering effects (O’Connor & Andreas, 2021; Lu et al., 2022; Liu et al., 2023). 
	- important line of research looks to develop new strategies for equipping LLMs with long-term memory through retrieval (Wu et al., 2022; Park et al., 2023), self-reflection (Shinn et al., 2023), or combinations of both that enable learning libraries of programmatic skills in embodied environments (Wang et al., 2023). 
	- Currently, these approaches face the common challenge of determining what information to preserve, leading to a large space of ad hoc heuristics

---

# Other

- In main expt, task descriptions were autogen from GT. in appendix 6, they also try the techniques on task descriptions from humans
- for few shot prompting, still need to select task examples. surprisingly using ada ebd is on par with random selection (tradeoff in performance between some tasks). they also noted that ebd might be sensitive to superficial lexical overlap (eg inclusion of an irrelevant but similar keyword boosts its similarity ranking)
- avg ~ 2$ per iteration n only runs for like 15 iterations per dataset
- see Leveraging language to learn program abstractions and search heuristics (LAPS) for annotations. Their LOGO results are anomalously higher than LILO and DreamCoder due to token-to-token assumptions made by the IBM translation model.

---

# Implementation notes
- enumerative search done in `infer_programs_for_tasks()` which uses dreamcoder's `dreamcoder.enumeration.multicoreEnumeration`, which leads to `grammar.py` for the enumeration for python
- stitch compression in `get_compressed_grammar_mdl_prior_rank`