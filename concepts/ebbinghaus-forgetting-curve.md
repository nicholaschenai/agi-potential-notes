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
- dual process theory distinguishes between recollection and familiarity processes. 
- Studies show that recollection declines rapidly over time following the classic forgetting curve pattern, while familiarity remains relatively stable. 
- This finding explains why recognition memory (aided by familiarity cues) follows a different pattern than free recall (dependent on recollection)

### Memory Consolidation Theory
- memories initially depend on the hippocampus but gradually become independent through neocortical consolidation over weeks to years. 
- The consolidation process involves both synaptic consolidation (hours) and systems consolidation (weeks to years), providing a neurobiological basis for Ebbinghaus's observation

### Individual Differences in Forgetting

Recent research has identified significant individual variations in forgetting rates (eg shape n steepness) based on factors such as 
- verbal comprehension, 
- working memory capacity, 
- cognitive styles. 

### Context and Retrieval Conditions
Modern research emphasizes that forgetting curves vary significantly based on 
- retrieval conditions, 
- learning context, 
- material type. 

The testing effect literature has shown that retrieval-based learning can dramatically alter forgetting curves, particularly for meaningful material that connects to existing knowledge networks

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