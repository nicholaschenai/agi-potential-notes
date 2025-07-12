---
date: 2025-07-12
time: 18:11
author: 
title: 
created-date: 2025-07-12
tags: 
paper: 
code: https://github.com/langchain-ai/langmem
zks-type: lit
---
Memory layer with native Langgraph integration


### Background memory manager 
(automatic, does not need agent decision). Apparently has some reflection module to extract insights?

```python
from langmem import create_memory_store_manager

memory_manager = create_memory_store_manager(
    "anthropic:claude-3-5-sonnet-latest",
    # Store memories in the "memories" namespace (aka directory)
    namespace=("memories",),  
)

@entrypoint(store=store)  # Create a LangGraph workflow
async def chat(message: str):
    response = llm.invoke(message)

    # memory_manager extracts memories from conversation history
    # We'll provide it in OpenAI's message format
    to_process = {"messages": [{"role": "user", "content": message}] + [response]}
    await memory_manager.ainvoke(to_process)  
    return response.content
# Run conversation as normal
response = await chat.ainvoke(
    "I like dogs. My dog's name is Fido.",
)
```


### Mem tools

```python
from langmem import create_manage_memory_tool, create_search_memory_tool
```

tools for managing and searching mem. like any agent tool, agent decides when to call them

### Mem API
- Memory managers for CRUD (esp removing outdated ones), consolidation and generalization
	- can be LM with schemas e.g. triplets, can be multiple schemas at once
- Prompt optimizers
- Can have (multi-level) namespaces e.g. group memories by organization, user, application, or any other hierarchical structure
```python
# Organize memories by organization -> configurable user -> context
namespace = ("acme_corp", "{user_id}", "code_assistant")
```
- Retrieval: via
	- key
	- semantic search
	- metadata filtering


## Mem representation
- Episodic: full state
	- Their examples use LM extraction with schema of (obs, thought, action, result)
- Semantic: Collection (append-only) or profile (modified upon update)
- Procedural: system prompts
	- has a way to optimize prompts


## Patterns
- `ReflectionExecutor` to batch memory processing via some delay
