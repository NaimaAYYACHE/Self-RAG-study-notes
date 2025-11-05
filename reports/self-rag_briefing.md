# Paper: Self‑RAG: Learning to Retrieve, Generate, and Critique through Self‑Reflection (Akari Asai et al., arXiv 2310.11511)
<img width="461" height="443" alt="Capture d’écran 2025-11-05 130453" src="https://github.com/user-attachments/assets/cf4923c8-990f-4b07-a029-fd3ebddc8bd4" />

## **Executive Summary**

This document outlines the *Self-Reflective Retrieval-Augmented Generation (SELF-RAG)* framework, a novel approach designed to enhance the quality, factuality, and controllability of *Large Language Models (LLMs)*.

Standard LLMs often suffer from factual inaccuracies, a problem partially addressed by *Retrieval-Augmented Generation (RAG)*. However, conventional RAG systems retrieve information indiscriminately, which can reduce model versatility or introduce irrelevant context, leading to poor-quality output.

SELF-RAG overcomes these limitations by training a single LLM to adaptively retrieve information on-demand and to *self-reflect* on its own generated content. This is achieved through the use of special **reflection tokens** that are generated alongside the text.

These tokens allow the model to decide when to retrieve, assess the relevance of retrieved passages, evaluate whether its own output is supported by evidence, and critique the overall quality.

This self-reflection mechanism makes the model's behavior controllable at inference time, allowing users to tailor its performance to specific task requirements (e.g., maximizing factual accuracy vs. creativity) without retraining.

Experiments demonstrate that SELF-RAG models (at 7B and 13B parameters) significantly outperform state-of-the-art LLMs, including ChatGPT and retrieval-augmented Llama2-chat, on a diverse set of tasks spanning open-domain question answering, reasoning, fact verification, and long-form generation.

Notably, SELF-RAG shows substantial gains in improving factuality and citation accuracy.

---

## **The Problem with Existing Approaches**

### **Large Language Models (LLMs)**

State-of-the-art LLMs, despite their scale and capabilities, continue to struggle with generating factually accurate content. Their reliance solely on parametric knowledge encapsulated during training leads to factual errors, commonly known as *“hallucinations.”*

### **Conventional Retrieval-Augmented Generation (RAG)**

Standard RAG methods augment LLM inputs with retrieved text passages to reduce factual errors. However, this approach has several fundamental weaknesses:

* **Indiscriminate Retrieval:** RAG models typically retrieve a fixed number of documents for every query, regardless of whether retrieval is necessary (e.g., for creative writing) or if the passages are relevant.

  
* **Reduced Versatility:** The constant injection of external text can hinder an LLM's versatility and creative capabilities on tasks that do not require factual grounding.

* **Low-Quality Generation:** Incorporating unnecessary or off-topic passages can distract the model and lead to low-quality, unhelpful responses.


* **Lack of Attribution:** The final output is not guaranteed to be consistent with the retrieved passages, as models are not explicitly trained to follow facts from the provided context. This results in poor citation accuracy.

---

## **The SELF-RAG Framework**

SELF-RAG is an end-to-end framework that trains an arbitrary LLM to improve its generation quality through *on-demand retrieval* and *self-reflection.*

The core innovation is teaching the model to generate special tokens, called **reflection tokens**, intermittently within its output. These tokens make the generation process *transparent* and *controllable.*

---

## **Key Concepts**

* **Adaptive Retrieval:** The model first determines if retrieval is necessary for a given prompt or generation segment. If so, it generates a `[Retrieve]` token to call a retriever model on demand.

* **Parallel Passage Processing:** After retrieval, the model processes multiple passages concurrently, generating a candidate output for each.

* **Self-Critique and Selection:** For each candidate output, the model generates critique tokens to evaluate the relevance of the source passage and the factual support for its own generation. It then uses these critiques to select the best-quality output segment.

* **Controllable Inference:** The generation of reflection tokens enables a customizable decoding algorithm, allowing users to adjust retrieval frequency and prioritize different quality aspects (e.g., factuality, relevance) at inference time.

---

## **Reflection Tokens**

SELF-RAG expands the LLM's vocabulary with four types of reflection tokens, which are used to signal the need for retrieval and to critique the quality of retrieved passages and generated text.

| **Type**     | **Input**    | **Output Values**                                    | **Definition**                                                                        |
| ------------ | ------------ | ---------------------------------------------------- | ------------------------------------------------------------------------------------- |
| **Retrieve** | `x` / `x, y` | `{yes, no, continue}`                                | Decides when to retrieve information with a retriever.                                |
| **ISREL**    | `x, d`       | `{relevant, irrelevant}`                             | Determines if a passage `d` provides useful information to solve the input `x`.       |
| **ISSUP**    | `x, d, y`    | `{fully supported, partially supported, no support}` | Assesses if all verifiable statements in the output `y` are supported by passage `d`. |
| **ISUSE**    | `x, y`       | `{5, 4, 3, 2, 1}`                                    | Rates whether the output `y` is a useful response to the input `x`.                   |

*Note:* `x` = input, `y` = output, `d` = retrieved passage. **Bold text indicates the most desirable critique tokens.**

---

## **Training and Inference Methodology**

### **Training Process**

The SELF-RAG training process involves two main stages and is designed to be more efficient than methods like *Reinforcement Learning from Human Feedback (RLHF).*

1. **Critic Model (C) Training:**

   * A dataset of reflection tokens is first created by prompting a proprietary model (GPT-4) with various inputs and outputs.
   * A critic model `C` (initialized from Llama 2-7B) is then trained on this dataset to learn how to generate the appropriate reflection tokens. This distills the judging capability into an open model, reducing API costs and improving reproducibility. The trained critic achieves over 90% agreement with GPT-4 on most token categories.

2. **Generator Model (M) Training:**

   * A diverse training corpus (150k instruction-output pairs) is augmented offline using the trained critic `C` and a retriever `R`.
   * For each training example, `C` inserts the appropriate reflection tokens and `R` adds relevant passages.
   * The final generator model `M` (initialized from Llama2 7B/13B) is trained on this augmented corpus using a standard next-token prediction objective. The model learns to generate both the task output and the reflection tokens simultaneously.

---

## **Controllable Inference**

At inference time, the generated reflection tokens enable fine-grained control over the model's behavior *without retraining.*

* **Adaptive Retrieval with Threshold:** Users can set a probability threshold for the `[Retrieve=Yes]` token to control how frequently the model seeks external information. A higher threshold reduces retrieval (suitable for creative tasks), while a lower threshold increases it for fact-intensive tasks.

* **Tree Decoding with Critique Tokens:** At each generation step requiring retrieval, the model performs a segment-level beam search:

  * It generates multiple candidate continuations based on different retrieved passages.
  * Each candidate is scored using a weighted linear sum of the probabilities of the critique tokens (`ISREL`, `ISSUP`, `ISUSE`).
  * By adjusting the weights, users can customize the model's behavior. For example, increasing the weight for the `ISSUP` score forces the model to prioritize outputs that are strongly supported by cited evidence, enhancing factuality.

---

## **Experimental Results and Analysis**

SELF-RAG was evaluated on six diverse tasks and demonstrated superior performance compared to a wide range of baselines.

### **Performance Highlights**

* **Overall Performance:** SELF-RAG (7B and 13B models) achieves the best performance among non-proprietary models on all six tasks, which include short-form QA (PopQA, TriviaQA), closed-set reasoning (PubHealth, ARC-Challenge), and long-form generation (Biography, ASQA).


* **Comparison to LLMs:** SELF-RAG outperforms ChatGPT on four tasks and surpasses Llama2-chat and Alpaca on all tasks.


* **Factuality and Citation:** On the ALCE-ASQA long-form QA task, SELF-RAG 13B achieves a citation precision of **70.3%** and recall of **71.3%**, significantly outperforming retrieval-augmented Llama2-chat (19.8% precision, 36.1% recall) and even surpassing Ret-ChatGPT in precision (65.1%). On biography generation, SELF-RAG achieves a FactScore of **81.2 (7B)** and **80.2 (13B)**, higher than Ret-Llama2-chat (79.9).


* **Ablation Studies:** The removal of key components—such as the critic during training or the use of critique tokens at inference—leads to significant performance degradation, confirming the effectiveness of the SELF-RAG framework's design.

---

## **Key Analysis Findings**

* **Inference-Time Customization:** The framework allows for a clear trade-off between different objectives. For instance, increasing the weight for the `ISSUP` token during decoding improves citation precision but can slightly lower fluency scores (MAUVE), as longer, more fluent sentences may contain claims not fully supported by a single source.

* **Retrieval Efficiency:** By adjusting the retrieval threshold, users can control the trade-off between accuracy and retrieval frequency. On PopQA, a high-recall QA task, reducing retrieval frequency significantly hurts accuracy. On PubHealth, a fact verification task, performance is more robust to lower retrieval rates.

* **Human Evaluation:** Human annotators found that SELF-RAG's outputs are highly plausible and well-supported by evidence (92.5% S&P score on PopQA). The model's own reflection tokens also showed high alignment with human assessments of relevance (`ISREL`) and factual support (`ISSUP`).
