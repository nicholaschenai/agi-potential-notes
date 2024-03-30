---
date: 2024-03-30
time: 13:37
author: "Michihiro Yasunaga,1,2 Xinyun Chen,1 Yujia Li,1 Panupong Pasupat,1 Jure Leskovec,2\rPercy Liang,2 Ed H. Chi,1 Denny Zhou1"
title: LARGE LANGUAGE MODELS AS ANALOGICAL REASONERS
created-date: 2024-03-30
tags: 
paper: https://arxiv.org/pdf/2310.01714.pdf
code:
---

# Description of result
"Experimental results show that our approach outperforms 0-shot CoT and manual fewshot CoT in a variety of reasoning tasks, including math problem solving in GSM8K and MATH, code generation in Codeforces, and other reasoning tasks in BIG-Bench."

---
# How it compares to previous work
foo

---
# Main strategies used to obtain results
![](assets/Pasted%20image%2020240330133809.png)

"The underlying idea here is that modern LLMs have already acquired knowledge of various problems during training. Explicitly prompting them to recall relevant problems and solutions in the context guides LLMs to perform in-context learning to solve new problems"