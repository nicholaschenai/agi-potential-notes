# Symbolic distillation
Distilling a NN as much as possible into symbolic form (explicit, interpretable). For example:
- Semantic: Facts about the world
- Procedural: Code-based skills
- maybe decision procedures?

Input: NN. output: NN + programs + knowledge, where the system performs similar as original NN
## Motivation
- Store symbolic components in a separate system to be as interpretable/ editable / controllable as possible, so NN is only for things that are hard to declare (e.g. how to swim)

## Current approaches
### Transformer programs
- https://arxiv.org/abs/2106.06981 RASP **is a symbolic programming language that allows us to write some computational steps using transformer primitives**
- https://arxiv.org/pdf/2301.05062 tracr **compiles these RASP programs into actual transformers**
- https://arxiv.org/abs/2306.01128 "Learning Transformer Programs" converts RASP programs to human-readable code.
### Mech interp 
(for discovery/localization of knowledge and skills)
- see [notes](../../interp-notes/README.md)
## Potential approaches
### Discover then delete
- find a circuit that can be represented by code
- include the code as tool so NN can call the code
- include some term to encourage tool use rather than rely on internal weights
	- eg apply standard weight regularization during finetuning on its own trajectories (iterative loop of finetune then inference to collect trajectories), weights responsible for the program could atrophy due to lack of use. 
		- Since the circuit has already been discovered, we know where to monitor
### Symbolic regression
- Given procedural trajectories (eg trajectories of an NN policy), use symbolic regression techniques to recover code/equations?
## Detection
- less things to unwrap in SAE (sparser) if program distillation is successful (if we train NN which has option to use program)

## Potential benefits
The symbolic structures in a neuro-symbolic system open up the possibility of symbolic reasoning not available in a fully neural system. Some of the below are speculations
- Formal verification of symbolic components?
- Analysis (e.g. graph analysis) for early warning of agent's impact?
- Grow the symbolic side, see [symbolic-growth](symbolic-growth.md)
- Memory as control:  model is capable enough to execute tasks, but not so capable to do so without in-context learning
	- extreme case: procedural-only NN by having CA retrieve semantic just enough to reason to complete task