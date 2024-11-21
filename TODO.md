- The Rise and Potential of Large Language Model Based Agents: A Survey [[Repo (lit review)](https://github.com/WooooDyy/LLM-Agent-Paper-List)]
### Toolformer: Language Models Can Teach Themselves to Use Tools
[[Paper](https://arxiv.org/abs/2302.04761)]
- Notes heavily adapted from stanford cs224n 2023 L15
- self supervised approach to teach models to use new tools
    - Start with a few examples of each tool and larger dataset for the task without tool use
    - in context learning to insert candidate API calls in training examples
    - Call APIs, evaluate whether the result decreases perplexity of the rest of the solution
    - Fine tune model on cases where it does

