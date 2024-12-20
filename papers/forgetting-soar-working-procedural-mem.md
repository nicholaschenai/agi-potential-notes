---
date: 2013-01-05
time: 08:17
author: Nate Derbinsky, John E. Laird
title: Effective and efficient forgetting of learned knowledge in Soar’s working and procedural memories
created-date: 2024-12-20
tags: 
paper: https://www.sciencedirect.com/science/article/pii/S1389041712000563
code: 
zks-type: lit
---
==Note: mostly AI summarized with some edits==

also see [mit-agi-ca-nate-derbinsky](mit-agi-ca-nate-derbinsky.md) where author presents this work

### Summary of the Work Including Key Contributions

The research work focuses on addressing the challenge of managing learned knowledge in cognitive models, particularly in the Soar cognitive architecture, during complex and temporally extended tasks. The key contribution is the development and evaluation of forgetting policies for Soar's working and procedural memories. These policies are based on the concept of base-level activation, which determines the relevance of knowledge based on its usage history. The work introduces a novel algorithm for efficiently forgetting items from large memory stores while preserving base-level fidelity, thereby improving model reactivity and scaling without compromising reasoning competence.

### Description of Result

The proposed forgetting policies were applied to Soar's working and procedural memories and evaluated in two tasks: a simulated robotic exploration and a competitive multi-player game. The results demonstrated that these policies improved model reactivity and scaling while maintaining problem-solving competence. The novel algorithm for forgetting was shown to efficiently manage large memory stores, supporting real-time modeling.

---



### How it Compares to Previous Work

The work builds on previous research in cognitive modeling that has explored forgetting mechanisms to account for human behavior. Unlike prior work, which often focused on short-term memory decay, this research emphasizes long-term, real-time modeling in complex tasks. The proposed policies extend previous investigations by incorporating task-independent forgetting mechanisms that balance task performance with reductions in retrieval time and storage requirements.

#### How was the Work Evaluated

The work was evaluated using two tasks: a simulated robotic exploration and a multi-player game (Liar's Dice). The evaluation focused on the effectiveness of the forgetting policies in managing memory size and improving task performance.

##### Which Benchmarks Were Used and a Description of Each Benchmark

1. **Simulated Robotic Exploration**: The task involved a robot visiting over 100 rooms, requiring the model to build and reason with a large topological map. The benchmark measured working-memory size and decision time over the duration of the task.
    
2. **Liar's Dice Game**: A multi-player game with a large state space, where the model learned rules to represent the value function. The benchmark measured memory usage, task performance, and decision-making efficiency over 40,000 games.
    

### Evaluation Performance and Comparison to Other Works

The proposed forgetting policies significantly reduced memory usage and improved decision-making efficiency compared to models without forgetting mechanisms. The policies maintained task performance comparable to hand-coded rules, demonstrating their effectiveness in managing memory while preserving reasoning competence.

---


### Main Strategies Used to Obtain Results

The main strategy was the use of base-level activation as a heuristic for identifying knowledge that could be forgotten. The research introduced a novel algorithm for predicting when knowledge would decay, allowing for efficient memory management. The policies were designed to be task-independent, relying on the architecture's ability to reconstruct forgotten knowledge if needed.

#### Forgetting WME
At the end of each decision cycle, Soar removes from
working memory each element that satisfies all of the following
requirements, with respect to thr

- R1. The WME was not encoded directly from perception. 
	- distinguishes between the decay of representations of perception, and any dynamics that may occur with actual sensors, such as refresh rate, fatigue, noise, or damage.
- R2. The WME is operator-supported.
	- conceptual optimization: as operator- supported WMEs are persistent, while instantiation supported structures are direct entailments. Thus, if we properly remove operator-supported WMEs, any instantiation- supported structures that depend on them will also be removed. Therefore our mechanism only manages operator- supported structures
- R3. The activation level of the WME is less than thr
- R4. The WME augments an object, o, in semantic memory.
	- since it can be reconstructed
- R5. The activation levels of all augmentations of o are less than thr
	- either all object augmentations are removed, or none. This policy leads to an object-oriented representation whereby procedural knowledge can distinguish between objects that have been completely cleared of substructure, and those that simply are not augmented with a particular feature or relation. makes an explicit tradeoff, weighting more heavily model competence at the expense of the speed of working-memory decay. This requirement resembles the declarative module of ACT-R, where activation is associated with each chunk and not individual slot values

We adopted requirements R1-R3 from Nuxoll et al.
(2004), whereas R4 and R5 are novel

#### Forgetting procedural
At the end of each decision cycle, Soar removes from
procedural memory each rule that satisfies all of the following
requirements, with respect to parameter thr:

- R1. The rule was learned via chunking.
	- distinguish learned knowledge from “innate” rules developed by the modeler, which, if modified, would likely break the model
- R2. The rule is not actively firing.
- R3. The activation level of the rule is less than thr.
- R4. The rule has not been updated by RL.
	- attempts to retain rules that include information that cannot be regenerated in the future, since expected utility info from RL is not recorded by other learning mechanisms

We adopted R1–R3 from Chong (2004), whereas R4 is
novel

---

### Any Other Important Info

#### Weaknesses of Method

The forgetting policies may lead to reasoning errors if semantic memory changes in a way that is inconsistent with decayed working-memory elements. Additionally, the model may thrash if the decay rate is too aggressive, leading to insufficient knowledge retention for complex planning tasks.

#### Future Work

Future research could explore adaptive decay-rate settings and approaches to planning that are robust in the face of memory decay. Further evaluation across a wider variety of problem domains is also needed to validate the generality of the proposed policies.

### Biological Inspiration

The mechanisms are inspired by the base-level activation model from ACT-R, which is based on human memory theories. 