# Improving Prompts
- [Promptbreeder](https://arxiv.org/abs/2309.16797) 
	- Improve both task and mutation prompt via genetic algo. 
	- "compositional task-specific initialization of mutation-prompts, subsequent online mutation of mutation-prompts, uses special mutation operators that take into account the whole population and elite history, and uses diversity-maintenance methods"
	- early expts: LLMs do not understand fitness values, but other ppr suggests that LLMs do.
	- diversity by lying to LLM via reversing order fitness, to counter recency bias (!?)
- [Automatic Prompt Engineer](https://arxiv.org/abs/2211.01910) 
	- "one generator-prompt to generate prompt candidates, and another mutation-prompt to mutate them"
	- "generating an initial distribution of prompts using another prompt that infers the problem from a number of input-output examples from the dataset.", stabilizes after 3 iterations
- [self-discover](../papers/self-discover.md)