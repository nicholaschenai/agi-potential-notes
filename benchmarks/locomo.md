---
date: 2025-07-27
time: 20:29
author: Adyasha Maharana, Dong-Ho Lee, Sergey Tulyakov, Mohit Bansal, Francesco Barbieri, Yuwei Fang
title: Evaluating Very Long-Term Conversational Memory of LLM Agents
created-date: 2025-07-27
tags: 
paper: https://arxiv.org/abs/2402.17753
code: https://github.com/snap-research/LoCoMo
zks-type: lit
---
==TODO: this summary is still WIP==

Use gen agents to generate LT dialogue
## Description of result
The dataset consists of ten conversations between two LLM agents with pre-assigned personalities, spanning multiple sessions over extended time periods. Each conversation is annotated for **question-answering**, **event-summarization**, and **multimodal-dialog-generation** tasks.

Some features include
### Temporal Span
- Conversations span multiple sessions over extended time periods
- Sessions are chronologically ordered with timestamps
- Supports evaluation of very long-term conversational memory

### Multimodal Support
- Contains references to images with captions and search queries
- Images themselves are not included (only URLs, captions, and search metadata)
- Supports multimodal dialog generation tasks

### Multiple Evaluation Tasks
1. **Question Answering**: Using conversation history as context
2. **Event Summarization**: Identifying significant events per speaker per session
3. **Multimodal Dialog Generation**: Generating contextually appropriate responses

Categories:
- Category 1 (Multi-Hop)
- Category 2 (Single-Hop)
- Category 3 (Temporal)
- Category 4 (Open-Domain)
- Category 5 (Adversarial)

---
## How it compares to previous work
foo

---
## Main strategies used to obtain results
foo

---

## Ensuring temporal coherence across chat sessions
==AI generated, verify!==

During the construction of the LOCOMO dataset, significant efforts were made to ensure temporal coherence across chat sessions, moving beyond just coherence within individual sessions. This was achieved primarily through the design of the generative pipeline and subsequent human verification.

Here's how temporal coherence was established and maintained across sessions:

- **Temporal Event Graphs**: Each virtual agent in the conversation was assigned a **temporal event graph (G)**. This graph consists of a sequence of life events (ei) for the agent, each associated with a specific date of occurrence (ti). Crucially, these event graphs include **causal connections (l = (ei, ej)) that illustrate the causal relationships among events and reflect a natural succession of events in an individual’s life**. These events are spread across a timeframe of 6 to 12 months, and are generated iteratively, with subsequent events being caused by earlier ones.
- **Agent Architecture for Long-term Memory**: The virtual agents incorporate a "reflect & respond" mechanism which uses both short-term and long-term memory. To induce long-term temporal narratives in the conversation, the agent's responses are **additionally conditioned on the subset of events from their temporal event graph that occurred between the last conversation session and the current one**. This means that events in an agent's life directly influence the topics and context of subsequent conversations, ensuring a continuous narrative across sessions. For instance, if a speaker acquired a new dog, subsequent conversations would naturally reflect events like playdates with other dogs. Similarly, if a speaker recently had an injury, later conversations would likely focus on their recuperation rather than adventurous activities, thus sustaining a coherent narrative over time.
- **Human Verification and Editing**: After the initial LLM-generated conversations, human annotators were tasked with manually filtering and refining the data. A key part of their role was to **edit the dialogue to eliminate long-term inconsistencies** and to **verify and edit for alignment between the event graphs and the content of the conversations**. This human oversight ensured that the conversations remained consistent with the evolving life events of the personas over time.

---

## LoCoMo Dataset Implementation Details

The dataset is stored in `locomo/data/locomo10.json` as a JSON array containing 10 samples (conversations).

### Dataset Statistics

| Metric | Min | Max | Average |
|--------|-----|-----|---------|
| Sessions per conversation | 19 | 32 | 27.2 |
| QA pairs per conversation | 105 | 260 | 198.6 |
| Total turns per conversation | 369 | 689 | 588.2 |

**Total dataset size**: ~10 conversations, ~1,986 QA pairs, ~5,882 conversational turns

## Data Format

Each sample represents a single conversation with the following structure:

### Schema

| Column Name | Data Type | Description |
|-------------|-----------|-------------|
| `sample_id` | string | Unique identifier for the conversation sample |
| `conversation` | dict | Contains the conversational data with sessions, speakers, and turns |
| `qa` | list[dict] | Question-answer pairs annotated for the conversation |
| `event_summary` | dict | Significant events for each speaker within each session |
| `observation` | dict | Generated observations for each session (for RAG evaluation) |
| `session_summary` | dict | Generated session-level summaries (for RAG evaluation) |

### Conversation Structure

The `conversation` field contains:

- **Speaker Information**:
  - `speaker_a`: Name of the first speaker
  - `speaker_b`: Name of the second speaker

- **Session Data** (chronologically ordered):
  - `session_<num>`: List of conversational turns for session number `<num>`
  - `session_<num>_date_time`: Timestamp for session number `<num>`

- **Turn Structure**: Each turn within a session contains:
  - `speaker`: Name of the speaker (either `speaker_a` or `speaker_b`)
  - `dia_id`: Dialog identifier (e.g., "D1:1")
  - `text`: Content of the dialog turn

- **Multimodal Content** (when present):
  - `img_url`: Link to the image (URLs only - images not included in release)
  - `blip_caption`: Caption generated by BLIP model for the image
  - `query`: Search query used to retrieve the image
  - `re-download`: Boolean flag for redownload status

### Question-Answer Structure

The `qa` field contains a list of QA pairs, each with:

- `question`: The question text
- `answer`: The expected answer
- `evidence`: List of dialog IDs that contain evidence for the answer (e.g., `["D1:3"]`)
- `category`: Question category (integer 1-5)

#### Adversarial Category
**NOTE**: For category 5 (adversarial), there is no `answer` (most of the time), only `adversarial_answer`.
`adversarial_answer` is not the answer! 
It is meant to be a different task where we have a distractor answer and the model has to choose 'no answer' over that. 

Also, the evidence part is the closest memory chunk that sounds like the answer but its not. 
For example, something happened to person A (eg bought new shoes), but the question asks the equivalent for person B, 
so the evidence is the memory chunk that says something happened to person A.

#### QA Categories Distribution

| Category | Count | Percentage |
|----------|-------|------------|
| 1 | 282 | 14.2% |
| 2 | 321 | 16.2% |
| 3 | 96 | 4.8% |
| 4 | 841 | 42.4% |
| 5 | 446 | 22.5% |

### Event Summary Structure

The `event_summary` field contains session-level event summaries:
- `events_session_<num>`: Dictionary with events for each session containing:
  - `Caroline`: Events related to speaker A
  - `Melanie`: Events related to speaker B  
  - `date`: Date information for the events

### Generated Annotations

The dataset includes pre-generated annotations for RAG evaluation:

- **Observations**: `session_<num>_observation` - Generated observations for each session
- **Session Summaries**: `session_<num>_summary` - Generated summaries for each session

**Note**: These generated annotations use `gpt-3.5-turbo` and differ from the manually annotated event summaries.

## Loading the Dataset

### Basic Loading
```python
import json

# Load the full dataset
with open('locomo/data/locomo10.json', 'r') as f:
    data = json.load(f)
    
print(f"Loaded {len(data)} conversations")

# Access a conversation
conversation = data[0]
print(f"Sample ID: {conversation['sample_id']}")
print(f"Speakers: {conversation['conversation']['speaker_a']} and {conversation['conversation']['speaker_b']}")
print(f"QA pairs: {len(conversation['qa'])}")
```

### Session Analysis
```python
# Analyze sessions in a conversation
conv_data = data[0]['conversation']
session_keys = [k for k in conv_data.keys() if k.startswith('session_') and not k.endswith('_date_time')]

print(f"Number of sessions: {len(session_keys)}")
for session_key in session_keys[:3]:  # First 3 sessions
    session = conv_data[session_key]
    timestamp = conv_data.get(f"{session_key}_date_time", "N/A")
    print(f"{session_key}: {len(session)} turns at {timestamp}")
```


---

## Update: Current SOTA
As of Mar 2026
- Most papers give evaluation metrics based on LLM-as-judge, which can vary depending on LLM and prompt used. 
	- various memory systems, some proprietary, can score 80-90+ % via this
- The original paper's metrics are used less, and so far doesn't seem to saturate
- For LLM-as-judge, according to [DyCP](https://arxiv.org/pdf/2601.07994), GPT 4.1 full context can get 92% score
	- still leaves out more recent models like GPT 5 series, Claude 4.5 series