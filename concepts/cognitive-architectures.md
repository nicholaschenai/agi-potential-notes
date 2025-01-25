# Why cognitive architecture (CA)?
(i'll use examples from [soar](../papers/soar.md) a lot, but in general, other cognitive architectures also seek to replicate variants of the concepts described below)
## Bio-inspiration
Why train a large model to learn cognitive processes when we can explicitly implement those that we already understand? Here are some examples of cognitive processes replicated in CAs

### Symbolic representation
- A feature that separates humans from other species

### Working memory
- temporary store of info for reasoning, decision-making and behavior
	- impt for other processes like reading comprehension, problem solving

### Distinct Long-Term memories
- procedural memory for skills and procedures
- declarative memory (semantic, episodic) for facts and knowledge
- in humans, such knowledge are distinct and have different properties: eg knowing how to ride a bicycle (procedural) is much stickier than recalling what you ate for lunch 4 days ago (episodic) or random trivia (semantic)
- while CAs do not necessitate the use of explicit memories, many do especially when applying rule-based procedures. Explicit memories have the advantage of explainability (eg seeing the timestamps and tracing when and how the memories and associations are formed), editability

### Chunking
- Grouping skills as a single unit

### Attention mechanisms
- CAs usually have explicit mechanisms to focus on specific info in working memory (usually influenced by current goals), reflective of human's attention mechanisms

### Other
- Could nature's solution, from years of evolution, be a good starting point in the extremely large search space of intelligence?
- Could bio-inspiration help us find energy-efficient architectures compared to the energy-hungry deep learning systems?

## Heuristic argument
In complex systems, emergent behaviors can arise and these can often be described by macroscopic variables that do not require low-level microscopic details (eg statistical mechanics, renormalization group theory, systems biology, network science). Might this also extend to AI, where it might not be necessary to simulate neurons at the fundamental level, as we can model the resultant phenomena?

## Other
- [building-machines](../papers/building-machines.md) argues for AI to have some 'start-up software' -- similar philisophy of CAs deliberately building up cognitive components (compared to tabula rasa style in deep learning)
- Similar to [Compound AI systems](https://bair.berkeley.edu/blog/2024/02/18/compound-ai-systems/) view by BAIR
	- beyond parametric updates (deep learning: knowledge is static after training)
	- better control with systems
- Are there even more useful ideas that were figured out way back in the past? (so we can avoid rediscovering their successes / failures?)

## CA + LLMs: Complementing each other
see last 2 slides in "[Improving Knowledge Extraction from LLMs for Task Learning through Agent Analysis](https://raw.githubusercontent.com/SoarGroup/website-downloads/main/workshops/44/talk.15.pptx)" at the 44th Soar Workshop 
- CAs: expensive to acquire new task knowledge (need to run many processes within the CA per task), LLMs have task knowledge from pretraining
- LLMs: hallucination, memorized reasoning and planning, lack grounding or environment context. CAs: techniques for grounding, verification, multistep reasoning, planning, and coordinating multiple knowledge sources


## Limitations, challenges and caveats of cognitive architecture and biological inspiration
- [ca-limitations](ca-limitations.md)