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
- [Formal Pseudocode](#formal-pseudocode)
- [Results](#results)
- [Critical Analysis](#critical-analysis)
- [Impact and Follow-up Work](#impact-and-follow-up-work)
- [Live Demo](#live-demo)
- [Resources](#resources)
- [Citation](#citation)


## Overview

**Context:** Large Language Models (LLMs) like GPT-3 and PaLM have demonstrated impressive fluency in natural language generation. However, despite their linguistic power, these models often struggle with tasks that require structured, multi-step reasoning—for example, arithmetic word problems, symbolic manipulation, and logical inference. Before this work, prompting strategies focused mostly on phrasing or example selection rather than explicitly guiding the reasoning process itself.

**Problem:** LLMs are trained to predict the next word rather than to reason explicitly. As a result, they tend to jump straight to final answers that sound plausible but are often incorrect on problems that require intermediate steps. The authors ask a simple but powerful question:

Can we elicit reasoning behavior from language models purely through prompting, without retraining or changing the architecture?

**Idea:** The paper introduces Chain-of-Thought (CoT) prompting, a method that encourages the model to generate intermediate reasoning steps in natural language before giving the final answer. By adding a short cue such as “Let’s think step by step,” the model shifts from “answer-only” behavior to “reason-then-answer.” This makes its reasoning process explicit and interpretable to humans.

**Experiments:** The authors tested CoT prompting across multiple reasoning benchmarks, including arithmetic (GSM8K), symbolic reasoning, and commonsense inference. They found:

Large performance gains at scale. PaLM-540B accuracy on GSM8K improved from 17.9% → 58.5%.

Emergent capability. Smaller models showed limited improvement, suggesting reasoning emerges only at higher capacities.

No fine-tuning required. The improvement arises solely from the prompt itself.

Interpretability. The generated reasoning chains allow researchers to trace how the model reached its conclusions.

**Why it matters:** This study reshaped how researchers think about reasoning in LLMs. It showed that reasoning can be elicited linguistically, not just engineered architecturally. CoT prompting became the foundation for follow-up work such as Self-Consistency, Tree-of-Thought, and ReAct, which all build on the same principle of encouraging explicit reasoning paths.

**One-sentence summary:** Chain-of-Thought prompting transforms LLMs from “answer generators” into “step-by-step reasoners,” improving multi-step problem solving without retraining or architectural changes.


## Formal Pseudocode

*Note:* Chain-of-Thought (CoT) prompting introduces **no architectural or training modification** to the Transformer model. The change occurs entirely at the **inference/prompting stage**. The pseudocode below adapts Algorithm 14 (*DInference*) from *Phuong & Hutter (2022)* to show where the CoT cue is inserted. Algorithms 10 and 14 from that paper (shown in the figures below) describe the baseline Transformer and its inference loop.

**Reference Algorithms (Phuong & Hutter, 2022)**  
- Algorithm 10 — *Decoder-Only Transformer (GPT)*  
- Algorithm 14 — *Prompting and Inference Procedure*

<img width="727" height="544" alt="Screenshot 2025-11-06 at 8 46 50 AM" src="https://github.com/user-attachments/assets/2af7761b-c45a-41f0-9d6b-8dff18cff4a5" />

<img width="355" height="355" alt="Screenshot 2025-11-06 at 8 46 18 AM" src="https://github.com/user-attachments/assets/f03e304e-37fe-4c4f-854d-2de0f52b63e8" />

*Figures: Baseline Transformer architecture (Algorithm 10) and standard inference (Algorithm 14) from Phuong & Hutter (2022). CoT modifies only the input prompt in Step 1 of inference.*

**Algorithm:** `CoTInference(x, θ̂)` — prompting a trained Transformer to elicit reasoning traces through a Chain-of-Thought cue.

**Input:**  
 `x ∈ V*` — user query or question  
 `θ̂` — trained Transformer parameters  

**Output:**  
 `y ∈ V*` — generated reasoning trace and final answer  

**Hyperparameters:**  
 `τ ∈ (0, ∞)` — sampling temperature  
 `L_gen ∈ ℕ` — number of generation steps  

Algorithm:

1. Compose prompt
$\text{prompt} \leftarrow x ; \Vert ; \text{+" Let’s think step by step."}$

2. Tokenize input
$\text{tokens} \leftarrow \text{tokenize}(\text{prompt})$

3. Iteratively generate reasoning and answer
  for $i = 1, \ldots, L_{\text{gen}}$ do
    $P \leftarrow \text{DTransformer}(\text{tokens} \mid \hat{\theta})$ → Forward pass
    $p_{\text{next}} \leftarrow P[:, |\text{tokens}|]$ → Next-token distribution
    $y_i \leftarrow \text{sample}(p_{\text{next}}, \text{temperature} = \tau)$
    $\text{tokens} \leftarrow [\text{tokens}, y_i]$
    if $\text{EOS}(y_i)$ then break
    end for

4. Return decoded text
$y \leftarrow \text{detokenize}(\text{tokens})$

5. return $y$

Comment:
Relative to Algorithm 14: DInference (standard Transformer decoding), this procedure prepends a short reasoning cue (e.g., “Let’s think step by step.”) before inference.
This change occurs entirely at the prompting stage, guiding the model to produce intermediate reasoning steps before its final answer, without modifying model weights or architecture.

**Question 1:**  
1) What is the difference in architecture between a Chain-of-Thought (CoT) model and a standard language model, and what are some alternative ways—beyond the method used in the paper—to create a CoT-capable model?  
<details>
<summary><i>Click to reveal possible discussion points</i></summary>

- **No architectural change:** CoT prompting uses the *same Transformer architecture* as standard models; the difference lies entirely in how the *prompt* elicits reasoning, not in the network design or training.  
- **Prompt engineering:** CoT adds a short cue such as *“Let’s think step by step”* to encourage explicit intermediate reasoning before the final answer.  
- **Fine-tuning approaches:** Instead of prompting, a model can be **fine-tuned** on datasets that include reasoning traces to internalize CoT behavior.  
- **Reinforcement or self-training:** Use **Reinforcement Learning from CoT outputs** or **self-distillation**, where a base model learns from its own reasoning examples.  

</details>

---


## Results

High-level summary from the paper (see original for full details):

<img width="643" height="306" alt="Screenshot 2025-11-10 at 6 34 57 PM" src="https://github.com/user-attachments/assets/402f8bbe-701c-4d93-9f07-8b1ad36ce272" />

<img width="643" height="306" alt="Screenshot 2025-11-05 at 12 46 57 PM" src="https://github.com/user-attachments/assets/6063c66e-79ca-4e2c-a226-b2d9637c0c26" />

| Benchmark (examples)        | Standard Prompt | CoT Prompt |
|-----------------------------|----------------:|-----------:|
| MultiArith                  |            17.7%|     78.7%  |
| GSM8K                       |            19.2%|     57.9%  |
| StrategyQA                  |            68.0%|     75.4%  |
| Last-Letter Concatenation   |            35.0%|   ~99.0%   |

### Scale Dependence of Chain-of-Thought Prompting


| **Model** | **Parameters** | **Benchmark** | **Standard Prompt (%)** | **CoT Prompt (%)** | **Observation** |
|------------|----------------|----------------|--------------------------|--------------------|-----------------|
| GPT-2 | 1.5 B | MultiArith | ~18 | ~19 | Minimal or no improvement at small scale. |
| GPT-3 | 175 B | GSM8K | 17.9 | 40.7 | Moderate gains; reasoning begins to emerge. |
| PaLM | 540 B | GSM8K | 18 | 57 | Major jump in accuracy; reasoning emerges only at large scale. |

**Summary.**  
Performance under Chain-of-Thought prompting remains flat for small and mid-sized models but rises sharply at the **540 B-parameter** scale, confirming reasoning as an **emergent capability** in large language models.

**Question 2:**  
1) Why might CoT prompting yield strong gains only at large model scales?  
<details>
<summary><i>Click to reveal possible discussion points</i></summary>

- Larger models have **higher capacity** to represent and follow complex reasoning paths.  
- CoT relies on **emergent behavior**—reasoning patterns that only appear once models reach a certain scale.  
- Smaller models can mimic reasoning linguistically but lack the **latent computation depth** to perform it reliably.  

</details>

Observations:
- CoT improves performance across arithmetic, symbolic, and commonsense tasks.
- Gains are most pronounced at large model scales (emergent behavior).
- Reasoning traces make intermediate steps inspectable.


## Critical Analysis

Strengths
- Prompt-only technique; no retraining or architectural changes.
- Improves interpretability via visible reasoning traces.
- Demonstrates emergent reasoning at large scales.

Limitations
- Scale dependence: strong gains typically require very large models.
- Reasoning traces may be verbose, inconsistent, or partially incorrect. (stuck chain)
- Automatic evaluation of reasoning quality is impossible in this form.

## Impact and Follow-up Work

This paper marked a turning point in how researchers conceptualize reasoning in large language models. Before CoT, reasoning was viewed as an architectural or training problem—something that required fine-tuning or symbolic modules. Wei et al. (2022) showed that *reasoning can be elicited purely through language*, transforming prompting from a surface-level technique into a pathway for genuine logical and numerical reasoning. This insight reshaped prompting research and established interpretability and step-by-step reasoning as central goals for the next generation of LLMs.

The paper catalyzed a research line on explicit reasoning with LLMs. Representative follow-ups include:
- Self-Consistency (Wang et al., 2022): sample multiple CoTs and vote.
- Tree-of-Thought (Yao et al., 2023): search over multiple reasoning paths.
- Tool-augmented reasoning (e.g., Toolformer, 2023): call tools during reasoning.
- Reasoning-oriented training (2024–): finetuning/recipes to make CoT more reliable.

Future Directions:
- Interactive or Reflective CoT: Develop models that revise or critique their own reasoning chains before finalizing an answer. This could reduce logical inconsistencies and hallucinations.
- Cross-Model Reasoning Collaboration: Use multiple models with different architectures or training data to compare and reconcile reasoning paths, similar to peer review between LLMs.
- Reasoning under Resource Constraints: Explore lightweight or compressed CoT models that can perform multi-step reasoning efficiently on edge devices or smaller architectures.
- Reasoning-oriented training (2024–): finetuning/recipes to make CoT more reliable.

Takeaway.
The next frontier for CoT-style prompting lies in making reasoning more self-correcting, collaborative, and efficient, bridging the gap between interpretability and real-world usability.

## Live Demo

Notebook: `demo/cot_demo.ipynb`

## Resources

- Paper (arXiv): https://arxiv.org/abs/2201.11903
- Google AI Blog overview of Chain-of-Thought prompting
- Self-Consistency (Wang et al., 2022): https://arxiv.org/abs/2203.11171
- Tree-of-Thought (Yao et al., 2023): https://arxiv.org/abs/2305.10601
- Prompt engineering documentation (OpenAI or equivalent)

## Citation

Plain-text: Wei, J., Wang, X., Schuurmans, D., Bosma, M., Ichter, B., et al. “Chain-of-Thought Prompting Elicits Reasoning in Large Language Models.” arXiv:2201.11903, 2022.


