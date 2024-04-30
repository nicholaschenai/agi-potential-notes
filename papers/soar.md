---
date: 2022-05-08
time: 14:22
author: John E. Laird
title: Introduction to Soar
created-date: 2024-03-05
tags:
  - AI
  - cog-arch
paper: https://arxiv.org/abs/2205.03854
code: https://github.com/SoarGroup
site: https://soar.eecs.umich.edu/
---
The paper is meant as initial reading for an overview of the cognitive architecture. see first para for more materials
# Intro
Soar is meant to be a general cognitive architecture (Langley et al., 2009) that provides the fixed
computational building blocks for creating AI agents whose cognitive characteristics and capabilities
approach those found in humans (Laird, 2012; Newell, 1990). 

A cognitive architecture is ==not a single algorithm or method== for solving a specific problem; 

rather, it is the task-independent infrastructure that learns, encodes, and applies an agent’s knowledge to produce behavior, making a cognitive architecture a software implementation of a general theory of intelligence. 

# Description of result
Over the years, a wide range of agents has been developed in Soar (see appendix)

These include
- agents embodied in real-world robots
- computer games 
- large-scale distributed simulation environments. 

These agents incorporate combinations of 
- real-time decision-making 
- natural language understanding 
- metacognition  
- multiple forms of learning.

### Combinations of Reasoning and Learning
One unique aspect of cognitive architectures is that they can combine multiple approaches to reasoning
and learning depending on the available knowledge. Below are examples that have arisen in Soar agents.
1. Reinforcement learning is used to learn retrievals from episodic memory (Gorski & Laird, 2011).
2. Mental imagery is used to simulate actions (in a video game) and detect collisions that inform
reinforcement learning (Wintermute, 2010).
3. Chunking is used to learn RL rules whose initial values are derived from internal probabilistic
reasoning that estimate the expected value. Those values are then tuned by experience (Laird, 2011).
4. Substates are used to reason about the actions of another agent via a combination of self-knowledge
(the simulation approach to Theory of Mind) and knowledge learned about the agent (Laird, 2001).
5. A variety of knowledge sources are used to model the action of operators for planning, including
procedural memory, semantic memory, episodic memory, and mental imagery (Laird et al., 2010), all
of which are compiled into rules by chunking.
6. Episodic memory, metareasoning, and chunking are combined in retrospective analysis that provides
one-shot learning of new operator proposal and evaluation knowledge (Mohan, 2015).
7. Emotional appraisals are used as intrinsic reward for reinforcement learning (Marinier et al., 2009).
8. Episodic memory is used to store goals for future situations in prospective tasks (Li, 2016).


---
# How it compares to previous work
- ACT-R (Anderson et al., 2004), emphasizing cognitive modeling and connections to brain structures (Anderson, 2006).
	- Laird (2021) provides a detailed analysis and comparison of Soar and ACT-R.
- Sigma (Rosenbloom et al., 2016)
- Common Model of Cognition (Laird et al., 2017),

## Operators vs rule-based systems
The use of operators contrasts with rule-based systems where the processing cycle involves selecting and firing a single rule, and where the knowledge for those functions is forever bound together as conditions (if-part) and actions (then-part). 

In contrast, the knowledge in Soar for each of those functions is represented as independent rules, which ==fire in parallel== when they match the current situation.

## Deliberation and meta-reasoning
Soar commits to a single approach for both deliberation and meta-reasoning, which differs from architectures that have separate meta-processing modules, such as MIDCA (Cox et al., 2016) and Clarion (Sun, 2016).

---
# Main strategies used to obtain results

## Structure overview
![](assets/Pasted%20image%2020240305142705.png)
These are interacting task-independent modules.

See page 3 top for concrete examples

Agent behavior driven by the interaction between the contents of working memory and procedural
memory. Other modules provide nonsymbolic reasoning (SVS) and long-term declarative knowledge (semantic and episodic memory).

## Procedural
skills and “how-to” / processing knowledge

Procedural knowledge / memory
- drives behavior by responding to the contents of working memory and making modifications to it. 
- can also initiate actions with either SVS for spatial/imagery based reasoning or with the motor system for action in the world. 
- implements purely internal reasoning,
- initiate retrievals from semantic and episodic memory into working memory

Automatic learning mechanisms are associated with procedural and episodic memories.

Learnt from [chunking](soar#Chunking%20Learning%20New%20Rules) and [RL](soar.md#Reinforcement%20Learning%20Learning%20Control%20Knowledge%20from%20Reward)

## Deliberate Behavior: Selecting and Applying Operators (Decision cycle)
![](assets/Pasted%20image%2020240305200356.png)

Soar organizes knowledge about conditional action and reasoning into **operators**. 

An operator can be
- Internal, eg
	- adding numbers
	- retrieving information for episodic or semantic memory
	- rotating an image in the spatial memory system
- external, eg 
	- initiating forward motion or turning in a mobile robot 
	- producing a natural language statement
	- accessing an external software system over the internet

Soar decomposes the knowledge associated with an operator into three functions
- proposing potential operators 
- evaluating proposed operators 
- applying the operator

Rules are not themselves alternative actions, but instead units of context-dependent knowledge that contribute to making a decision and taking action. 

Thus, there are rules to propose operators, evaluate proposed operators, and apply the selected operator.

### Working mem
contains the information that is tested by rules. It includes goals, data retrieved from long-term memories, information from perception, (intermediate) results of internal operators, situation etc

- no predefined structure for the contents of working memory except for the buffers that interface to other modules
- Thus, there is no predefined structure for goals or even operators. 
- one exception: to support the proposal and selection of operators, Soar has a special data structure called a preference. Preferences are created in the actions of rules and added to preference memory. two classes: 
	- Acceptable preferences are created by operator proposal rules to indicate an operator is available for selection independent of whether it should be selected. Acceptable preferences are also added to working memory so that the evaluation rules can detect which operators have been proposed. 
	- Evaluative preferences, of which there are many types, specify information about whether an operator should be selected for the current situation

### Input Phase

processes data from perception, SVS, and retrievals from semantic and episodic memory, and adds that information to the associated buffers in working memory
### Elaboration phase
where rules that elaborate the situation fire, propose operators, and evaluate operators. 

- All rules fire only once for a specific match to data in working memory ("instantiation")
- a given rule will fire multiple times if it matches different structures in working memory 
- rules that fire in this phase make monotonic additions to either working or preference memory 
- the structures they create are valid only as long as the rule instantiation that created them matches
	- else the structures created by it (elaborations or preferences) are retracted and removed from their respective memories. This is an example of justification-based truth maintenance (Forbus & DeKleer, 1993).
- no ordering of firing and retracting among rules – occur in parallel. 
- However, there is a common progression that starts with a wave of elaboration rule firings, followed by a wave of operator proposal, and finally a wave of operator evaluation. 
- Different type rules that newly match or retract intermix within a wave. 
	- For example, a proposal rule that tests only data from input will fire at the same time as elaborations that test input. 
- When no additional rules fire or retract, quiescence is reached, and control passes to operator selection.
- Operator application rules, even those that newly match because of changes from input, ==do not fire== during the elaboration phase, only during the operator application phase. 
	- this ensures that they fire only when their tested operator is preferred for the current situation.

#### Elaboration Rules
create new structures entailed by existing structures in working memory. 

Elaborations can simplify the knowledge required for operator proposal, evaluation, and application by detecting useful regularities or abstractions. 

An example could be a rule that determines if an object is close enough to be picked up by testing that the object is not more than 8 inches away. By computing this once in a single rule, other rules can test the abstraction (close-enough-for-pickup) instead of the specific distance.

#### Operator Proposal Rules
In parallel with elaborations (or following them if dependent on them), operator proposal rules test the
current situation (preconditions or affordances of an operator) to determine if an operator is relevant. 

If it is, they propose the operator by creating an acceptable preference and an explicit representation ( which often will include parameters) of the operator in working memory. 

Often, task knowledge is incorporated into the proposal to avoid proposing all operators that are legal in a situation. 

For a mobile robot, an operator proposal rule might test that if the goal is to pick up an object, and the robot can see the object, is lined up with the object, is not close enough to the object (testing the absence of the result computed by the elaboration above), and there are no objects between it and the object, then it will propose moving forward.

#### Operator Evaluation Rules
Once operators have been proposed, operator evaluation rules test the proposed operators and other
contents of working memory (such as the goal) and create preferences that determine which operator is
selected. 

Soar has preferences that allow assertions of an operator’s value relative to another operator (A
is better than B, A is equal to B) or absolute value (A is as good as can be achieved, B is the worst that
can be achieved, or C should be rejected). 

There are also numeric preferences that encode the expected future reward of an operator for the current situation as matched by the conditions of the rule. 

 All preferences newly created are added to preference memory to be processed during the operator selection phase.

### Operator Selection Phase
Once quiescence is reached, a fixed decision procedure processes the contents of preference memory to
choose the current operator. 

If the available preferences are sufficient to make a choice, a structure is created in working memory indicating the selected operator. 

If the preferences are inconclusive or in conflict, then no operator is selected, and an impasse arises

### Operator Application Phase
Once an operator is selected, operator application rules that match the selected operator fire to apply it.

These rules make non-monotonic changes to working memory and ==do not retract== those changes when they no longer match. 

Operator applications have multiple functional roles:
- Internal reasoning steps that modify the internal state of a problem. 
	- For example, if an agent is counting the number of objects in a room, and the count is maintained in working memory, a counting operator will increment the count.
- External actions that create commands in the motor buffer. 
	- For a robot, once the move forward operator is selected, a rule would fire and create the move forward command in the motor buffer.
- Mental imagery actions that create commands in the SVS buffer
	- These actions include projecting hypothetical objects, filtering information, or performing actions on SVS structures. 
	- For a robot, it could project the locations of objects that are obscured by its arm into SVS as the arm moves in front of its camera.
- A retrieval cue that is created in either of the semantic or episodic memory buffers 
	- For a robot, as it moves into a new room, it might retrieve the layout of the room from semantic memory. When asked to retrieve an object, it can query episodic memory to recall its last known location.

Operator application terminates when 
- there are no more rules to fire or 
- if the selected operator is no longer the best choice. 
	- To determine this, the decision procedure is rerun after each wave to ensure that the current operator should stay selected. 
	- The selected operator can change if either the rule that proposed it retracts or there are sufficient changes in other preferences such that another operator is preferred. 
	- If that is the case, the current operator is deselected, but a new operator is not selected until the next operator application phase. 

## Impasses and Substates: Responding to Incomplete Knowledge
When the available knowledge is insufficient to make a decision or apply an operator, an impasse arises  and the proposal, evaluation, and/or application phases are themselves carried out via recursive operator selections and applications in a substate

This impasse-driven process is the means through which complex, hierarchical operators and reflective meta-reasoning (including planning) are implemented in Soar, instead of through additional task-specific modules. 

Thus, an operator such as moving to the next room, setting the table, or deriving the semantics from natural language input, can be proposed and selected, but then implemented in a substate through more primitive operators in a substate.

Philosophy: additional relevant knowledge can be obtained through additional deliberate reasoning and retrieval from other sources (going meta), including 
- internal reasoning with procedural memory (e.g., planning), 
- reasoning with non-symbolic knowledge, 
- retrieval from episodic memory or semantic memory, 
- interaction with the outside world

There are three types of impasses that correspond to the different types of failures of the decision procedure that are related to the different types of knowledge described earlier: operator proposal, operator evaluation, and operator application. 

- State no-change: No operators are proposed. This usually indicates that new operators need to be created for the current situation.
- Operator tie/conflict: Multiple operators are proposed, but the evaluation preferences are insufficient or in conflict for making a decision. Usually, this involves determining which proposed operator should be selected and creating the appropriate preferences to make that happen.
- Operator no-change: The same operator stays selected across multiple decision cycles. This usually indicates that there is insufficient knowledge to apply an operator, but it can also arise when an operator is selected whose actions require multiple cycles to apply in the external environment. In that situation, the response is usually for the agent to wait for the action to complete.

To localize reasoning about impasses from the original task information, Soar organizes information in
working memory into states. 

The initial contents of working memory are all linked to a single state, called the topstate. 

When there is an impasse, a new state is created, (substate (also referred to as subgoal)) in working memory that links to the existing state (its superstate). 

The substate has its own preference memory and the ability to select and apply operators without disrupting any of the processing in superstates. Through the link to the superstate, it has access to all superstate structures. It has buffers for semantic memory and episodic memory, but not perception and motor, which must go through the topstate so that all interaction with the outside world can be monitored by reasoning in the topstate.

Procedural memory matches against structures in the substate just as in the topstate. Operators can be
proposed, selected, and applied using the same decision procedure, but applied to the preferences that are created locally in the substate. 

If there is insufficient knowledge in the substate to select or apply an operator, another impasse arises, which leads to a stack of states. There is a tradeoff in that deliberate reasoning in a substate takes more time. Execution time can be monitored in the substate so that a nonoptimal (or even random) decision can be made if time is an issue.

When an operator in a substate creates and modifies structures in a superstate, those changes are the results of the substate. If the result is an elaboration in the superstate (state elaboration, operator proposal, or operator evaluation), it persists only as long as the superstate structures responsible for its creation exist.

==If the result is part of the application of an operator selected in the superstate (a superoperator), such as==
==creating a motor command, the result does not retract.==

The decision cycle remains active across all states, and an impasse is resolved whenever a new decision
is possible in a superstate, either through the creation of new preferences or through changes in the state
(including changes in perception) that then lead to changes in preferences in the substate. 

When an impasse is resolved, the substate terminates and all non-result substate structures are automatically removed. Through this approach, Soar agents remain reactive to relevant changes in their environment, even when there are many active substates. 

For example, if an agent is attempting to build a tower and has an operator proposed for multiple blocks, there could be an impasse to choose from among those operators. If a human removes all the blocks but one, the resulting changes to input will lead to rule retractions so that only a single operator is proposed, and a decision will be made, resolving the impasse.

### Deliberate Operator Application: Hierarchical Task Decomposition

While it is possible to define operators that apply immediately through the application of rules, so that all decision-making is in terms of primitive actions, it is frequently useful to reason with more abstract operators that require dynamic combinations of primitive operators and other abstract operators. 

For example, if we want our robot to clean a room, it would be useful to have operators such as fetch, throw-away, or store. These operators are not primitive and require the execution of multiple low-level operators, which themselves may require problem solving to perform, leading to hierarchical task decompositions. 

Hierarchical task decomposition arises naturally in Soar when an abstract operator is selected, and there are no rules that directly implement it. This leads to an impasse and the creation of a substate. As part of this approach, there must be procedural knowledge that is sensitive to the existence of such an impasse, which then proposes operators for implementing fetch, such as remember-location, find-object, go-to-object, pickup object, and return-to-location, and selecting between them. these operators can be a mix of primitive (remember-location) and abstract and lead to substates with additional problem solving, ultimately bottoming out in movement and manipulation operators. 

Figure 3 shows a three-level hierarchy of operators (but not substates) where the application of a move-block operator is decomposed into pickup and put-down. Pick-up and put-down are implemented in even more primitive operators that direct control the gripper.

![](assets/Pasted%20image%2020240306182040.png)

Soar’s approach to hierarchical decomposition has similarities to those used in Hierarchical Task
Networks, Hierarchical Goal Networks, or options in RL. A few distinguishing characteristics of Soar’s
approach are that the hierarchical decomposition is determined by the knowledge available at run time.
There is no fixed declarative structure that specifies the decomposition, nor are there fixed sequences of
sub-operators that are applied. Within a substate, all of Soar’s processing is available, which includes
planning, retrievals from semantic and episodic memory, and interaction with the external environment.

### Deliberate Operator Selection (resolving operator conflicts/ties)
The purpose of the resulting substate is to determine which operator should be selected and to create additional preferences so a decision can be made. (The preference scheme is such that additional preferences can always eliminate an impasse that arises for operator selection. In the extreme, this requires creating new copies of some of the tied operators.) This is a case when an automatic parallel process (rule firings) is insufficient, it is supplemented by a deliberate, sequential process (operator selection and application) that can explicitly reason about each proposed operator and how it compares to the other proposed operators. This naturally leads to look-ahead planning and other deliberate approaches to operator evaluation. 

Consider the example of where the robot wants to move to another room and has abstract operators for moving to an adjacent room. If there are no evaluation rules, an impasse arises and in the substate, the agent can use internal models of its operators to imagine the situation after applying these different operators. It could then evaluate those situations based on how close they are to the desired room or keep planning until a path to the desired room is found. 

Different look-ahead search methods, such as iterative-deepening approaches to best-first search or alpha-beta, arise depending on the knowledge available for evaluating states and combining the evaluations (Laird et al., 1986).

### Summary of Impasses and Substates
- Impasse-driven substates allow an agent to automatically transition from using parallel procedural knowledge to using more reflective, metacognitive reasoning when the procedural knowledge is insufficient to select and/or apply operators (Rosenbloom, Laird & Newell, 1986).  
- provides a means to incorporate any and all types of reasoning that are possible in Soar in service of the core aspects of proposing, selecting, and applying operators, so that as described below, there is a portal for incorporating additional knowledge and unconstrained reasoning, including deliberate access to semantic and episodic memories, non-symbolic reasoning, planning, reasoning about others (Laird, 2001), etc.

## Chunking: Learning New Rules
When the processing in a substate creates knowledge to resolve the impasse, chunking occurs: the processing in a substate is compiled into rules that create the substate results, eliminating future impasses and substate processing

Converts deliberate, sequential reasoning into parallel rule firings

- Chunking is automatic and is invoked whenever a result is created in a substate. 
- when results are created, Soar back-traces through the rule that created them, finding the working memory elements that were tested in its conditions. ( determining which structures in the superstate had to exist for the results of the substate processing to be created. )
- All of those working memory elements that are part of a superstate are saved to become conditions along with additional tests in the conditions of the associated rules, such as equality and inequality tests of variables used in rule conditions, or comparative tests of numbers, and so on. 
- Tested working memory elements that were local to the substate lead to further back-tracing, which recurs until all potential conditions are determined. 
- Once back-tracing is complete, those structures become the conditions of a rule, and the results become the actions. 

Independent results in a substate lead to the learning of multiple rules.

New approach: explanation-based behavior summarization (EBBS; Assanie, 2022).

Chunking can learn all the types of rules encoded in procedural memory and what it learns is dependent
only on the knowledge used in the substates to create results. The breadth of that processing means that
chunking can learn a surprisingly wide variety of types of knowledge. 


==One limitation is that chunking requires that substate decisions be deterministic so that they will always==
==create the same result.== Therefore, chunking is not used when decisions are made using numeric
preferences. We have plans to modify chunking so that such chunks are added to procedural memory
when there is sufficient accumulated experience to ensure that they have a high probability of being
correct.

Our empirical results show that costs of the analyses it performs to create a chunk is minimal and that the performance improvements are much greater than the costs (Assanie, 2022).

### Examples
Steier et al., (1987) and Rosenbloom et al., (1993) are collections of early examples of the diversity of learning possible with chunking.

- Elaboration: In any impasse, if the processing in the substate creates monotonic entailments of the superstate, Soar learns elaboration rules.
- Operator Proposal: When there is a state no-change impasse, it is resolved through the creation of acceptable preferences for new operators, which lead to the learning of operator proposal rules.
- Operator Evaluation: When there is a tie or conflict impasse, and a substate generates new preferences, chunking learns operator evaluation rules. 
	- When the substate processing involves planning, this involves compiling planning into knowledge that is variously called search-control, heuristics, or value functions. 
	- When those are numeric preferences, the rules created by chunking will be RL rules that are initialized with whatever evaluation was generated. In the future, those rules can be further tuned by reinforcement learning (Laird et al., 2011).
- Operator Application: For operator no-change impasses where the substate implements an operator through hierarchical decomposition, interpreting declarative representations from semantic memory, or recalling past examples from episodic memory, chunking learns operator application rules (Laird et al., 2010). For operators used to simulate external behavior, this corresponds to model learning.

Also Steier et al., (1987):
- analogy
- look-ahead search
- task decomposition
- planning

## Reinforcement Learning: Learning Control Knowledge from Reward

Possible to use RL to tune the selection of operators if the agent receives feedback (reward) from its actions

- create operator evaluation rules that create numeric preferences, aptly called RL rules (Nason & Laird, 2005). 
- The conditions of an RL rule determine which states and proposed operators the rule applies to, and the numeric value of the preference encodes the expected reward (Q value) for those states and operators. 
- After an operator applies, all RL rules that created numeric preferences for it are updated based on the reward associated with the state and the expected future reward. 
- The expected future reward is the sum of the numeric preferences for the next selected operator.
- Even when no reward is created for a state, the values associated with operators proposed for that state propagate the expected reward back to RL rules used in the previous decision.
- RL influences operator selection only when non-RL preferences are insufficient for making a decsision. This approach can lead to much faster learning using RL, as well as less risk of dangerous behavior.

Robot example: RL rules might be a set of rules for moving and turning in relation to an object the robot wants to pick up. Each rule would test different distances and orientations of the object relative to the robot, and a specific operator for either moving forward fast or slow, or turning left or right. The action would be a numeric preference (Q value) for that operator. Assuming a reward that decreases over time, with experience, the rules will learn to prefer the operators that are best for quickly achieving the objective. As mentioned above, the RL rules can be supplemented with evaluation rules that always avoid collisions from the start, which are usually easy to include when the agent is being developed.

- Reward is created by rules that test state features and create a reward structure on the state. 
	- simple case: a rule detects that a goal has been achieved and creates a positive reward, such as when the object is picked up. 
- rules can also compute reward by evaluating intermediate states or converting sensory information (including external reward signals) into a representation of reward on the state. 
	- robot example: an intermediate reward could be proportional to the distance to the object, although that could have some issues in this case. 

==Soar currently has no pre-existing intrinsic reward==; however, intrinsic reward based on appraisal theory has been used in an experimental version of Soar (Marinier et al., 2009).

All created rewards are summed to give the total reward for the state. As operators are applied, reward
changes as rules that create reward values fire (or retract) in response to changes in the state.

the mapping from state and operator to expected reward (the value-function) is represented as collections of relational rules. 

When there are multiple rules that test different state and operator features, they can provide flexible and complex value functions that map from states and operators to an expected value, supporting tile coding, hierarchical tile coding, coarse coding, and other combination mappings, which can greatly speed up learning (Wang & Laird, 2007).

Furthermore, RL rules can be learned by chunking, where the initial value is initialized by the processing
in a substate, and then subsequently tuned by the agent’s experience and reward.

Finally, RL in Soar applies to every active substate, with independent rewards and updates for RL rules
across the substates. Thus, Soar naturally supports hierarchical reinforcement learning for all different
types of problem solving and reasoning, including when planning is used in substates (model-based RL),
or in the topstate (model-free RL).

## Semantic mem
declarative knowledge: facts about the world and the agent. serves as
- knowledge base that encodes general context-independent world knowledge and
- specific knowledge about an agent’s environment, capabilities, and long-term goals.

Semantic and episodic memories differ from procedural memory in 
- how knowledge is encoded (as graph structures instead of rules), 
- how knowledge is accessed (through deliberate cues biased by metadata as opposed to the matching of working memory to rule conditions), 
- what is retrieved (a declarative representation of the concept or episode that best matches the cue versus the actions of a rule), 
- and how they are learned

Q: Why the information in semantic memory cannot be maintained in working memory?
A: cost of matching procedural knowledge against working memory increases significantly with working memory size, making it necessary to store long-term knowledge separately

Concepts in semantic memory are encoded in the same symbolic graph structures as used in working
memory. (Semantic (and episodic) memory can include symbols that are pointers to modality-specific structures (such as images), and those structures can then be retrieved in Soar’s modality-specific memory) 

Retrieval process:
- creation of a cue (partial specification of the concept to be retrieved, such as a room that has a trash can in it. ) in the semantic memory buffer
- cue is used to search semantic memory for the concept that best matches the cue, 
- the complete (best-matching) concept is retrieved into the working memory buffer, where it can then be tested by procedural memory to influence behavior. 

semantic memory provides flexibility in what aspects of a concept are used as a cue to retrieve it. The same trashcan concept might also be retrieved using its color, its brand, or its size (assuming each of those is a defining characteristic).

Soar uses a combination of base-level activation and spreading activation to determine the best match, as used originally in ACT-R (Anderson et al., 2004). 

- Base-level activation biases the retrieval using recency and frequency of previous accesses of concepts (and noise), 
- spreading activation biases the retrieval to concepts that are linked to other concepts in working memory. 
 
Together, these model human access to long-term semantic memory (Schatz et al., 2018), and have been shown to retrieve the concept most likely to be relevant to the current context (as defined by working memory). 

For example, if a retrieval is attempted for the concept associated with the concept “bank,” and the agent has recently had the definition associated with a financial institution in working memory, that concept would get an activation boost from recency. In another situation, where someone is canoeing down the river, the concept of river would spread activation to the concept related to the bank of a river, improving the likelihood of its retrieval.

### Implementation
can be ==initialized with knowledge from existing curated knowledge bases (such as WordNet or DBpedia)== and/or built up incrementally by the agent during its operations. 

As of yet, ==Soar does not have an automatic learning mechanism for semantic memory==, but an agent can deliberately store information at any time. 

examples of semantic mem contents in robot:
- map of its environment either preloaded from some other source or dynamically constructed as the robot explores its world
- maintain representations of words and their meaning for language processing
- knowledge about different agents the robot interacts with
- declarative representations of hierarchical task structures it learns from instruction.

## Episodic mem
declarative knowledge: memories of experiences

Knowledge in semantic mem is **independent** of when it was learned

- An episode is a snapshot of the structures in the topstate. 
- A new episode is automatically stored at the end of each decision. 

provides an agent with the ability to remember the context of past experiences as well as the ==temporal relationships== between experiences.

retrieval process:
- Similar to retrievals in semantic memory, and for similar reasons, retrievals from episodic memory are initiated via a cue created in the episodic memory buffer by procedural knowledge. 
	- Unlike semantic memory, a cue for an episodic retrieval is a partial specification of a **complete state**, as opposed to a single concept. 
- Episodic memory is searched, and the best match is retrieved (biased by recency) and recreated in the buffer. 
- Once a memory is retrieved, memories before or after that episode can also be retrieved, providing the ability to replay an experience as a sequence of retrieved episodes or to move backward through an experience to determine factors that influenced the current situation, including prior operators, but also changes from the dynamics of the environment.

### why
Episodic learning has often been ignored as a learning method because it does not have generalization
mechanisms. despite this, it supports many important capabilities:
- Virtual sensing of remembered locations and objects that are outside of immediate perception (Nuxoll & Laird, 2012).
- Learning action models for internal stimulation from memories of the effects of past operators that have external actions (Laird et al., 2010; Jones, 2022).
- Learning operator evaluation knowledge via retrospective analysis of previous behavior (Mohan & Laird, 2013).
- Using prospective memory to trigger future behavior, by imagining hypothetical future situations that are stored in memory and then recalled at the appropriate time (Li & Laird, 2015).
- Using a string of episodes to reconstruct and debug a particular course of action.

## Spatial-visual system
supports perceptual grounding and modality-specific non-symbolic representations and processing / reasoning

### motivation
All the representations in Soar’s short and long-term memories described so far are symbolic, with nonsymbolic metadata used for biasing decision making and retrievals from long-term memories. 
- For real-world environments, many of the symbol structures in working memory must be grounded in the perception of objects in the world. 
- there are forms of reasoning that can be much more efficient with modality-specific representations, such as mental imagery (Kosslyn et al., 2006). 
- Purely symbolic representations abstract away critical details and do not efficiently support non-symbolic data processing, such as simulating movement and interactions of objects in 2D or 3D

SVS is an intermediary between symbolic working memory and the nonsymbolic components of a Soar agent, including perception and motor control, and long-term modality-specific memories. 

information flows 
- bottom-up from perception through SVS into working memory
- and top-down from working memory to SVS to support reasoning over hypothetical non-symbolic representations.

Soar’s approach to integrated symbolic and modality-specific representations contrasts with architectures
that have unified representations where both symbolic and non-symbolic representations are combined in a single memory. The separation in Soar allows for independently specialized and optimized processing of each type of representation but requires an interface for transferring information between them.

SVS is currently restricted to visual input, which includes 2D image-based/depictive representations for
video games and 3D representations of objects with associated spatial metric information (scene graphs)
for real-world robots and 3D simulation environments. 

To control the flow of information from SVS to working memory, an agent uses operators to issue commands to SVS that create filters, which automatically extract 
- symbolic properties (object `o45` has `color g34`, has `shape box`, has `image i45`, has `height 34 inches`, …) 
- and relations (object `o45` is to the `left-of` object `o65`).

SVS supports hypothetical reasoning over spatial-visual representations through the ability to project nonsymbolic structures into SVS, such as projecting a ball into a scene. 

An agent can perform modality-specific operations on the objects in SVS (real and imagined), such as rotation, scaling, and motion, and detect relations such as collisions, to determine the outcome of potential motor actions in the current state.

For example, a Soar agent can simulate the motion of a vehicle along a path to determine if it will collide
with other objects in planning (Wintermute, 2010). Thus, SVS mediates interactions between motor
actions and perception in 3D robotic environments.

Figure 4 shows an example of SVS’s use while planning to move a robot arm. A representation of the arm
together with objects in the environment is maintained in SVS and in parallel, a symbolic representation
is maintained in working memory. When the arm is moved in such a way that it obscures an object (such
as object 5, which is red and barely visible in the image), the agent uses its knowledge of 3D space and
object persistence to understand that the obscured object has not disappeared, and it can maintain a
symbolic representation of the object in working memory (Mininger, 2019).

![](assets/Pasted%20image%2020240306191331.png)

## summary
![](assets/Pasted%20image%2020240305173019.png)
fig 6: The image memory system is still experimental, although variants have been used on past systems that relied on SVS. What this figure does not show are the variety of contents that each learning mechanism stores. For example, chunking can learn operator proposal, operator evaluation, or operator implementation knowledge, and the exact content depends on the reasoning performed in the substate that generated the result.

---
# Other

### Challenges
- Cognitive architecture design: how to create sufficient structure to support coherent and purposeful behavior, while providing sufficient flexibility so that an agent can adapt (via learning) to the specifics of its tasks and environment.
- Assaine 2022 (thesis): traces can be extremely large for even simple tasks (eg 16 pages)
	- also see thesis for problems with chunking in general
- Operate autonomously, but within a social community (partial, no). Soar agents are completely autonomous, without direct human control. Any control is indirect through communication channels that feed in as input into working memory such as natural language. Some agents have run uninterrupted for 30 days. In addition, Rosie does exist in a (very limited) social community with its instructor. Other agents have been developed that operate in teams (Tambe, et al. 1995; Jones, et al. 1999). it is not clear whether what is missing is some aspect of architecture as opposed to agent knowledge.
- Core and commonsense knowledge (no). Although Soar supports the encoding and use of a wide variety of knowledge, we have not pre-encoded large bodies of innate knowledge that are available in every task. Thus, Soar agents have neither core knowledge systems (Kinzler & Spelke, 2007) nor large bodies of commonsense knowledge (Davis & Marcus, 2015). 
	- It is an open question as to whether this is an issue with missing innate knowledge, or whether it is an architectural issue in that Soar’s procedural and semantic memories are insufficient for representing and accessing these types of knowledge and its learning mechanisms are insufficient for learning them. It is also possible that it is a combination of both.
- most missing from Soar: ability to “bootstrap” itself up from the architecture and a set of innate knowledge into being a fully capable agent across a breadth of tasks. Our agents do well when restricted to specific well-defined tasks. 
	- We have gone beyond that with Rosie, where we achieved success with its task learning capabilities. But without a human to closely guide it, Rosie is unable to get very far on its own, especially in 
		- being unable to learn new abstract symbolic concepts on its own, and similarly 
		- being unable to independently learn new abstract operators (although the IBMEA project briefly described in the appendix is making progress on this).
- from earlier: ==Soar does not have an automatic learning mechanism for semantic memory==
- from earlier: ==Soar currently has no pre-existing intrinsic reward==

### Design choices
- Key hypothesis: sufficient regularities above the neural level to capture the functionality of the human mind. 
- Thus, the majority of knowledge representations in Soar are symbol structures, with architecturally maintained numeric metadata biasing the retrieval and learning of those structures. 
- no commitment to a specific underlying implementation level, such as NN
### Episodic mem overhead details
- Minimizes memory overhead by storing only the **changes** between episodes 
- indexing to minimize retrieval costs

However, memory does grow over time, and the cost to retrieve old episodes slowly increases as the number of episodes grows, whereas the time to retrieve recent episodes remains constant. 

An agent can further limit the costs of retrievals by explicitly controlling which aspects of the state are stored, usually ignoring frequently changing low-level sensory data.