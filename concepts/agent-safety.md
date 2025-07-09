Some thoughts on agent safety



1. Is it possible to guarantee AI safety by placing bounds / safeguards on the tools / skills rather than the model? (so the model can be malicious but its action space is constrained)
2. Can we abstract popular agent use cases (e.g. software engineering, web tasks) into a PDDL so that planning can be interpretable / verifiable?
3. Can the techniques behind structured output (e.g. ensuring JSON output) be used to constrain model outputs for tools to be within 'acceptable values' (e.g. when an agent is searching through a filesystem, it is prevented from outputting the .env secrets file as a parameter for whichever tool use)
4. Can existing graph analysis tools be repurposed to be an early warning indicator (e.g. check for agent's impact to the environment given its action, and see if its a few steps away from doing something catastrophic)
5. If we only allow a 'normal' (relative to the task) amount of thinking / action steps to an agent on a per-task basis, is that enough to prevent hidden schemes (since the agent has to somehow come up with a method to accomplish the task in less that the normal allotted steps and then scheme within the remaining steps)
6. If we find any circuit that is replaceable by code, can we distil that into code instead and require the agent to use that code so it is more transparent?
7. Can we mine internet-scale data for standard ranges of input parameters to tools (relative to the situation) and use that as a safeguard / warning trigger for human in the loop, if the agent deviates from such norms?
8. Can we distill a network so that the declarative (interpretable) memories (such as facts) remain in a separate system, while the neural network's function is strictly for things that are hard to declare (e.g. how to swim), so that the declarative component remains as interpretable and editable (eq via SQL operations) / controllable as possible? see [symbolic-distillation](symbolic-distillation.md)
9. Are there bounds for the capability of weaker models with respect to in-context learning? Goal is to make a model that is a fine balance of capable enough to execute tasks, but not so capable to do so without in-context learning -- so controlling the memories retrieved is a form of agent control
10. Will whatever we know about agent safety change in the recent trend of o1-style test-time compute and process reward modeling?
11. What are some ways to deliberately get the agents to develop causal models of the world so that its reasoning is more accurate?
12. Is it possible to train a neural network's theory of mind process to be deliberately represented as calling a copy of itself with modified memories (maybe deliberately make the model smaller so that it has no choice but to do so), so that any forms of scheming could be shown by the multiple theory of mind simulation calls?
13. Can fuzzing techniques be adapted to the agent's action space, so that we can automatically catch dangerous edge cases?
14. Can we integrate agents with homomorphic encryption so that it can still perform actions on data (e.g. just calling a function on the data), without it actually seeing the data? Prevents sensitive data leak (e.g. web agent accidentally posting sensitive data online) or any form of harm involving access to sensitive data
15. Is there an automated way to construct digital twins of whichever environments the agents operate in, to scale safety measures? e.g. OpenHands has a series of dockers to ringfence the agent's code execution but we want to find a way to automatically construct such dockers given any env
16. Can we show if having 'police' agents that monitor the main agent is / is not sufficient for safety?
17. Open-source role / permissions management for agents -- major cloud providers are already integrating agent infrastructure with roles and permissions, much like how employees in a company are given appropriate read/write access given their job scope. Can we have an open source version to make it easy for the community to adopt such safety measures?
18. Extensions to Voyager (Wang et. al. 2023): Can we get agents to learn explicit representations of more complex things like decision procedures and amount of compute / reasoning steps allocated?
19. Assuming we have an agent with (at least partially) a way to have explicit memories, can we use human-curated curriculums to train an agent to perform tasks and monitor the construction of explicit skills, so that there is a level of oversight? (In contrast to web-scale data which can be noisy or incorrect)
20. Monkey brains rewire after they learn to use a tool (e.g. robotic arm). What can mech interp tell us about the models that are tuned for tool use (which is highly adopted in agents), and is this change better or worse for safety?


---

## Papers
- [SELP](../../agent-security-notes/papers/SELP.md)