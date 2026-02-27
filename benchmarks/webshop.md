---
date: 2026-02-21
time: 13:30
author:
title: "WebShop: Towards a Generalist Agent for the Web"
created-date: 2026-02-21
tags:
paper: https://arxiv.org/abs/2207.01206
code: https://github.com/princeton-nlp/WebShop
zks-type: lit
is_public: true
site: https://webshop-pnlp.github.io/
---
## Description of result
- web env with prodts scraped from amazon and attributes annotated
- has 2 types of env, `WebAgentTextEnv` (Text/Simple Mode) and `WebAgentSiteEnv` (HTML/Site Mode)
### `WebAgentTextEnv` (Text/Simple Mode)
This is purely a text env, HTML is simulated

```python
import gym
from web_agent_site.envs import WebAgentTextEnv

env = gym.make('WebAgentTextEnv-v0', observation_mode='text', num_products=1000)
obs, info = env.reset()

# Inspect available actions at current state
available = env.get_available_actions()
# e.g., {'has_search_bar': True, 'clickables': ['search']}

# Take an action
obs, reward, done, info = env.step('search[red shoes]')
```
#### Action space
obtained via `env.get_available_actions()`, which returns
- **`has_search_bar`** (`bool`): Whether a search input is available on the current page.    
- **`clickables`** (`list[str]`): A list of text labels for all buttons/links that can be clicked on the current page

The action space is then
- **`search[keywords]`**: Issues a text query to the search engine. Only valid when the agent is on the search/home page (`has_search_bar: True`).
- **`click[value]`**: Clicks a named button or link (e.g., `click[Buy Now]`, `click[Size 9]`). Only valid on non-search pages, and `value` must be one of the strings in `clickables`. 
	- NOTE: The paper uses the term `choose` but the code uses `click`
- NOTE: above 2 are mutually exclusive

| Page Type        | `has_search_bar` | Available Actions                                                 |
| ---------------- | ---------------- | ----------------------------------------------------------------- |
| Search / Home    | `True`           | `search[query]`                                                   |
| Results page     | `False`          | `click[product title]`, `click[Next >]`, etc.                     |
| Item page        | `False`          | `click[option]` (e.g. `color`), `click[Buy Now]`, `click[< Prev]` |
| Item-detail page | `False`          | `click[< Prev]` to go back                                        |
All non-search pages have a button to go to the search page

### `WebAgentSiteEnv` (HTML/Site Mode)
This env uses an actual browser

- **`get_available_actions()`** returns **Selenium `WebElement` objects**, live references to actual DOM elements in the rendered browser page.
- **`step()`** accepts a `WebElement` to click (or a `(element, text)` tuple for filling in the search bar), rather than a text string.
- The agent must parse **raw HTML** observations and resolve which DOM element to interact with

## How it compares to previous work
foo

---
## Main strategies used to obtain results
foo
