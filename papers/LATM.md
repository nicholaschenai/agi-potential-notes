---
date: 2024-07-31
time: 17:32
author: 
title: LARGE LANGUAGE MODELS AS TOOL MAKERS
created-date: 2024-07-31
tags: 
paper: 
code: "https://github.com/\rctlllll/LLM-ToolMaker"
zks-type: lit
---

# Description of result
Identifying existing tools: Over five random constructions of the test set, the accuracy in correctly determining the suitable tool is 95% ± 2%.

Requesting tool-making: Over multiple runs, the accuracy of making the correct
decision stands at 96% ± 3%

The above results illustrate that the dispatcher can effectively recognize existing tools and accurately
request tool-making for unseen tasks, all while maintaining high performance.

---
# How it compares to previous work
- conceptually close to Voyager: use strong LM to write fns to solve problems
- main diff from existing works: "our dispatcher distinctively contributes to creating a functional cache—it discerns new tasks that cannot be resolved with existing tools, thereby triggering the tool maker to generate appropriate tools for these tasks."

---
# Main strategies used to obtain results
![](assets/Pasted%20image%2020240731173441.png)

## Dispatcher
- maintains a repository of existing tools crafted by the tool maker in the format of function APIs. 
- Upon receipt of a new task instance, the dispatcher first attempts to locate a compatible tool within the cache.
	- If such a tool is present, the dispatcher assigns the instance and corresponding tool to the tool user for resolution. 
	- if no suitable tool is available, the dispatcher identifies this as a novel task, either solving it with a powerful model or, if necessary, invoking a human labeler.
- These new instances are then cached until a sufficient number are amassed to craft a new tool, further enriching the functional cache. 
- This mechanism allows for the functionally similar tasks to reuse these tools, expanding the coverage of the classic cache mechanism and reducing the overall serving cost. 
- Given the simplicity of the dispatching task, a lightweight model equipped with appropriate prompts (See Appendix C) can efficiently serve as the dispatcher, adding only a marginal cost to the entire pipeline.