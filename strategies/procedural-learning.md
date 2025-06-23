# Procedural learning
Recall that there are these available options to the agent, each of which corresponds to a procedure itself:
- Learning (for each LT memory type)
- Retrieval (for each LT memory type)
- Grounding action (action that interacts with env)
- Reasoning

So we have learning to learn, learning to retrieve, learning to .. etc

## Learning grounding actions: When and how to learn procedural skills?
- attempt task with code and store if task success [voyager](papers/voyager.md)
- attempt task with code and store if task success and critic rates it at least 8/10 [oscopilot](papers/oscopilot.md)
- write code only when LM determines that existing tools cannot solve task [LATM](papers/LATM.md)
- synthesis and compression of code [LILO](../papers/LILO.md)
- After every test run, sample thought and action trajectories, use LLM/rules to extract reusable workflows (trajectory templates) [agent-workflow-memory](../papers/agent-workflow-memory.md)
### How to discover rules to learn?
- https://en.wikipedia.org/wiki/Association_rule_learning

## Learning to reason
- [improving-prompts](improving-prompts.md)
- Finetuning
- Wave of learning to CoT works (e.g. openAI o1, deepseek R1)

## Learning to retrieve
- [Auxiliary Cross Attention Network](https://www.frontiersin.org/journals/psychology/articles/10.3389/fpsyg.2025.1591618/full) train network to calculate attention between agent state and memory chunks

### Trend: Test-time learning to memorize (and retrieve)
Main reason: Pairwise attention in transformers is $N^2$, need more efficient solutions for long context
- [Titans: Learning to Memorize at Test Time](https://arxiv.org/abs/2501.00663) 
	- 3 components
		- persistent memory (fixed at test time)
		- contextual memory (updates during test time)
		- core memory (in-context learning)
	- effectively scales to 2m context
	- surprise-based encoding to update contextual (long-term) memory
	- adaptive forgetting mechanism
- [ATLAS: Learning to Optimally Memorize the Context at Test Time](https://arxiv.org/abs/2505.23735)
	- "Typically, no persistent learning or skill acquisition carries over to new, independent global context once the memory is cleared"
