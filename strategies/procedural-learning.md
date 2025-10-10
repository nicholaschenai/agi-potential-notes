# Procedural learning
Recall that there are these available options to the agent, each of which corresponds to a procedure itself:
- Learning (for each LT memory type)
- Retrieval (for each LT memory type)
- Grounding action (action that interacts with env)
- Reasoning

So we have learning to learn, learning to retrieve, learning to .. etc

## Learning grounding actions: When and how to learn procedural skills?
- attempt task with code and store if task success [voyager](papers/voyager.md)
	- personal observation: unrestricted learning causes memory bloat which can displace other useful memories, and can result in uninformative memories
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
[learning-to-retrieve](learning-to-retrieve.md)