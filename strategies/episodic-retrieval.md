- Recency score (eg [gen-agents](papers/gen-agents.md) and other episodic mem implentations)
- Graph score (eg [arigraph](papers/arigraph.md))
- cardinality of match (num elements that cue and mem have in common, [arigraph](papers/arigraph.md), [enhancing-agents-episodic](papers/enhancing-agents-episodic.md))
- activation level of working mem elements in the cue that match the mem ([enhancing-agents-episodic](papers/enhancing-agents-episodic.md), Nuxoll & Laird 2004)

## Activation-Based Selection
The use of activation levels, similar to those in human memory, can determine the salience of working memory elements. Items with higher activation levels, indicating greater relevance or recency, are more likely to be retrieved from episodic memory (Laird, 2008, Nuxoll & Laird 2007). Can also apply to learning
- activation Increase when production rules fire due to that WME, or agent attempts to add existing WME [enhancing-agents-episodic](../../../gh-notes/agi-potential-notes/papers/enhancing-agents-episodic.md)
- In [gen-agents](../../../gh-notes/agi-potential-notes/papers/gen-agents.md), activation is based on recency (with decay and expiry), importance (poignancy) and relevance (dense ebd). Actually they also have something like frequency (keyword strength)
- ==needs citation==: measure of novelty
## Optimizations

### Contextual Indexing
Storing memories with contextual tags can facilitate quicker retrieval by allowing the system to filter memories based on the current context or task requirements. 
- [gen-agents](../../../gh-notes/agi-potential-notes/papers/gen-agents.md) has keyword filter to retrieve related events n thoughts

