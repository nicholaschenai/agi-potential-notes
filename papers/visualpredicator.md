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
==Note: currently LM draft==
## Description of result

The research introduces **Neuro-Symbolic Predicates (NSPs)**, a novel state representation that integrates the flexibility of neural networks for handling visually grounded concepts with the interpretability and compositionality of symbolic representations for robot planning. NSPs are defined as snippets of Python code capable of invoking Vision-Language Models (VLMs) to query perceptual properties and then algorithmically manipulate these properties.

The core contribution is an **online algorithm for inventing such predicates and learning abstract world models**. This approach allows robots to learn how to plan in new environments with novel physical mechanisms and objects from relatively few interactions, analogous to minutes or hours of training experience. The system learns both the abstract state representation (predicates) and the abstract transition function (High-Level Actions or HLAs). The resulting approach offers **better sample complexity, stronger out-of-distribution generalisation, and improved interpretability** compared to other methods.

---
## How it compares to previous work

This work addresses limitations found in existing robot planning and learning approaches:

- **Traditional Robot Task Planning:** Unlike traditional methods that rely on **hard-coded symbolic world models**, which cannot adapt to novel environments, this approach enables adaptation and learning.
- **Recent Works with Limited Learning:** While some recent works have introduced learning, they often **restrict perceptual and logical abstractions** and necessitate **demonstration data** rather than enabling the robot to explore autonomously. The presented NSPs offer a much broader set of perceptual primitives (anything a VLM can perceive) and deeper logical structures (anything computable in Python).
- **Hierarchical Reinforcement Learning (HRL) Baselines (e.g., MAPLE):** The proposed method consistently **outperforms HRL approaches like MAPLE** across all tested domains, achieving near-perfect solve rates. MAPLE struggles with sample complexity and generalisation to tasks outside the training distribution, a common issue in robotics due to the high cost of real-world interaction data and the sim-to-real gap.
- **Vision-Language Model (VLM) Planning Baselines (e.g., ViLa):** The approach significantly **outperforms VLM planning methods like ViLa**, which demonstrate limited planning capabilities, frequently generate infeasible plans, and tend to overfit. ViLa struggles with complex domains and scenarios requiring nuanced understanding, such as grasping an object with specific orientation.
- **Symbolic Predicate Baselines (Sym. pred.):** While symbolic predicates perform well in simpler domains, they **struggle to invent predicates requiring grounding in perceptual cues** not explicitly encoded in object features. They also face challenges in accurately and efficiently reasoning about abstract concepts without derived NSPs (e.g., determining if a balance beam is balanced).
- **Comparison to Silver et al. (2023):** This research builds upon predicate learning but **advances by inventing open-ended, visually and logically rich concepts without relying on hand-selected features**, and it learns **purely online** unlike their demonstration-based approach.
- **Comparison to Konidaris et al. (2018) and similar works:** While previous online abstraction discovery methods exist, they often use **constrained classes of classifiers** (e.g., decision trees, linear regression), limiting the effectiveness and generalisability of learned predicates.
- **Performance against Oracle (Manually Designed Abstractions):** The method achieves **similar solve rates and efficiency to an "oracle" baseline** that uses manually designed abstractions, demonstrating its ability to autonomously discover expert-level abstractions.

---
## Main strategies used to obtain results

The approach is built upon several key strategies:

- **Neuro-Symbolic Predicates (NSPs):**
    
    - NSPs are Python code snippets that combine programming constructs (conditionals, numerics, loops, recursion) with **API calls to neural vision-language models (VLMs)** for evaluating visually-grounded natural language assertions.
    - They are categorised into **Primitive NSPs**, which are evaluated directly on the raw state (e.g., `Holding(obj)` using VLM queries or `GripperOpen()` using proprioception), and **Derived NSPs**, which compute their truth value based on other NSPs (e.g., `OnPlate` recursively checking if a block is on a plate or something on a plate).
    - To improve the accuracy of primitive NSPs, the system provides **extra context to VLM queries**, including the previous action, previous visual observation, and previous truth values of the queried ground atom. It also **visually labels each object with a unique ID** in the RGB image to disambiguate objects for precise reasoning.
    - Derived predicates can be in preconditions of High-Level Actions (HLAs) but not in postconditions, as their truth values can be immediately calculated from primitive NSPs.
- **Online Abstract World Model Learning Algorithm:**
    
    - The core algorithm **interleaves predicate proposal, predicate scoring/validation, and goal-driven exploration** with a planner in the loop.
    - **Exploration (Section 5.1):** The agent plans using its current abstract world model (predicates and HLAs) and executes these plans. It collects data from successfully executed skills (positive transitions), failed skill executions (negative state-action tuples), and satisficing plans.
    - **Predicate Proposal (Section 5.2):** VLMs are prompted using three strategies to invent diverse, task-relevant predicates:
        - **Strategy #1 (Discrimination):** Prompts VLMs with examples of skill success and failure to discover good preconditions for skills.
        - **Strategy #2 (Transition Modeling):** Prompts VLMs with "before" and "after" snapshots of successful skill execution to describe properties that changed, helping discover postconditions.
        - **Strategy #3 (Unconditional Generation):** Prompts VLMs to propose logical extensions (e.g., negation, quantification, transitive closure, disjunction) of existing predicates, crucial for creating complex derived predicates.
    - **Predicate Selection (Section 5.3):** A novel objective is used to score a set of predicates based on **classification accuracy and a simplicity bias**, particularly useful when no satisficing plans are available early in learning. Once enough satisficing plans are found, it switches to an objective that considers planning efficiency and simplicity. A **greedy best-first search** is used to select the best predicate set.
    - **Learning High-Level Actions (HLAs) (Section 5.4):** The system learns HLAs, which define the abstract transition model, by extending the **cluster and intersect operator learning algorithm**. This extension improves the precondition learner to ensure all data in a partition is modeled by the associated HLA and that the precondition is not satisfied if a skill is inapplicable or has different effects. It **minimises the syntactic complexity of HLAs** to encourage optimistic world models, which is critical for exploration without demonstration data.
- **Hierarchical Planning (Section 4):**
    
    - The learned abstract world model is used to generate a **high-level plan (sequence of HLAs)**, which is then translated into a low-level action sequence by calling corresponding skill policies.
    - Learning is driven by **failures in hierarchical planning**, which can be either infeasible plans (skills fail to execute) or not satisficing plans (skills execute but don't achieve the goal).
