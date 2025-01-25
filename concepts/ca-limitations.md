# Limitations, challenges and caveats of cognitive architecture and biological inspiration
- we don't fully understand all cognitive processes -- so we still need to have components which can learn arbitrary processes
- our understanding of cognitive processes can be wrong or insufficient
- Biological inspiration at the fine-grained level can sidetrack us from using alternate building blocks to achieve the same effective phenomena (eg biological implausibility of backpropagation should not discourage the use of modern deep learning techniques to replicate important phenomena)
## We need to be cognizant of the differences between brains and computers
Brains: constrained capacity, energy budget
- our cognitive processes are influenced by a constrained energy budget, leading to cognitive shortcuts and fallacies (see "Thinking, Fast and Slow") which is not desirable for AI. At the same time, we need to think about how to make the best out of the extra energy afforded to AI
- brains prune memories due to limited capacity, while storage in computers are cheap and scalable -- might there be ways to take advantage of this? (but need to deal with a larger search space)
- We need to take advantage of the massive (deliberate) parallelism afforder to computers (brains do parallel processing but much harder to control)

## Current challenges

- see [Challenges](papers/soar.md#Challenges)
- [gen-agents](papers/gen-agents.md): "However, their space of action was limited to manually crafted procedural knowledge, ..."