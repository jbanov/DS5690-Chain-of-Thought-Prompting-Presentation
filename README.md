# DS5690-Chain-of-Thought-Prompting-Presentation
Repository for my DS5690 paper presentation on “Chain-of-Thought Prompting Elicits Reasoning in Large Language Models” (Wei et al., 2022). Includes walkthrough, pseudocode, demo notebook, and analysis.

# Chain-of-Thought Prompting Elicits Reasoning in Large Language Models

**Course:** DS 5690 Topics (Fall 2025)  
**Presenter:** Jacob Banov  
**Presentation date:** Nov 11, 2025  
**Paper:** Jason Wei, Xuezhi Wang, Dale Schuurmans, Maarten Bosma, Brian Ichter, et al. (2022)  
**Link:** https://arxiv.org/abs/2201.11903

## Table of Contents
- [Overview](#overview)
- [Key Questions for the Audience](#key-questions-for-the-audience)
- [Method Summary](#method-summary)
- [Formal Pseudocode](#formal-pseudocode)
- [Results](#results)
- [Critical Analysis](#critical-analysis)
- [Impact and Follow-up Work](#impact-and-follow-up-work)
- [Live Demo](#live-demo)
- [Repo Structure](#repo-structure)
- [Resources](#resources)
- [Citation](#citation)

## Overview

**Problem.** Large language models (LLMs) are fluent but often fail on tasks that require multi-step reasoning (arithmetic, symbolic manipulation, logical inference).

**Idea.** Elicit intermediate reasoning by adding a brief cue to the prompt (e.g., “Let’s think step by step.”). The model then produces a natural-language reasoning chain before the final answer. This is called **Chain-of-Thought (CoT) prompting**.

**Why it matters.** CoT substantially improves performance on reasoning benchmarks and exposes interpretable reasoning traces. The strongest gains appear at large model scales, suggesting an emergent capability.

**One-sentence summary.** CoT turns answer-only generation into “reasoning-then-answer,” improving accuracy on multi-step problems without changing model weights or architecture.

## Key Questions for the Audience

1) Why might CoT prompting yield strong gains only at large model scales?  
2) How could CoT prompting be adapted for multimodal tasks (e.g., vision-language reasoning)?

*(I will pause here during the talk for short responses.)*


## Method Summary

**Standard prompting**
- Input: question
- Output: direct answer

**Chain-of-Thought prompting**
- Input: question + short reasoning cue (e.g., “Let’s think step by step.”)
- Output: free-form reasoning trace → final answer

**Key points**
- No change to network architecture or training.
- The difference lies entirely in prompting and decoding.
- Final answer is extracted from the reasoning trace (heuristics or a follow-up instruction).



---

## Step 7 — Results

Summarize the headline gains. Do not over-claim; keep it high-level.

```markdown
## Results

High-level summary from the paper (see original for full details):

| Benchmark (examples)        | Standard Prompt | CoT Prompt |
|-----------------------------|----------------:|-----------:|
| MultiArith                  |            17.7%|     78.7%  |
| GSM8K                       |            19.2%|     57.9%  |
| StrategyQA                  |            68.0%|     75.4%  |
| Last-Letter Concatenation   |            35.0%|   ~99.0%   |

Observations:
- CoT improves performance across arithmetic, symbolic, and commonsense tasks.
- Gains are most pronounced at large model scales (emergent behavior).
- Reasoning traces make intermediate steps inspectable.
```

## Critical Analysis

Strengths
- Prompt-only technique; no retraining or architectural changes.
- Improves interpretability via visible reasoning traces.
- Demonstrates emergent reasoning at large scales.

Limitations
- Scale dependence: strong gains typically require very large models.
- Reasoning traces may be verbose, inconsistent, or partially incorrect.
- Automatic evaluation of reasoning quality is nontrivial.

What could be improved or extended
- Self-consistency: sample multiple CoTs and aggregate for robustness.
- Program-of-Thought and tool use: verify steps with code, calculators, or external tools.
- Multimodal extensions: align step-by-step reasoning across text and images.


## Impact and Follow-up Work

The paper catalyzed a research line on explicit reasoning with LLMs. Representative follow-ups include:
- Self-Consistency (Wang et al., 2022): sample multiple CoTs and vote.
- Tree-of-Thought (Yao et al., 2023): search over multiple reasoning paths.
- Tool-augmented reasoning (e.g., Toolformer, 2023): call tools during reasoning.
- Reasoning-oriented training (2024–): finetuning/recipes to make CoT more reliable.

Overall impact: shifted practice from answer-only prompting to reasoning-then-answer prompting for complex tasks.

## Live Demo

Notebook: `demo/cot_demo.ipynb`

Demo plan:
1) Ask a small algebra or symbolic task with a direct prompt and print the output.
2) Ask the same question with “Let’s think step by step.” and print the reasoning trace and final answer.

If API access is unavailable in class, the notebook includes saved outputs or a mocked response so it still runs.


## Resources

- Paper (arXiv): https://arxiv.org/abs/2201.11903
- Google AI Blog overview of Chain-of-Thought prompting
- Self-Consistency (Wang et al., 2022): https://arxiv.org/abs/2203.11171
- Tree-of-Thought (Yao et al., 2023): https://arxiv.org/abs/2305.10601
- Prompt engineering documentation (OpenAI or equivalent)

## Citation

Plain-text: Wei, J., Wang, X., Schuurmans, D., Bosma, M., Ichter, B., et al. “Chain-of-Thought Prompting Elicits Reasoning in Large Language Models.” arXiv:2201.11903, 2022.


