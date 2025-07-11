---
date: 2025-07-06
time: 02:58
author: 
title: https://arxiv.org/abs/2501.13956
created-date: 2025-07-06
tags: 
paper: "Zep: A Temporal Knowledge Graph Architecture for Agent Memory"
code: https://github.com/getzep/graphiti
zks-type: lit
---
- Zep: memory layer service for AI agents
	- Solution for LT mem, ctx mgmt and retrieval
- Graphiti: Underlying engine of Zep 
	- **Temporal-aware** KG representing declarative (semantic + episodic) mem
	- knowledge graph construction, temporal extraction, edge invalidation, and community detection.
- Motivation: Data can be voluminous and time-dependent, need RAG solution (since voluminous data cant fit into context) that accommodates for this setting

## Description of result

- 94.8% on Deep Memory Retrieval benchmark with gpt-4-turbo
- Up to 18.5% higher accuracy on LongMemEval benchmark while reducing response latency by 90%

---
## How it compares to previous work
- Embeddings: BGE-m3 model
- retrieval k=20 most relevant edges (facts) and entity nodes
### Deep Memory Retrieval (DMR)
- 500 multi-session convos, 
	- each with 5 chat sessions w up to 12 msgs/session
	- Each convo has a QA pair for eval
- Models
	- gpt-4o-mini-2024-07-18 for graph construction,
	- and both gpt-4o-mini-2024-07-18 and gpt-4o-2024-11-20 for the chat agent generating responses to provided context.
	- gpt-4-turbo-2024-04-09 for direct comparison to MemGPT
![](assets/Pasted%20image%2020250711173729.png)
- Result: 94.8% vs 93.4% against MemGPT on Deep Memory Retrieval benchmark with gpt-4-turbo
- Limitations of benchmark: 
	- small scale, 
	- reliance on single-turn fact-retrieval questions, 
	- ambiguous phrasing, 
	- poor representation of real-world enterprise use cases
### LongMemEval
- addresses limitations of DMR, with convos averaging 115k tokens in length
- Authors couldnt not get successful question responses from MemGPT (need to adapt framework to expt scenario)

![](assets/Pasted%20image%2020250711174324.png)
![](assets/Pasted%20image%2020250711174336.png)

- Up to 18.5% higher accuracy on LongMemEval benchmark over full context with only 1.6k avg tokens used compared to 115k for full ctx

### Other
- I thought it should at least compare to [arigraph](arigraph.md) since it is similar (as mentioned in the paper) 
	- Arigraph is Also a declarative KG that is temporarily updated

---
## Main strategies used to obtain results
- Incremental, real-time updates
- Temporal metadata for conflict resolution and historical state maintenance

### Temporally-aware Knowledge Graph Representation
Organized into these 3 hierarchies from lowest to highest
#### Episodic subgraph
- Nodes are raw events 
	- messages (focus of the paper)
	- text
	- JSON
- Episodic edges connect episodes to extracted entity nodes
- Bi-temporal model tracks both
	- chronological order of events
	- transactional order of data digestion for database auditing.
#### Semantic subgraph
- Entities extracted from episodes are resolved with existing graph entities. 
- Semantic edges represent relationships between these entities.

#### Community Subgraph
- Represents clusters of strongly connected entities with high-level summarizations. 
- Community edges connect communities to their entity members

### Graph construction
Start with episode ingestion (raw data) with timestamps
#### Entity Extraction and Resolution
- From current msg content and last $n(=4)$ messages
	- Perform NER
	- Speaker automatically extracted as entity
- After initial entity extraction, reflection technique to enhance coverage
- Extract entity summary from episode to facilitate subsequent entity resolution and retrieval
- Entities vectorized and a separate full text search performed for candidate node identification
- Candidate notes with episode context are sent to LLM for entity resolution + duplicate detection
	- if duplicate found, generates of updated names and summaries
- Data incorporated into KG with predefined Cypher queries (Neo4j) for consistent schema
	- avoid LLM generated queries in case of hallucination

#### Fact (relationship / edge) Extraction and Resolution
- Facts extracted, same fact can be extracted multiple times between different entities (can model complex multi-entity facts via hyper-edges)
- Embed facts
- Edge deduplication (similar to entity resolution) by constraining hybrid search to edges between the same entity pairs
	- prevents erroneous combinations of similar edges between different entities
	- reduces computational overhead by limiting search space

#### Temporal Extraction and Edge Invalidation
- Temporal information (absolute and relative timestamps) extracted from episode
- 4 timestamps tracked on edges
	- Chronological timeline: temporal range of validity and invalidity (when facts held true)
	- Transactional timeline: Time of creation and expiry
- LLM reasoning to compare new edges vs semantically related existing ones for invalidation (check for contradictions)

### Dynamic community detection
- Label propagation algo for straightforward dynamic extension instead of Leiden algo
	- Dynamic updating reduces latency and LLM inference costs by delaying the need for full community refreshes
		- Periodic community refreshes remain necessary
- New entity node is assigned to the community most frequent among its neighbors, and community summary + graph is updated
- Community nodes contain summaries derived from an iterative map-reduce-style summarization of member nodes. 
- Community names are generated and vectorized for search
### Retrieval
- Main search to identify candidate nodes and edges:
	- returns a 3-tuple of semantic edges, entity nodes and community nodes
	- Search types
		- semantic search via ebd 
		- keyword search via BM25
		- BFS to identify additional nodes and edges within n-hops for contextual similarity
			- can accept nodes as params for search, e.g. use recent episodes as seeds
	- Search fields:
		- Facts for semantic edges
		- Entity name for entity nodes
		- Community name for community nodes
			- comprises relevant keywords and phrases covered in the community

- Rerank via
	- Reciprocal rank fusion
	- maximal marginal relevance
	- Graph-based episode-mentions (prioritize by frequency of mentions)
	- Node distance from centroid node for context localized to specific areas of KG
	- Cross-encoders (LLMs) to generate relevance scores by evaluating nodes and edges against queries using cross-attention (highest computational cost)
- Constructor: transform result to formatted text. includes
	- facts with valid date ranges
	- entity names and summaries 
	- community summaries
