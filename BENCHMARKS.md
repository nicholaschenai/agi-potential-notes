# Benchmarks and Environments

## Environments
### Open ended game
- [MineDojo](https://minedojo.org/)

## Benchmarks
### QA

| Benchmark                                          | Year | Desc                                                                                                                                             | SOTA |
| -------------------------------------------------- | ---- | ------------------------------------------------------------------------------------------------------------------------------------------------ | ---- |
| [MoreHopQA](https://github.com/Alab-NII/morehopqa) | 2024 | Multihop that requires both factual extraction and additional reasoning on top of facts. Built on top of HotpotQA, 2Wiki-MultihopQA, and MuSiQue |      |
| [GPQA](https://github.com/idavidrein/gpqa/)        | 2023 | Grad level, 448 multiple-choice questions in biology, physics, and chemistry                                                                     |      |
| MMLU (Hendrycks et al., 2020)                      |      | diverse QA, including STEM                                                                                                                       |      |
| TimeQA (Chen et al., 2021)                         |      | SituatedQAtime sensitive knowledge                                                                                                               |      |
| SituatedQA (Zhang & Choi, 2021)                    |      | "open-retrieval QA dataset requiring the model to answer questions given temporal or geographical context"                                       |      |
| MuSiQue (Trivedi et al., 2022)                     |      | multihop reasoning                                                                                                                               |      |
| StrategyQA (Geva et al., 2021)                     |      | multihop reasoning, open domain                                                                                                                  |      |
| HotpotQA                                           |      | Multihop reasoning                                                                                                                               |      |
| ScienceQA                                          |      | Also has imgs so multimodal                                                                                                                      |      |

#### MathQA

| Name                                                   | Year | Description                                                                                              | open SOTA | closed SOTA    |
| ------------------------------------------------------ | ---- | -------------------------------------------------------------------------------------------------------- | --------- | -------------- |
| [GSM-Symbolic](https://arxiv.org/abs/2410.05229)       | 2024 | Eval framework to generate qns from GSM8K templates to evaluate if AI can really do math vs memorization |           |                |
| [Omni-MATH](https://arxiv.org/abs/2410.07985)          | 2024 | 4428 competition-level problems, 33 sub-domains and span more than 10 distinct difficulty levels         |           | 60.54% o1-mini |
| [MATH](https://huggingface.co/datasets/lighteval/MATH) | 2021 | 5000 train and 5000 test                                                                                 |           |                |
| [GSM8K](https://github.com/openai/grade-school-math)   | 2021 | Grade school math 7.5k train 1k test                                                                     |           |                |
|                                                        |      |                                                                                                          |           |                |
