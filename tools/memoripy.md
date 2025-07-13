---
date: 2025-07-13
time: 19:17
author: 
title: 
created-date: 2025-07-13
tags: 
paper: 
code: https://github.com/caspianmoon/memoripy
zks-type: lit
---
## Features
- long and short term memory
- Memory decay/reinforcement via frequency of access
- semantic clustering
- auto concept extraction via LLM
- networkX graph for associations
	- Bidirectional weighted edges

## Storage
- FAISS-based indexing
- JSON/InMemory/DynamoDB backends
## Retrieval
- Ebd similarity + concepts
- generic retrieval: similarity score is adjusted by decay factor and reinforcement (access_counts) factor
### semantic retrieval
- find similarity with cluster centroid, then from the highest scoring cluster, retrieve interactions 
### spreading activation
- during generic retrieval, query concepts (nodes) are added to a set (activated nodes) with base activation of 1
- neighbors of these nodes which arent in the set are added with an activation that is the edge weight modified by decay factor and activation of that node
- loop this for n=2 steps
## organization
- hierarchical k-means clustering

## Mem dynamics
- Time-based exponential decay
- reinforcement
	- access-based
	- when attempting to add existing edge between concepts, weight is incremented instead
- automated short to long term mem transition
	- via access count threshold
