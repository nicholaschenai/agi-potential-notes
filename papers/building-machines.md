---
date: 2016-11-02
time: 20:43
author: Brenden M. Lake, Tomer D. Ullman, Joshua B. Tenenbaum, Samuel J. Gershman
title: Building Machines That Learn and Think Like People
created-date: 2024-06-20
tags: 
paper: https://arxiv.org/abs/1604.00289
code: 
zks-type: lit
---
# Intro
Concept paper (Note: this paper was published in 2016 but largely remains relevant as of 2024)
- Despite recent computational achievements, people are better than machines at solving a range of difficult computational problems, including concept learning, scene understanding, creativity, common sense, and general-purpose reasoning.
- Review progress in cognitive science: Truly human-like learning and thinking machines will have to reach beyond current engineering trends in both what they learn and how they learn it
- machines should 
	- (1) build causal world models that support explanation and understanding (go beyond pattern recognition) 
	- (2) ground learning in intuitive physics and psychology 
	- (3) harness compositionality and metalearning to rapidly acquire and generalize knowledge to new tasks and situations. 
- Suggest concrete challenges and promising routes toward these goals that can combine the strengths of deep learning with more structured cognitive models.
## Approaches to intelligence

### Statistical pattern recognition
- prediction as primary, usually in the context of a specific classification, regression, or control task. 
- learning: from dataset, discover features that have high-value states in common – a shared label during classification or a shared value in RL
### World models
- models of the world as primary
- learning: model building
- Cognition is about using these models to understand the world, to explain what we see, counterfactual reasoning, or what could be true that isn’t, and then planning actions to make it so. 


The diff between the above, between prediction and explanation, is central to our view of human intelligence. 

Just as scientists seek to explain nature, not simply predict it, we see human thought as fundamentally a model building activity.

also discuss how pattern recognition, even if it is not the core of intelligence, can support model building, through “model free” algorithms that learn through experience how to make essential inferences more computationally efficient

## Overview of key ideas
### Key Idea 1: Developmental “start-up software” 
or cognitive capabilities present early in development. why? 
- If an ingredient is present early in development, it is certainly active and available well before a child or adult would attempt to learn the types of tasks discussed in this paper. 
- the earlier the presence, the more likely it is to be foundational to later development and learning.
- This is true regardless of whether the ingredient is from nature or nurture

#### Software 1: Intuitive physics
Infants have primitive object concepts that allow them to track objects over time and to discount physically implausible trajectories, eg persistence of objects over time, and that they are solid and coherent.
- Equipped with these general principles, people can learn more quickly and make more accurate predictions. 
- Although a task may be new, **physics still works the same way**.

#### Software 2: Intuitive psychology
- Infants understand that other people have mental states like goals and beliefs (theory of mind), and this understanding strongly constrains their learning and predictions. eg A child watching an expert play a new video game can infer that the avatar has agency and is trying to seek reward while avoiding punishment. 
- This inference immediately constrains other inferences, allowing the child to infer what objects are good and what objects are bad. 
- These types of inferences further accelerate the learning of new tasks.

### Key Idea 2: Model building
or explaining observed data through the construction of causal models of the world
- early present capacities for intuitive physics and psychology are also causal models of the world. 
- learning: extend and enrich these models and to build analogous causally structured theories of other domains.
- Compared to SOTA algorithms in machine learning, human learning is distinguished by its richness and its efficiency.
	- Children come with the ability and the desire to uncover the underlying causes of sparsely observed events and to use that knowledge to go far beyond the paucity of the data. 
- We suggest that **compositionality** and **learning-to-learn** are ingredients that make this type of rapid model learning possible

### Key Idea 3: Thinking fast

- An important motivation for using neural networks in CV and speech is to respond as quickly as the brain does. our system 1 is lightning fast!
- although neural networks are usually aiming at pattern recognition rather than model building, we discuss ways in which these “model-free” methods can accelerate slow model-based inferences in perception and cognition
- By learning to recognize patterns in these inferences, the outputs of inference can be predicted without having to go through costly intermediate steps. 
- Integrating neural networks that “learn to do inference” with rich model building learning mechanisms offers a promising way to explain how human minds can understand the world so well and so quickly.

### Other
Also discuss the integration of model-based and model-free methods in RL. (recap: MBRL is When rewards are used as the metric for successs in model building)

Once a causal model of a task has been learned, humans can use the model to plan action sequences that maximize future reward. 

However, planning in complex models is cumbersome and slow, making the speed-accuracy trade-off unfavorable for real-time control. 

By contrast, model-free reinforcement learning algorithms, such as current instantiations of deep reinforcement learning, support fast control, but at the cost of inflexibility and possibly accuracy. 

Humans **combine model-based and model-free learning algorithms** both competitively and cooperatively and that these interactions are **supervised by metacognitive processes**. yet to be realized in AI systems, but this is an area where crosstalk between cognitive and engineering approaches is especially promising.

---
# Challenges for building more human-like machines
"... claim that a mind is a collection of general-purpose neural networks with few initial constraints is rather extreme in contemporary cognitive science."

"... highlights the importance of early inductive biases, including core concepts such as number, space, agency, and objects, as well as powerful learning algorithms that rely on prior knowledge to extract knowledge from small amounts of training data. This knowledge is often richly organized and theory-like in structure ... "
## Characters challenge
handwritten character recognition. How is this different from MNIST?
- Human learn from fewer examples
- Humans learn richer repr which enable
	- recognition of new char from single example (fig 1Ai) to distinguish new instances from other ppl vs similar looking non-instances
	- gen new examples (via learning of concepts) (fig 1Aii)
	- conceptual decomposition into parts and relations (fig 1Aiii) "people have a far richer (wrt machines) repertoire of structural relations between strokes"
	- gen new chars given small set of related chars (fig 1Aiv) "people can efficiently ... infer which have optional elements, such as the horizontal cross-bar in ‘7’s, combining different variants ... into a single coherent representation."
- progress on this challenge using probabilistic program induction (Lake et al. 2015a), authors hypothesize combining this with deep learning


![](assets/Pasted%20image%2020240626141602.png)


> [!NOTE]
> Program induction: Constructing a program that computes some desired function, where that function is typically specified by training data consisting of example input-output pairs. In the case of probabilistic programs, which specify candidate generative models for data, an abstract description language is used to define a set of allowable programs, and learning is a search for the programs likely to have generated the data.


## Frostbite challenge
### Intro
Frostbite: one of the "long horizon planning" atari games which the original DQN struggled on. rules:
- guide an agent to construct an igloo and enter the igloo within a time limit.
- igloo built piece by piece by getting agent to jump on constantly moving ice floes in water, when the floes are in an activated state.
- extra points by gathering fish and avoiding hazards (falling in water, other animals)

![](assets/Pasted%20image%2020240626160902.png)

### Difference in human and DQN learning
- original DQN needs to be trained frm scratch for each game
- in Mnih et al 2015, DQN takes 924h to achieve < 10% of what pro gamer can in 2h. also DQN has exp replay where each frame is replayed ~ 8X more on avg over learning
- newer DQN variants are still nowhere as efficient (fig 3)
- humans can grasp the game within minutes. authors speculate that humans infer a general schema to understand the goals of the game, the object types and interactions, using the mechanisms described below. tho novice players may make some mistakes like mistaking if fish are harmful vs helpful, they can learn to play better than chance within minutes. if they watch others play, they can learn faster
- informal experiment: 2 authors watch videos of expert plays on youtube for 2 min, reach scores comparable to human expert in DQN paper after 15-20 min practice
- Frostbite provides incremental rewards for reaching active ice floes which are useful hints for AI, but humans probably dont need it and can figure out that an igloo is the goal.


![](assets/Pasted%20image%2020240626161329.png)

### Challenge: Can AI adapt to new tasks which humans can do so easily? 
- eg
	- getting a specific score
	- making the worst moves (side note: applicable in some games which have features to control your opponents eg Emrakul from MTG.)
	- getting a score that is slightly better than someone while they are playing next to you
	- discovering easter eggs
	- optimizing for side quests (eg getting as many fishes)
	- additional constraints eg touch each ice floe only once
	- team someone to play as efficiently as possible
- this challenge highlights an essential component of intelligence: humans learn models which enable them to adapt to new task and goals much faster than current AI
#### Wait
its not fair that NNs start from scratch but humans dont (eg biological priors, evolution)? but that is **exactly** the point the authors want to make. what is this prior knowledge in humans which enable them to learn and adapt quickly, and how can we get AI to learn the same ingredients? Addressed in section 5.1

---
# Core ingredients of human intelligence
Recall: authors make no statement whether the ingredients should be provided or learnt, only that they are important and not commonly present in AI systems.
## Developmental start-up software
"Early in development, humans have a foundational understanding of several core domains (Spelke 2003; Spelke & Kinzler 2007)." eg
- numbers
- space
- physics
- psychology

"The underlying cognitive representations
can be understood as “intuitive theories,” with
a causal structure resembling a scientific theory (Carey
2004; 2009; Gopnik et al. 2004; Gopnik & Meltzo 1999;
Gweon et al. 2010; Schulz 2012b; Wellman & Gelman
1992; 1998)."


"Child as scientist" proposal: learning is scientist-like
- seek new data to distinguish b/w hypotheses
- isolate variables
- test causal hypotheses
- make use of data generating process in drawing conclusions
- learn selectively frm others

domains are shared cross-culturally, and partly with non-human animals

### Intuitive physics
#### Biological inspiration
over the course of development (from 2mth, 6mth, 1y), infants learn physical properties . eg
- persistence (also: vid on monkey's surprise when a magician performs the disappearing ball in cup trick)
- continuity, 
- solidity
- trajectories. 

which in turn guide learning of more physical properties eg object segmentation.

**no single agreen-upon computational account** of these principles and concepts. suggestions ranged from decision trees to cues and list of rules. 

#### Promising approach: inference over physics software engine
like modern day physics engines (Bates et al. 2015; Battaglia et al. 2013; Gerstenberg et al. 2015; Sanborn et al. 2013). According to this hypothesis, people reconstruct a perceptual scene using 
- internal representations of the objects
- physically relevant properties (such as mass, elasticity, and surface friction)
-  forces acting on objects (such as gravity, friction, or collision impulses). 

this representation is approximate, probabilistic, oversimplified and incomplete but rich enough to support mental simulations that can predict how objects will move in the immediate future, either on their own or in responses to forces we might apply.

This intuitive physics engine can be applied to a wide range of scenarios in a way that goes beyond perceptual cues. For example, a physics engine reconstruction of blocks (see fig 4) can predict if the tower of blocks will fall, in close agreement with how adults make the predictions, and also other simpler physical predictions studied in infants (Téglás et al. 2011).


![](assets/Pasted%20image%2020240626171840.png)

Simulation-based models are also impt in faster learning of hypotheticals and counterfactuals, eg 
- what if properties (material, color, weight) of the blocks change or 
- what if some blocks were removed? 

If we adopt a pattern recognition approach, each of these qns will require new features for training whereas the simulator might not.

Capturing intuitive phys in deep learning: 2016 SOTA was physnet (Lerer et al. 2016) which can predict stability of a block tower from images on par with humans, but required significantly more data (~100k examples).

> Author's challenge: train deep learning systems to be flexible enough to generalize to new scenarios with no new training and without explicitly simulating causal interactions between objects, just like humans
> 
> challenge 2: train NNs to emulate a general-purpose physics simulator given the right type and quantity of training data, such as the raw input experienced by a child. will the higher layers generalize to encode objects, general physical properties, forces, and approximately Newtonian dynamics? (maybe. ref this paper about training NN to predict trajectories in gravity, EM, bouncing balls etc)

#### Applications
back to the Frostbite example, intuitive physics could 
- guide the understanding of how objects move (DQN probably does not parse objects and understands its adherence to intuitive physics), making it robust to changes in object behavior, reward structure or appearance. 
- when new objects are introduced (eg bear in the later levels of Frostbite), a NN with intuitive physics will have an easier time adding this to its knowledge (the challenge of adding new objects was also discussed in Marcus `[1998; 2001]`).
### Intuitive psychology
#### In infants
- distinguish animate agents from inanimate obj, aided by detection of low level cues such as presence of eyes, motion from rest, biological motion
- expect agents to act contingently and reciprocally, to have goals, and to take efficient actions toward those goals subject to constraints (Csibra 2008; Csibra et al. 2003; Spelke & Kinzler 2007).
- socially directed: distinguish between neutral and antisocial agents, then pro-social in later stages

What is less agreed on is the computational architecture that supports this reasoning and whether it includes any reference to mental states and explicit goals.

#### the problem with cue based
increasingly complex cues as the scenario changes
- eg suppose B is blocking A from reaching a box. then the cue is "if expected trajectory is prevented from completion, blocker is negative"
- but what if box is harmful? then B is seen as helping
- what if A is bad in the first place? then B might be good
 
with all these edge cases, the cue becomes a complex series of if else statements

#### prior work: generative models of action choice
- Bayesian inv planning
- Bayesian ToM
- naive utility calculus
- predictive coding

formalize explicitly mentalistic concepts such as “goal,” “agent,” “planning,” “cost,” “efficiency,” and “belief,” used to describe core psychological reasoning in infancy.

assume adults and children treat agents as approximately rational planners who choose the most efficient means to their goals

Planning computations may be formalized as solns to (PO)MDPs, taking as input utility and belief functions defined over an agent’s state-space and the agent’s state-action transition functions, and returning a series of actions the agent should perform to most efficiently fulfill their goals (or maximize their utility).

By simulating these planning processes, people can predict what agents might do next or use inverse reasoning to infer utilities and beliefs of agents from their actions (akin to inverse RL). also similar to intuitive physics where we use an engine to predict future or infer properties of objects. Unlike intuitive physics, simulation-based reasoning in intuitive psychology can be nested recursively: We can think about agents thinking about other agents

#### Deep learning
- can probably learn visual cues, heuristics and summary statistics of a scene that happens to involve agents.
- If that is all that underlies human psychological reasoning, a data-driven deep learning approach can likely find success in this domain
- update: see ToMnet for NN based ToM

a full intuitive physics engine requires the understanding of agency, goals, efficiency, and reciprocal relations. not clear if NNs can properly learn this from predictive training. Just like in intuitive physics, NNs can solve tasks without explicitly learning the required concepts, but that will limit its ability to generalize

#### back to games
- intuitive psych enables fast learning when watching an expert play, by inferring beliefs, desires and intentions eg if the expert player keeps avoiding an object, can infer that the object is harmful without having to manually confirm
- sidekick / assistant agent: an agent with intuitive psych can predict how this sidekick is helpful in new scenarios faster than learning from pixel based repr

#### ideas for deep learning
- a simple inductive bias, for example, the tendency to notice things that move other things, can bootstrap reasoning about more abstract concepts of agency (Ullman et al. 2012a)
- a great deal of goal-directed and socially directed actions can also be boiled down to a simple utility calculus (e.g., Jara-Ettinger et al. 2015), in a way that could be shared with other cognitive abilities

## Learning as rapid model building
Some concepts are learnt more slowly by humans, like those in school (eg calculus). machines also beat humans in some tasks, eg combing thru weather and financial data.

However for most of the cognitively natural concepts, such as the meaning of words, humans learn faster and more efficiently than deep learning, eg children can learn words and concepts in one shot, and the ability persists into adulthood. This type of learning will be the focus of this section

### Biological inspiration
humans learn rich conceptual models with few examples. these models enable functions such as
- classification
- prediction
- action
- communication
- imagination
- explanation
- composition

These abilities are not independent; rather they hang together and interact (Solomon et al.1999), coming for free with the acquisition of the underlying concept. (revisit fig 1B for an example of the range of things people can do once they learn a concept)

This richness **strongly supports the learning as model building view**. 

the one shot capability **suggests that these models are built upon rich domain knowledge**, not tabula rasa which tends to be the focus for deep learning

The 3 key ingredients of rapid model building: **compositionality, causality, and learning-to-learn**

### Rapid model building ingredient 1: Compositionality
Why? 
- finite primitives can express inf repr.
- "... broadly influential in both AI and cognitive science, especially as it pertains to theories of object recognition, conceptual representation, and language"
- eg structural description models represent visual concepts as compositions of parts n relations. this provides strong inductive bias for constructing new models n concepts


parts and relations are themselves product of prev learning, their facilitation of construction of new models is an example of learning to learn. While they naturally fit together, there r also forms of compositionality that rely less on prev learning, such as the bottom-up, parts-based representation of Hoffman and Richards (1984).

another example: handwritten characters as compositions of pen strokes and related by how these strokes connect to each other. Lake et al. (2015a) additionally decomposes complex movements into simpler movements, and construct new chars by combining parts, sub parts and relations (fig 5)

#### Author's attempts: Bayesian program learning for the characters challenge.
- represents concepts as simple stochastic programs: structured procedures that generate new examples of a concept when executed (Fig. 5A)
- programs allow the model to express causal knowledge about how the raw data are formed
- probabilistic semantics allow the model to handle noise and perform creative tasks. 
- Structure sharing across concepts via compositional re-use of stochastic primitives that can combine in new ways to create new concepts.
- BPL framework as a whole: generative model
- hierarchy of models: 
	- higher-level program that generates different types of concepts,
	- which are themselves programs that can be run to generate tokens of a concept. 
- “rapid model building” refers to the fact that BPL constructs generative models (lower-level programs) that produce tokens of a concept (Fig. 5B).
- BPL performs one shot classification at human level (fig 1Ai) and outperforms CNNs
- repr learnt allows BPL to generalize to other tasks (fig 5B)
![](assets/Pasted%20image%2020240630164717.png)

#### back to frostbite
- can be composed as objects (birds, fish, ice floes etc), more economical and better for generalzation, as noted in previous work on object-oriented reinforcement learning (Diuk et al. 2008).
- efficiency more apparent as level progresses where numbers and combinations of objects increase

#### deep learning and compositionality
- NNs do have some notion of compositionality, e.g. features in deeper layer of CNNs look out for parts, and novel objects activate combinations of these features
- Frostbite: DQN may represent replications of same obj with same features via invariance properties of CNN
- "Recent work ... compositionality can be made more explicit, where neural networks can be used for efficient inference in more structured generative models (both neural networks and three-dimensional scene models) that explicitly represent the number of objects in a scene (Eslami et al. 2016)."
- compositionality also applies to goals and subgoals! "Recent work on hierarchical-DQNs shows that by providing explicit object representations to a DQN, and then defining sub-goals based on reaching those objects, DQNs can learn to play games with sparse rewards (such as Montezuma's Revenge) by combining these sub-goals together to achieve larger goals (Kulkarni, Narasimhan, Saeedi, & Tenenbaum, 2016)."

"To capture the full extent of the mind’s compositionality, a model must include explicit representations of objects, identity, and relations, all while maintaining a notion of “coherence” when understanding novel configurations."

### Rapid model building ingredient 2: Causality
causal models: "represent hypothetical real-world processes that produce the perceptual observations." e.g. in RL, causal models represent structure of env, such as modeling the transition functions.

- causal models are usually generative, but generative models are not necessarily causal. for example, the generative process in generative models may not resemble how the data are produced in the real world (eg DBNs, VAEs on handwritten digits). 
- Causality refers to the subclass of generative models that resemble, at an abstract level, how the data are actually generated.

#### Theories of perception
- "“Analysis-by-synthesis” theories of perception maintain that sensory data can be more richly represented by modeling the process that generated it (Bever & Poeppel 2010; Eden 1962; Halle & Stevens 1962; Neisser 1966)."
- "Relating data to their causal source provides strong priors for perception and learning, as well as a richer basis for generalizing in new ways and to new tasks"
- "Liberman et al. (1967) argued that the richness of speech perception is best explained by inverting the production plan, at the level of vocal tract movements, to explain the large amounts of acoustic variability and the blending of cues across adjacent phonemes."
- causality doesnt have to be a literal inversion like the above. eg in BPL, "causality is operationalized by treating concepts as motor programs, or abstract causal descriptions of how to produce examples of the concept, rather than concrete configurations of specific muscles (Fig. 5A).", impt in single shot classification / gen.

#### Causal knowledge influences learning of new concepts
- the structure of the causal network underlying the features of a category influences how people categorize new examples (Rehder 2003; Rehder & Hastie 2001)
- the way people learn to write a novel handwritten character influences later perception and categorization (Freyd 1983; 1987).

#### Causality as the glue for features
- "To explain the role of causality in learning, conceptual representations have been likened to intuitive theories or explanations, providing the glue that lets core features stick, whereas other equally applicable features wash away (Murphy & Medin 1985)."
- causal roles from the functions of objects, eg the feature “flammable” is more closely attached to wood than money, tho the feature is applicable to both
- features related by underlying cause, eg “can fly,” “has wings,” and “has feathers” co-occur across objects, whereas others do not.

#### Causal models to understand scenes
- humans compose a story to explain the perceptual observations
- consider early captioning models in fig 6: they describe most objects correctly, but fail to understand deeper phenomena like forces, mental states of the people, r/s between objects (i.e. doesnt build the right causal model of data)
![](assets/Pasted%20image%2020240709191005.png)

#### Attempts to learn causal models with NNs
- attempts back then were still primitive, quite likely not good enough for characters challenge
- Incorporating causality may greatly improve deep learning models as they were trained without causal data about how characters are produced, and do not have any incentive to learn the true causal process
- building one for Frostbite is challenging as it requires gluing object repr, explaining interations with intuitive phys and psych. NNs could play a role by
	- serving as a bottom-up proposer to make probabilistic inference (inverting causal model, explain pixels as objects and their interactions, such as the agent stepping on an ice floe to deactivate it) more tractable in a structured generative model
	- serving as the causal generative model if imbued with the right set of ingredients

### Rapid model building ingredient 3: Learning to learn
- When humans or machines make
inferences that go far beyond the data, strong prior knowledge
(or inductive biases or constraints) must be making up
the difference (Geman et al. 1992; Griffiths et al. 2010;
Tenenbaum et al. 2011). learning to learn is one way people acquire such priors
- NNs: entire field of meta-learning
- impt for learning-to-learn to occur at multiple levels of the hierarchical generative process, to reuse and combine previously learned primitives and larger generative pieces to define new generative models for new characters (Fig. 5A).
- Learn **typical levels of variability** within a typical generative model to know how far and in what ways to generalize (recall in few or single shot learning, not enough info to calculate variance). 
	- (side note: in the early days of RL before target networks, deep-learning based Q functions can vary their estimates by orders of magnitudes. learning typical levels of variability would have helped)
- example of learning-to-learn in humans for learning new object models in vision and cognition: novel two-wheeled vehicle in Figure 1B, where learning-to-learn can operate through the transfer of previously learned parts and relations (sub-concepts such as wheels, motors, handle bars, attached, powered by) that reconfigure compositionally to create a model of the new concept. 
- "If deep neural networks could adopt similarly compositional, hierarchical, and causal representations, we expect they could benefit more from learning to-learn."

#### Frostbite challenge
- interdependence b/w form of repr and effectiveness of learning to learn: ppl transfer knowledge n exploit compositionality at multiple levels, from low-lvl perception to high-lvl strategy
	- eg parsing of env into objects, types of obj, causal relations (instead of raw pixels)
- people understand that games have goals, which often involve approaching or avoiding objects based on their type (eg types of animals in the game)
	- general world knowledge and previous video games may help inform exploration and generalization in new scenarios, helping people learn maximally from a single mistake or avoid mistakes altogether
#### State of deep RL and learning to learn
Impressive successes in transfer learning but nowhere near learning to play new games as quickly as humans can: “Actor-mimic” (Parisotto et al. 2016) first learns 13 Atari games by watching an expert network play and trying to mimic the expert network action selection and/or internal states (for about four million frames of experience each, or 18.5 hours per game). This algorithm can then learn new games faster (1-2 million frames) than a randomly initialized DQN (4-5 million frames)

#### Summary
- interaction between representation and previous experience may be key to building machines that learn as fast as people. 
- A deep learning system trained on many video games may not, by itself, be enough to learn new games as quickly as people.
- if such a system aims to learn compositionally structured causal models of each game – built on a foundation of intuitive physics and psychology – it could transfer knowledge more efficiently and thereby learn new games much more quickly.

## Thinking Fast
- richer n more structured models require more complex n slower inference algo, so its remarkable how humans can perform such inference so quickly.
-  combination of rich models with efficient inference suggests another way psychology and neuroscience may usefully inform AI. 

### (learning to) approx inference in structured models
- Hier Bayesian models operating over probabilistic programs (Goodman et al. 2008; Lake et al. 2015a; Tenenbaum et al. 2011) are equipped to deal with theory-like structures and rich causal representations of the world, but challenge: Computing a probability distribution over an entire space of programs (or even finding a single high-probability program) is usually intractable
- In contrast, while representing intuitive theories and structured causal models is less natural in deep neural networks, recent progress has demonstrated the remarkable effectiveness of gradient-based learning in high-dimensional parameter spaces. A complete account of learning and inference must explain how the brain does so much with limited computational resources (Gershman, Horvitz, & Tenenbaum, 2015; Vul, Goodman, Griffiths, & Tenenbaum, 2014).
- Popular algorithms for approximate inference in probabilistic machine learning have been proposed as psychological models (see Griffiths et al. 2012 for a review). 
	- Most prominently, it has been proposed that humans can approximate Bayesian inference using MC methods: sample the space of possible hypotheses and evaluate these samples according to their consistency with the data and prior knowledge (Bonawitz et al. 2014; Gershman et al. 2012; Ullman et al. 2012b; Vul et al. 2014). 
	- MC sampling has been invoked to explain behavioral phenomena (see citations in ppr)
	- we are beginning to understand how such methods could be implemented in neural circuits (Buesing et al. 2011; Huang & Rao 2014; Pecevski et al. 2011)
- MC methods are powerful n come w asymptotic guarantees, but still challenging to make them work on complex probs like program induction n theory learning.
- when hypothesis space is vast n only a few of them are consistent w data, how to discover good models without exhaustive search?
- sometimes humans dont have a clever soln and have to grapple with the full combinatorial complexity of theory learning, so process is slow
- however sometimes theory learning can be fast: eg when learning Frostbite, there will be some 'aha' moments when interacting with env, eg seeing the color change in ice floes as the char jumps on it, or gaining points when getting the fish. these fragments of "Frostbite theory" are assembled to form a causal understanding of the game relatively quickly, which seems more like a guided process than arbitrary proposals in an MC inference scheme 
	- in such domains where prog/ theory learning occurs quickly, its possible that humans use inductive biases not only to eval hypothesis, but also to guide hypothesis selection. eg constraints (the ans to "where is ..." must be on a map, the ans to "what year ..." must be a measurement of time and not length). this is supported by studies where children can use high level abstract features of a domain to guide hypothesis selction, by reasoning about distributional or dynamical properties n r/s between cause n effect

#### Learning efficient mappings from qns to plausible ans
theme: amortizing probabilistic inference into efficient feedforward mapping (Eslami et al. 2014; Heess et al. 2013; Mnih & Gregor, 2014; Stuhlmüller et al. 2013). can also think of this as “learning to do inference,”. some strategies:
- using paired generative/ recognition networks (Dayan et al. 1995; Hinton et al. 1995) 
- variational optimization (Gregor et al. 2015; Mnih & Gregor 2014; Rezende et al. 2014), 
- nearest-neighbor density estimation (Kulkarni et al. 2015a; Stuhlmüller et al. 2013).

implication of amortization: solutions to different problems will become correlated because of the sharing of amortized computations.
- Some evidence for inferential correlations in humans was reported by Gershman and Goodman (2014). 

This trend is an avenue of potential integration of deep learning models with probabilistic models and probabilistic programming: 
- neural networks for probabilistic inference in a generative model or a probabilistic program (Eslami et al. 2016; Kulkarni et al. 2015b; Yildirim et al. 2015). 
- differentiable programming (Dalrymple 2016), by ensuring that the program-like hypotheses are differentiable and thus learnable via gradient descent
### Cooperation b/w MB and MF RL
#### MFRL in the brain
- substantial evidence that the human brain uses similar MF learning algos in simple associative learning or discrimination learning tasks (see Niv 2009, for a review). 
- phasic firing of midbrain dopaminergic neurons is qualitatively (Schultz et al. 1997) and quantitatively (Bayer & Glimcher 2005) consistent with the reward prediction error that drives updating of model-free value estimates.
- also: see the neuroscience chapter in Barto n Sutton
#### MBRL in the brain
- considerable evidence that MBRL also occurs in the brain, responsible for building a "cognitive map" of env and using it to plan action sequences
- MB planning is an essential ingredient of human intelligence, enabling flexible adaptation to new tasks n goals
- Thought expt: if we make a Frostbite clone with modified rewards, a competent player can easily shift behavior with little to no additional learning, and it is hard to imagine a way of doing that without a MB planning approach where the env model is modularly combined with the new reward fn and then deployed immediately for planning
- boundary condition on flexibility: skills can be "habitized" with repetition, possibly reflecting a shift from MB to MF ctrl, maybe due to rational arbitration between learning systems to balance tradeoff b/w flexibility and speed
	- thought: is this like the learning of procedural / motor skills?
#### Cooperation between the systems
- similar to amortization of probabilistic computations earlier, plans can be amortized into cached values by allowing the MB system to simulate training data for the MF system (Sutton 1990).
- Economides et al. 2015 demonstrated that model-based behavior becomes automatic over the course of training

#### intrinsic motivation
- plays impt role in human learning n behavior
- not just optimizing for external rewards; rather to reinterpret ext rewards according to "internal value" of agent, which may depend on current goal and mental state
	- thought: long horizon tasks are hard under 'standard' RL because the rewards dont come until a very late timestep. instead, in hierarchical RL, there are intrinsic rewards to guide the agent to achieve a subgoal, which is similar to the description above
- intrinsic drive to reduce uncertainty and construct models of env, closely related to learning to learn and multitask learning


---

# Looking forward
## Promising directions in deep learning
interest in integrating psychological ingredients with deep neural networks: lower-level than the key cognitive ingredients discussed in this article, yet they suggest a promising trend of using insights from cognitive psychology to improve deep learning, one that may be even furthered by incorporating higher-level cognitive ingredients.

### Selective attention
(reminder: this article was written in 2016!)
- incorporation of attn has led to substantial performance in variety of domains
- attn can help in several ways
	- focus on smaller sub-tasks
	- allow larger models to be trained without requiring every model param to affect every output / action
		- of generative NN: use attn to concentrate on generating particular regions of image rather than whole img at once
- This could be a stepping stone toward building more causal generative models in NNs

### Working memories
Augment the shorter-term memory provided by unit activation and the longer-term memory provided by the connection weights.

#### Trend: Differentiable programming
the incorporation of classic data structures, such as RAM, stacks, and queues, into gradient-based learning systems 
- eg neural turing machine: trained to perform sequence-to-sequence prediction tasks such as sequence copying and sorting
- differentiable neural computer: applied to solving block puzzles and finding paths between nodes in a graph after memorizing the graph.
- neural programmer-interpreters learn to represent and execute algorithms such as addition and sorting from fewer examples, by observing IO pairs (like the NTM and DNC), as well as execution traces (Reed & Freitas 2016). 
	- Each model seems to learn genuine programs from examples, albeit in a representation more like assembly language than a high-level programming language.

Differentiable programming suggests the intriguing possibility of combining the best of program induction and deep learning. 
- The types of structured representations and model building ingredients discussed in this article – objects, forces, agents, causality, and compositionality – help explain important facets of human learning and thinking, yet they also bring challenges for performing efficient inference (sect. 4.3.1). 
- Deep learning systems have not yet shown they can work with these representations, but they have demonstrated the surprising effectiveness of gradient descent in large models with high-dimensional parameter spaces. 
- A synthesis of these approaches, able to perform efficient inference over programs that richly model the causal structure an infant sees in the world, would be a major step forward in building human-like AI.

### Combining pattern recognition and model-based search
- Alphago: CNN + MCTS
- Comment: 2023 on: the rise of LLMs + planning eg MCTS (in [LATS](LATS.md), MCTS-R etc)
- Just as important are the new questions and directions they open up for building human-like AI.

### Efficient mastery challenge
Build an AI system that beats a world-class player with the amount and kind of training **human** champions receive.

AlphaGo effectively learns from 100 million or more games altogether (D. Silver, personal communication, 2017), while Lee Sedol has probably played around 50,000 games in his entire life.

### Efficient game learning challenge
- Could AI model these earliest stages of real human learning curves (learn basics of game by playing just a few games)?
- Could AI quickly adapt to new Go (or other game) variants as fast as humans?
	- value fns in AlphaGo unlikely to generalize as flexibly as humans, might require significant retraining directed by humans rather than the system itself -- it doesnt really 'understand' the game the same way humans do
	- humans can adapt quickly because they explicitly represent Go as a game, with a goal to beat an adversary who is playing to achieve the same goal he or she is, governed by rules about how stones can be placed on a board and how board positions are scored.
	- Humans represent their strategies as a response to these constraints, such that if the game changes, they can begin to adjust their strategies accordingly
	- some signs in the LLM era: [neurally guided + planning search](https://deepmind.google/research/publications/139455/)

### Go as an AI challenge
- in attempting the challenges above, humans are learning-to learn with compositional knowledge, using their core intuitive psychology and aspects of their intuitive physics (spatial and object representations), and integrating model-free pattern recognition with model-based search.
- as such, Go can be a compelling testbed for AI to demonstrate the ingredients mentioned in the article

## Future applications to practical AI problems

### Scene understanding
For example, picture a cluttered garage workshop with screw drivers and hammers hanging from the wall, wood pieces and tools stacked precariously on a work desk, and shelving and boxes framing the scene. 
- For an autonomous agent to effectively navigate and perform tasks in this environment, the agent would need **intuitive physics** to properly reason about stability and support. 
- A holistic model of the scene would require the **composition** of individual object models, glued together by **relations.** 
- **causality** helps infuse the recognition of existing tools or the learning of new ones with an understanding of their use, helping to connect different object models in the proper way (e.g hammering a nail into a wall). 
- If the scene includes people acting or interacting, it will be nearly impossible to understand their actions without thinking about their thoughts and especially their goals and intentions toward the other objects and agents they believe are present.
### Autonomous agents and intelligent devices
- cant possibly pretrain on all possible concepts they may encounter (i guess LLMs are the attempts at these)
- Like a child learning the meaning of new words, an intelligent and adaptive system should be able to learn new concepts from a small number of examples, as they are encountered naturally in the environment. 
- Common concept types include 
	- new spoken words (eg names), 
	- new gestures (a secret handshake and a “fist bump”),
	- new activities
- a human-like system would be able to learn both to recognize and to produce new instances from a small number of examples.
- a system may be able to quickly learn new concepts by constructing them from pre-existing primitive actions, informed by knowledge of the underlying causal process and learning-to-learn.

### Autonomous driving
- requires intuitive psychology. 
- Beyond detecting and avoiding pedestrians, autonomous cars could more accurately predict pedestrian behavior by inferring mental states, including 
	- their beliefs (e.g., Do they think it is safe to cross the street? Are they paying attention?) 
	- and desires (e.g., Where do they want to go? Do they want to cross? Are they retrieving a ball lost in the street?). 
- Similarly, other drivers on the road have similarly complex mental states underlying their behavior (e.g., Does he or she want to change lanes? Is he or she distracted?). 
- This type of psychological reasoning, along with other types of model-based causal and physical reasoning, are likely to be especially valuable in challenging and novel driving circumstances for which there are few relevant training data (e.g., navigating unusual construction zones, natural disasters).

### Creative design
- we see compositionality and causality as central to this goal. 
- Many commonplace acts of creativity are unexpected combinations of familiar concepts or ideas (Boden 1998; Ward 1994). 
	- Figure 1-iv, novel vehicles can be created as a combination of parts from existing vehicles, and similarly, novel characters can be constructed from the parts of stylistically similar characters, or familiar characters can be re-conceptualized in novel styles (Rehling 2001). 
- In each case, the free combination of parts is not enough on its own: Although compositionality and learning-to-learn can provide the parts for new ideas, causality provides the glue that gives them coherence and purpose.

---

# Other
## Caveats
any computational model of learning must ultimately be grounded in the brain’s biological neural networks.
### Future NNs might be diff from today's
- They may be endowed with 
	- intuitive physics, 
	- theory of mind, 
	- causal reasoning, 
	- other capacities described later
- More structure and inductive biases could be built into the networks or learned from previous experience with related tasks
- Networks may learn to effectively search for and discover new mental models or intuitive theories, and in turn enable subsequent learning, allowing systems that metalearn – using previous knowledge to make richer inferences from very small amounts of training data.
### Other advances in AI not covered (but are compatible with ideas here)
some of the most exciting recent progress has been in 
- new forms of probabilistic machine learning (Ghahramani 2015). 
- automated statistical reasoning techniques (Lloyd et al. 2014),
- automated techniques for model building and selection (Grosse et al. 2012),
- probabilistic programming languages (e.g., Gelman et al. 2015; Goodman et al. 2008; Mansinghka et al. 2014). 

## Cognitive and neural inspiration in AI

### Parallel distributed processing (PDP) 
McClelland et al. 1986; Rumelhart et al. 1986b

- "parallel computation by combining simple units to collectively implement sophisticated computations"
- deep learning shares same repr commitments and often even same learning algos as earlier PDP models
- PDP perspective is compatible with “model building” in addition to “pattern recognition.” Some of the original work done under the banner of PDP (Rumelhart et al. 1986b) is closer to model building than pattern recognition!

### subsymbolic
"Proponents of this
approach maintain that many classic types of structured
knowledge, such as graphs, grammars, rules, objects, structural
descriptions, and programs, can be useful yet misleading
metaphors for characterizing thought. These structures
are more epiphenomenal than real, emergent properties of
more fundamental sub-symbolic cognitive processes"
## On biological plausibility vs cognitive models
Note the distinction of neuroscience (studying at the level of neuronal models) and cognitive science (studying at the level of cognitive functions and processes). Their view when biological plausibility collides with cognitive models:
- pragmatic view: understand software before the hardware, and this paper focuses on the software
- cognitive approach should not ignore what we know about the brain, and neuroscience can provide valuable inspiration to cognitive models and AI researchers, eg NN and MFRL in the "thinking fast" section.
- What we "know" about the brain is not that clear cut: Claims of biological plausibility or implausibility usually rest on rather stylized assumptions about the brain that **can be wrong** in many of their details. Moreover, these claims usually pertain to the cellular and synaptic levels, with few connections made to systems-level neuroscience and subcortical brain organization (Edelman 2015). 
- Understanding which details matter and which do not requires a computational theory (Marr 1982). 
- They **prioritize cognitive plausibility over biological plausibility** as a result, and **can use the former to disqualify the latter** rather than the other way around
	- eg backprop is argued to be biologically implausible, but it does not stop people from implementing cognitive models via deep learning
	- eg Hebbian learning mechanisms such as long-term potentiation (LTP) and spike-timing dependent plasticity (STDP) are biologically supported, but the cognitive significance of Hebbian learning is unclear (see that paragraph for various studies on how Hebbian learning mechanisms are insufficient in explaining learning esp on timescales)

- In the long run, we are optimistic that neuroscience will eventually place more constraints on theories of intelligence. 

### My take
Similar, see heuristic argument section in [cognitive-architectures](../concepts/cognitive-architectures.md). In various physical systems, understanding the 'effective theory' may be more useful than going down to the more microscopic details.

## Why isnt language more prominent here?
### Ingredients here are confirmed and necessary for language
- lang builds on core capabilities for intuitive phys / psych, and rapid learning w compositional, causal models. However, these capacities are in place before children master language, and they provide the building blocks for linguistic meaning and language acquisition. (Carey 2009; Jackendoff 2003; Kemp 2007; O’Donnell 2015; Pinker 2007; Xu & Tenenbaum 2007).
- What else might we need to add to these core ingredients to get language? see paragraph for list of speculations. Is it some **new version of these things (speculations), or is it just more of the aspects of these capacities that are already present** in infants? 
- should keep in mind all the ways that acquiring lang extends and enriches the ingredients of cognition in this article (see para for various ways n studies)
- my thought: can have intelligence without language, eg vid on monkey being able to drive a golf cart and navigate

### They did not intend for their list to be complete: Future work and opinions
- some argue language should be included in any list of key ingredients for human intellect eg Mikolov et al. 2016
- The question is, how do we develop machines with a richer capacity for language?
- We hope that by better understanding these earlier ingredients and how to implement and integrate them computationally, we will be better positioned to understand linguistic meaning and acquisition in computational terms and to explore other ingredients that make human language possible.
- Ultimately, the full project of building machines that learn and think like humans must have language at its core.
### Thoughts
- its 2024 and while LLMs are good at language in terms of fluency, it occasionally fails at some reasoning tasks and is exceptionally data and energy inefficient -- does suggest that the ingredients argued in this article are still important and sometimes missing