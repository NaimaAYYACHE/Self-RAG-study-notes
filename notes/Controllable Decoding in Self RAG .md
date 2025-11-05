# **⚖️ SELF-RAG — Self-Reflective Retrieval-Augmented Generation — Controllable Decoding**

At inference, **Self-RAG** predicts reflection tokens like `[REL=YES]` and `[SUP=YES]` with associated probabilities.

These probabilities can **🎯 steer generation** toward better, evidence-based answers.

- 🎯 **Goal:** Prefer answers **supported by retrieved documents**, not just the most fluent ones.
- ⚖️ **Trade-off:** You can balance **factuality 🧩 vs fluency ✍️** depending on your needs.

---

## 📊 **Example:**

| 🗣️ Candidate Answer | 💬 Fluency Probability | 📚 Support Probability (`SUP=YES`) |
| --- | --- | --- |
| “Casablanca is the capital of Morocco.” | 90 % | 20 % |
| “Rabat is the capital of Morocco.” | 70 % | 95 % |

- 🤖 **Standard LLM** picks the first (more fluent).
- 🧠 **Self-RAG** picks the second (better supported by evidence).

---

## ⚖️ **The Trade-Off: Fluency vs Factuality**

The paper explains you can “**trade off fluency vs support**” — meaning you can **tune decoding** based on your goal:

- 🧩 **Prioritize factuality:** increase the weight of `[SUP=YES]` and `[REL=YES]`
    
    → Model generates **evidence-backed answers** but might sound slightly less natural.
    
- ✍️ **Prioritize fluency:** reduce their weight
    
    → Model sounds **smoother**, but may be **less grounded** in facts.
    

---

# **📈  Results & Impact**

They **trained and evaluated Self-RAG on both 7B and 13B models** 

However, they also **compared** those Self-RAG models to **non-RAG** and **standard RAG** baselines of the same or larger sizes.

So, in the experiments they had several setups like:

| Model Type | Size | Description |
| --- | --- | --- |
| Base LLM | 7B / 13B | Plain model without retrieval |
| Standard RAG | 7B / 13B | Model that always retrieves top-K documents |
| **Self-RAG** | 7B / 13B | Model that decides *when* to retrieve and *self-reflects* |

### ⚙️ **Their goal**

They wanted to test **two hypotheses**:

1. **Self-RAG vs. standard RAG (same size):**
    
    → Does self-reflection improve factuality and efficiency compared to traditional RAG?
    
2. **Self-RAG vs. larger models:**
    
    → Can a **Self-RAG 7B** outperform or match a **13B non-RAG or standard RAG** model?
    
    (i.e., can intelligence from *architecture* beat brute-force *scale*?)
    

---

### 📊 **Their findings**

- **Self-RAG (7B)** performed on par with or better than **standard RAG (13B)** on factual QA benchmarks like **Natural Questions** and **TriviaQA**.
- **Self-RAG (13B)** achieved the best overall factuality and citation support among all tested systems.

> They found that adding self-reflection (adaptive retrieval + critique tokens) improves factual reasoning more efficiently than simply increasing model size.
> 

---

### 🧩 **In short**

✅ They **used Self-RAG on both 7B and 13B models.**

✅ They **compared them** to non-RAG and standard RAG baselines.

✅ They discovered that **Self-RAG’s reasoning gains beat size gains** — meaning thoughtful retrieval > just “bigger model.”

**Insight:**

A smaller SELF-RAG (7B) can outperform a much larger RAG (13B) because it learns *when* to retrieve and *how* to critique itself.

| Benchmark | SELF-RAG 7B | Standard RAG 13B | ChatGPT | Result |
| --- | --- | --- | --- | --- |
| Factual QA | ✅ Higher | ❌ Lower | ⚖️ Close | Self-RAG wins 🏆 |
| Citation Accuracy | ✅ Better | ❌ Lower | ⚖️ Close | Better grounding |
| Efficiency | ✅ Adaptive | ❌ Always retrieves | — | Less cost |
| Hallucination | ⬇️ Reduced | ⬆️ Higher | ⚖️ Moderate | More reliable |
