---
date: 2025-10-12
time: 16:03
author:
title:
created-date: 2025-10-12
tags:
source:
zks-type: lit
is_public: true
---
# Ebbinghaus forgetting curve
## Core principles
- Rate of forgetting: memory retention decreases over time
- Time and mem decay: curve is steep at the beginning, then rate slows down. exponential decay
$$R=e^{-t/S}$$ where R is the retrievability (retention) and S is the relative memory strength
- spacing effect: information is better retained when learning sessions are distributed over time rather than massed together. Each review session creates a new, stronger memory trace with improved long-term retention
- Overlearning: continued study after material has been learned to criterion. While overlearning provides immediate benefits for retention, research shows these advantages diminish significantly over meaningful time periods
- Meaningful Material Effect: Information that connects to existing knowledge, has emotional significance, or personal relevance is forgotten at a slower rate than meaningless material
---
## Modern updates / refinements
### Dual Process Theory Integration
distinguishes between recollection (free recall) and familiarity (aided by familiarity cues) processes. 
- Studies show that recollection declines rapidly over time following the classic forgetting curve pattern, 
- while familiarity remains relatively stable. 
- This finding explains why recognition memory follows a different pattern than free recall 
- why 2 processes? familiarity is a fast, automatic process that isnt effortful (for quick recognition) while recollection is a slower, more controlled process that retrieves specific details. division of labor for computational efficiency, serve complementary purposes
#### familiarity
- Familiarity is a type of memory retrieval process that provides a **general sense** of having encountered something before, without the ability to recall specific contextual details about when or where the encounter occurred. It represents a feeling of "oldness" or recognition that an item has been previously experienced
- familiarity reflects a global measure of memory strength or stimulus recency, while recollection involves retrieving specific qualitative information about a study episode, such as contextual details of when or where an event occurred
##### what increases familiarity?
- processing time during encoding benefits both recollection and familiarity
	- elaborative processing (such as semantic encoding or making meaningful connections) has much larger effects on recollection than familiarity
- Multiple exposures to an item increase its familiarity by boosting the item's memory strength value
- Processing fluency (how easily something is processed) manipulations, such as subliminal priming of test items, increase the likelihood that items will be judged as familiar
- recent exposure
- Unlike recollection, which requires forming episodic bindings with contextual details, familiarity can be strengthened through simpler activation of item representations
### Memory Consolidation Theory
- memories initially depend on the hippocampus but gradually become independent through neocortical consolidation over weeks to years. 
- The consolidation process involves both synaptic consolidation (hours) and systems consolidation (weeks to years), providing a neurobiological basis for Ebbinghaus's observation

### Context and Retrieval Conditions
Modern research emphasizes that forgetting curves vary significantly based on 
- retrieval conditions, 
- learning context, 
- material type. 

The testing effect literature has shown that retrieval-based learning can dramatically alter forgetting curves, particularly for meaningful material that connects to existing knowledge networks

### Individual Differences in Forgetting

Recent research has identified significant individual variations in forgetting rates (eg shape n steepness) based on factors such as 
- verbal comprehension, 
- working memory capacity, 
- cognitive styles. 

---

## Implementations in AI
- [memorybank](../papers/memorybank.md) forgetting curve
- Memory consolidation in NN (spacing when learning)
	- "Do Your Best and Get Enough Rest for Continual Learning" View-batch model
	- "Human-like Forgetting Curves in Deep Neural Networks"
- Meaningful Material Processing
	- Larimar [larimar](../papers/larimar.md) has associative memory modules that leverage semantic relationships to improve retention of meaningful information over arbitrary data
- Adaptive Learning and Overlearning
	- [Knowledge tracking models](https://ieeexplore.ieee.org/document/10589981)models the learner's state and incorporate forgetting factors including time intervals, repetition frequency, and knowledge mastery