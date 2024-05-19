# Why cognitive architecture (CA)?
(i'll use examples from [[soar]] a lot, but in general, other cognitive architectures also seek to replicate variants of the concepts described below)
## Bio-inspiration
Why train a large model to learn cognitive processes when we can explicitly implement those that we already understand? Here are some examples of cognitive processes replicated in CAs

### Working memory
- temporary store of info for reasoning, decision-making and behavior
	- impt for other processes like reading comprehension, problem solving

### Distinct Long-Term memories
- procedural memory for skills and procedures
- declarative memory (semantic, episodic) for facts and knowledge
- in humans, such knowledge are distinct and have different properties: eg knowing how to ride a bicycle (procedural) is much stickier than recalling what you ate for lunch 4 days ago (episodic) or random trivia (semantic)

### Chunking
- grouping skills as a single unit

## Heuristic argument
In complex systems, emergent behaviors can arise and these can often be described by macroscopic variables that do not require low-level microscopic details (eg statistical mechanics, renormalization group theory, systems biology, network science). Might this also extend to AI, where it might not be necessary to simulate neurons at the fundamental level, as we can model the resultant phenomena?

# Limitations of CA
- we don't fully understand all cognitive processes -- so we still need to have components which can learn arbitrary processes