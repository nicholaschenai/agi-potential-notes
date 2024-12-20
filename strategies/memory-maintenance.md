## Maintaining internal knowledge

## Strategies for Pruning
- KG based pruning before addition [arigraph](papers/arigraph.md)
### Relevance-Based Pruning
Information that is no longer relevant or has not been accessed for an extended period can be pruned from episodic memory. This strategy is akin to the forgetting process in human memory and helps maintain a manageable memory size
- [gen-agents](../../../gh-notes/agi-potential-notes/papers/gen-agents.md) memory can decay and expire
### Procedural
- LM prompt based merging of successful sequences eg [GITM](papers/GITM.md)
- remove those that are learned, can be reconstructed (eg appears in other types of memories) and below activation thr [forgetting-soar-working-procedural-mem](../papers/forgetting-soar-working-procedural-mem.md)

### Working
- remove those that can be reconstructed (eg appears in other types of memories) and below activation thr [forgetting-soar-working-procedural-mem](../papers/forgetting-soar-working-procedural-mem.md)

## Strategies for efficient storage
which helps in retrieval
### Hierarchical Storage
Organizing memories into a hierarchy can make retrieval more efficient. More general memories might be stored at higher levels, with specific details at lower levels. This allows the system to navigate to the required level of specificity quickly. eg Episodic memory verbalization paper
### Chunking
Procedural in [soar](../../../gh-notes/agi-potential-notes/papers/soar.md). Also see [enhancing-agents-episodic](../../../gh-notes/agi-potential-notes/papers/enhancing-agents-episodic.md) where episode conditions are chunked, so in a sense the relevant episodic info is captured in the conditions of a rule

### Compression Techniques
Applying data compression techniques to episodic memory can reduce storage requirements without necessarily removing content. This approach can involve encoding episodes more efficiently or using algorithms to identify and eliminate redundancy
- [enhancing-agents-episodic](../../../gh-notes/agi-potential-notes/papers/enhancing-agents-episodic.md) stores changes between episodes rather than full episode


