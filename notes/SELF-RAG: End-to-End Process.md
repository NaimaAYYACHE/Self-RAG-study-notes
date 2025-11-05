## **SELF-RAG: End-to-End Process** 🧠🤖

### **Step 1: User Query**

> User: “How did Morocco perform in the 2022 World Cup?” ⚽🇲🇦
> 

The system receives the query and the generator (M) starts reasoning.

---

### **Step 2: Generator decides whether to retrieve** `[RETRIEVE]`

- The generator checks its parametric memory:
    - If confident: `[RETRIEVE=NO]` ✅
    - If unsure: `[RETRIEVE=YES]` 🔍 (triggers retrieval)

**In this case:**

```
[RETRIEVE=YES] 🔍 (not in training data)

```
---

### **Step 3: Retriever fetches documents**

- External retrieval system searches the knowledge base.
- Returns multiple documents relevant to 2022 Morocco World Cup.

**Retrieved docs:**

1️⃣ Doc A: “Morocco reached the semifinals after defeating Portugal.”

2️⃣ Doc B: “France won the 2018 World Cup.”

---

### **Step 4: Generator evaluates relevance** `[ISREL]`

- Reads retrieved documents and decides which are relevant:

```
Doc A → [ISREL=1] ✅ (directly relevant)
Doc B → [ISREL=0] 🚫 (irrelevant)

```

- Focuses only on relevant docs for generation.
---
### **Step 5: Generator writes output and predicts support** `[ISSUP]`

- For each generated statement, checks if it is supported by the relevant documents:

```
Sentence: “Morocco became the first African team to reach the semifinals in 2022.”
[ISSUP=1] ✅ (fully supported by Doc A)

```

---

### **Step 6: Generator predicts usefulness** `[ISUSE]`

- Evaluates if the statement is useful for answering the query:

```
[ISUSE=5] ⭐⭐⭐⭐⭐ → extremely useful, main answer to the query

```
---
### **Step 7: Optional second retrieval**

- If the generator finds evidence weak or missing, it can **retrieve again**:

```
[RETRIEVE=CONTINUE] 🔄 → reuse previous docs or fetch new ones

```

- In this case, Doc A already sufficient, no new retrieval needed.

---

### **Step 8: Final output**

- Generator combines all decisions and outputs the final answer **with annotations**:

> [RETRIEVE=YES] Morocco became the first African team to reach the semifinals in 2022. [ISREL=1] ✅ [ISSUP=1] ✅ [ISUSE=5] ⭐⭐⭐⭐⭐
> 
- This output is **factually supported, relevant, and highly useful**. ✅💡
---
### **Step 9: Critic model role (training phase)**

- During training, the Critic (C) would label retrieved documents and outputs with reflection tokens.
- Generator learns from this data to self-reflect during inference.

---

### **Step 10: Self-reflection & quality control**

- Generator can **reevaluate its own output** using reflection tokens:
    - Check relevance `[ISREL]`
    - Check support `[ISSUP]`
    - Check usefulness `[ISUSE]`
- Ensures **final answer is accurate and useful** before returning to the user. 🏆

---

## **💡 Key Takeaways**

✅ **RAG** improves factual grounding by adding retrieval — but it’s rigid.

✅ **SELF-RAG** adds **self-awareness**: retrieves when needed, critiques its logic, and balances fluency vs factuality.

✅ **Reflection tokens** turn reasoning into structured signals.

✅ **Critic + Generator** training removes the need for complex reinforcement learning.

✅ **End result:** Efficient, factual, transparent LLM reasoning.

<aside>
💡

SELF-RAG combines **adaptive retrieval** + **self-reflection** to produce **more factual, efficient, and transparent outputs** than standard RAG or even larger LLMs.

</aside>
