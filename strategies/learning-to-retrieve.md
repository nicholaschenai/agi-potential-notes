- [Auxiliary Cross Attention Network](https://www.frontiersin.org/journals/psychology/articles/10.3389/fpsyg.2025.1591618/full) train network to calculate attention between agent state and memory chunks (episodic & semantic)
- [Self-RAG](https://arxiv.org/abs/2310.11511)
- [memento](../papers/memento.md) train 2 layer MLP policy for episodic retrieval
- [Learning to Use Episodic Memory](https://web.eecs.umich.edu/~soar/sitemaker/docs/pubs/LearningToUseEpMem-ICCM09.pdf) Gorski & Laird 2009 ICCM, (Episodic)

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
