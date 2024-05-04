---
date: 2024-05-04
time: 13:26
author: 
title: "Do As I Can, Not As I Say: \rGrounding Language in Robotic Affordances"
created-date: 2024-05-04
tags: 
paper: https://arxiv.org/pdf/2204.01691
code: 
zks-type: lit
---
# Intro
LLM pro: has a lot of semantic knowledge about the world, useful to robots that act upon high level, long horizon tasks expressed in natural lang

LLM con: Not embodied, spills over to decision making in embodied agents. (recall: musicians can tell if a piece is AI generated because it is hard to play IRL, eg requires hands to move in unnatural ways across the instruments)
# Description of result
eval on real-world robotic tasks
- show the need for real-world grounding 
- shows their work can perform long-horizon, abstract, natural language instructions on a mobile manipulator

![](assets/Pasted%20image%2020240504155947.png)

![](assets/Pasted%20image%2020240504155959.png)

---
# How it compares to previous work
foo

---
# Main strategies used to obtain results
- LM provides high-level semantic knowledge about the task (algo 1 line 5). 
- "affordances (value fns) provide probabilities of successfully executing each skill" algo 1 line 6 (grounding)
- combine this with low-level skills to perform complex and long time horizon instructions (algo 1 line 7)

![](assets/Pasted%20image%2020240504155905.png)

![](assets/Pasted%20image%2020240504155818.png)

![](assets/Pasted%20image%2020240504155831.png)

## Skill instantiation
- each skill has a policy, value fn and lang desc
- trained using image based behavioral cloning (BC-Z) or RL (MT-Opt) via TD backups
- "While we find that the BC policies achieve higher success rates at the current stage of our data collection process, the value functions provided by the RL policies are crucial as an abstraction to translate control capabilities to a semantic understanding of the scene." sparse reward of 1 and 0 for success n fail, rated by humans
- "In order to amortize the cost of training many (low level) skills, we utilize multi-task BC and multitask RL, respectively, where instead of training a separate policy and value function per skill, we train multi-task policies and models that are conditioned on the language description"

## LM details
- use sentence ebd of skill desc as input to policy and value fn (not necessarily the same LM for planning

## Control policies
- "we study a diverse set of manipulation and navigation skills using a mobile manipulator robot. Inspired by common skills one might pose to a robot in a kitchen environment, we propose 551 skills that span seven skill families and 17 objects, which include picking, placing and rearranging objects, opening and closing drawers, navigating to various locations, and placing objects in a specific configurations." 
- " In this study we utilize the skills that are most amenable to more complex behaviors via composition and planning as well as those that have high performance at the current stage of data collection"


---

# Other
- "We also demonstrated an exciting property of our method where a robot’s performance can be improved simply by enhancing the underlying language model"
- "even though SayCan allows the users to interact with the agents using natural language commands, the primary bottleneck of the system is in the range and capabilities of the underlying skills"
- "system is not easily able to react to situations where individual skills fail despite reporting a high value, though this could potentially be addressed by appropriate prompting of the language model for a correction."