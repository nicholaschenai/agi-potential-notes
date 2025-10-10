---
date: 2025-10-10
time: 16:47
author:
title: "GAIA: a benchmark for General AI Assistants"
created-date: 2025-10-10
tags:
paper: https://arxiv.org/pdf/2311.12983.pdf
code: https://huggingface.co/gaia-benchmark
zks-type: lit
is_public: true
---
==draft mode==

"the answers to our questions are factoid, concise and unambiguous. These properties allow simple, fast and factual evaluation. Our questions are meant to be answered in zero shot, limiting the influence of the evaluation setup"
#### capabilities tested
- web browsing
	- eg Web browser, Search engine, Website widget access, Access to YouTube, Google Street View
- coding
	- eg Python, a calculator, Substitution cipher encoder, C++ compiler, A word reversal tool / script
- multi-modality
	-  eg speech-to-text tool, Video recognition, Image recognition, OCR, Google Street View
- diverse filetype reading
	- eg PDF viewer, Excel file access, PowerPoint viewer, CSV access, Txt file access.
- N/A: tools for tasks that can currently be performed by non-augmented LLMs. 
	- Examples: Tetris rules database, German translator, Spell checker, Text Editor, Bass note data.

 Example: working with xlsx files to answer data analysis qns

#### Difficulties
- Level 1 questions generally require no tools, or at most one tool but no more than 5 steps.
- Level 2 question generally involve more steps, roughly between 5 and 10 and combining different tools
is needed.
- Level 3 are questions for a near perfect general assistant, requiring to take arbitrarily long sequences of
actions, use any number of tools, and access to the world in general.

#### Baselines
- Humans score ~90% range for all 3 levels.
- Level 1: LMs ( + tools ) generally score in the 20-30% range
- Level 2: models score ~ 10%
- Level 3: models score ~0%

## Description of result
foo

---
## How it compares to previous work
foo

---
## Main strategies used to obtain results
foo
