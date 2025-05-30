# Cognitive Architectures for Safe AI Development


Cognitive architectures provide a structured approach to building intelligent systems by modeling the structural and functional organization of human cognition. Originating from cognitive science and psychology, these architectures offer a promising pathway for developing safe AI systems through their emphasis on interpretability, modularity, and human-like reasoning processes.

## Introduction
- Brief intro [here](cognitive-architectures.md)
- Cognitive architectures view intelligence (and the construction of it) as emerging from the interaction of multiple specialized **components**.
- This approach allows for better interpretability (especially localization of capabilities) and manageability.
- Components can be independently analyzed and verified.
- In contrast, existing foundation model approaches often require post-hoc techniques for localization and steering of capabilities.
### Assumptions
- Modern cognitive architectures are neuro-symbolic, as fully symbolic systems were found to be too rigid for general use.

## Interpretability Through Modularity
- Cognitive architectures achieve safety through modular design.
    - Each component has a specific, well-defined role.
    - Interfaces between components are explicit and understandable.
- Modularity enables:
    - Independent inspection and verification of components.
    - Transparency in the decision-making process.  (execution trace)
    - Easier localization and editing during debugging.  (e.g. removing undesired behavior)

## Potential for Bounded Behavior
- By modeling human cognitive processes, cognitive architectures could inherit the limitations and safety properties of human reasoning, e.g.
    - Favor reasonable interpretations of limited data.
    - Guide safe exploration of new situations.
- Symbolic components allow for predictable behavior and potentially formal verification.
	- Such predictability allows researchers to know where to implement appropriate safeguards or analyze for failure modes. 
- Modularity aids in controlling system evolution and tracking knowledge acquisition.
	- (e.g. ensuring desirable properties are maintained during training, in contrast to neural networks which can experience catastrophic forgetting)
## Incremental Development and Testing
- The modular nature allows for independent development, testing, and verification of each component before integration.
- This reduces the risk of unexpected emergent behaviors.

## Potential for Symbolic Distillation
- Instead of relying on one large neural network, a system of smaller networks with symbolic components can reduce opaqueness.
- By equipping a large network with symbolic structures, it may be possible to prune away unnecessary neural functions over time, resulting in smaller, more interpretable networks in a still-capable system

## Potential for interpretable learning and corrigibility
- Cognitive architectures have a rich history of methods to represent learnt knowledge and skills in symbolic structures. 
- Such methods could be extended to neuro-symbolic structures, which are more interpretable than fully neural structures as in current foundation models.
- Even if the resulting learned knowledge is not desirable, it can be identified and edited easily. 