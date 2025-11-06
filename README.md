**Paper:** *Self‑RAG: Learning to Retrieve, Generate, and Critique through Self‑Reflection* (Akari Asai et al., arXiv 2310.11511)


<img width="461" height="443" alt="Capture d’écran 2025-11-05 130453" src="https://github.com/user-attachments/assets/8f728b50-4fbd-40a4-bdd1-21589d2497c5" />



you can see the pdf here : [Self RAG.pdf](https://github.com/user-attachments/files/23398431/Self.RAG.pdf)

---


# **🌍 Part 1 — Background: Classic RAG**

## **1️⃣ What is RAG?**

**RAG (Retrieval-Augmented Generation)** is a framework that combines:

- **Retrieval models** 🔍 → fetch external information (from a database or knowledge source)
- **Generator models (LLMs)** ✍️ → produce final answers using both retrieved and internal knowledge.

💡 **Goal:**

Make LLMs more factual by grounding their answers in external documents instead of relying only on what they “remember” in their parameters (weights ).

---

## **2️⃣ How RAG Works**
![standard RAG](https://github.com/user-attachments/assets/0a4a54ea-6641-4238-ac80-d4183835d7ef)
---

## **3️⃣ RAG’s Limitations 🚫**

| Problem | Description | Example |
| --- | --- | --- |
| **Fixed Retrieval** | Always retrieves a fixed number (e.g., top-5) docs — even if not needed. | Unnecessary retrieval wastes computation 💸 |
| **Irrelevant Documents** | Retrieved docs may not match the query intent. | Retrieval noise → confusing output 😵 |
| **No Self-Evaluation** | Model can’t judge if info is relevant or factual. | Can repeat hallucinations confidently 😬 |
| **Static Pipeline** | Retrieval and generation are separate modules. | Lacks interaction and reflection 🔒 |

These challenges inspired the creation of **SELF-RAG**.

---

# **🚀 Part 2 — Enter SELF-RAG**

**SELF-RAG (Self-Reflective Retrieval-Augmented Generation)** is an advanced version of **RAG** that makes the model **think, check, and correct itself** 🤖🪞.

It combines:

- 🧠 **Reasoning (like an LLM)** — to generate fluent text.
- 🔍 **Retrieval (like RAG)** — to get real facts from external sources.
- 💭 **Self-Reflection** — to **evaluate its own answers** and improve factual accuracy.

Its a model that can:

- **Decide when** to retrieve 🔍
- **Evaluate** the **relevance** and **support** of retrieved docs 🧠
- **Critique** its own outputs ✍️
- **Adapt** generation dynamically

In short:

🧩 *It retrieves when necessary, reflects during generation, and critiques afterward.*

---

# 🧠 Part — 3 **Adaptive Retrieval**

Self-RAG makes retrieval **dynamic** — the model itself **decides when to retrieve and how much to retrieve**, based on its confidence and reasoning.

During generation, the model emits a *special internal signal* (called a **retrieval token**) like:

- `[RETRIEVE]` → *“I don’t know enough, fetch supporting docs.”*
- `[NO_RETRIEVE]` → *“I’m confident, I can answer from memory.”*

Once retrieval happens, the model integrates the new passages and continues generating the answer.

So instead of **fixed retrieval**, you get **on-demand retrieval.**

---

# **⚙️ Part 4 — Architecture Overview**

## 1️⃣ Two Models

- **Generator Model (M)** ✍️🤖 → creates text and emits *reflection tokens* during generation
- **Critic Model (C)** 👩‍🏫 → evaluates and labels data with reflection tokens during training

> Together, they learn to retrieve, generate, and critique without external supervision.
> 
<img width="8948" height="4734" alt="teaser_self_rag_v8" src="https://github.com/user-attachments/assets/50ebebe8-fd3c-4b4c-abec-259a47b77de1" />


---

## **2️⃣ Generator Model (M) — “The Writer & Self-Reflector” ✍️🤖🧠**

### **1. Role**

- Its main job is to **generate text** in response to a user query 📝💬.
- While generating, it **predicts reflection tokens** `([RETRIEVE] 🔍, [ISREL] ✅, [ISSUP] 📖, [ISUSE] ⭐`) to guide its own reasoning 🧠.
- It decides **when to retrieve information** 📚 and whether the generated output is supported by evidence 📊.
- Essentially, the Generator is the **decision maker & writer** ✍️🧠, producing answers and self-critiquing them for quality and factuality 🏆.

### **2. When it works**

- **During inference** 🔄 (i.e., when answering a user query).
    - **For every new user query**, the Generator:
        1. Decides if it needs to retrieve documents `([RETRIEVE]).`
        2. Evaluates relevance `([ISREL])` and support `([ISSUP]`) of retrieved info.
        3. Generates the answer while self-reflecting using these tokens `([ISUSE]).`
- **During training** 🏫, it learns to predict both **text** and **reflection tokens** from data annotated by the Critic model  👩‍🏫.
    - The Generator **applies the Critic’s lessons in real time** but without the ~~Critic mode~~l being involved.
<img width="1139" height="267" alt="image" src="https://github.com/user-attachments/assets/ae9dbf59-00d3-4cbe-8c44-6487c8998cf8" />

---
### **3. Step-by-Step Generator Process**

**1️⃣ Generator decides retrieval 🔍**

- The Generator evaluates its own knowledge when it starts generating.
    - Confident → skip retrieval → `[RETRIEVE=NO]` ✅
    - Uncertain → trigger retrieval → `[RETRIEVE=YES]` 🔍
    - Reuse previous evidence → `[RETRIEVE=CONTINUE]` 🔄

**2️⃣ Generator evaluates relevance of documents 📄**

- After retrieval, it reads retrieved passages.
- Judges each passage’s relevance internally and assigns reflection tokens:
    - `[ISREL=1]` → relevant ✅
    - `[ISREL=0]` → irrelevant ❌
- Focuses only on **useful evidence** for the answer.


**3️⃣ Generator produces reflection tokens alongside output 🧠**

- Generates text **and reflection tokens together**:
    - `[ISREL]` → relevance of retrieved doc
    - `[ISSUP]` → whether generated statement is supported 📖
    - `[ISUSE]` → usefulness of the sentence ⭐
- These tokens **guide reasoning step by step** and allow **self-reflection**.

**4️⃣ Generator improves final output ✨**

- Using reflection tokens, it can:
    - **Refine answers** if evidence is weak 🔄
    - **Select the best candidate** from multiple segments ✅
    - **Balance fluency vs factuality** depending on the task 🎛️

---

### **4. Example (Morocco 2022 ⚽🇲🇦)**

> User: “How did Morocco perform in the 2022 World Cup?” ⚽🇲🇦
> 

Generator’s thinking process:

```
Hmm… unsure 🤔 → [RETRIEVE] 🔍
(retrieves FIFA 2022 documents 📄📑)
Sentence: “Morocco became the first African and Arab team to reach the semifinals.”
[ISREL=1] ✅ [ISSUP=1] 📖 [ISUSE=5] ⭐
```

- The generator wrote the sentence **and** assigned reflection tokens 📝🧠.
- These tokens **indicate what the model thinks about relevance, support, and usefulness** 🔎📊.
- This annotated output helps **improve generation quality** and **control inference behavior** 🎯

---

## **3️⃣** **Critic Model (C) — “The Teacher & Annotator” 👩‍🏫📚**

### **1. Role**

- Its main job is **not to generate text** ❌📝.
- Instead, it **evaluates outputs** (generated by the Generator or from a dataset) and **labels them with reflection tokens** 🏷️.
- Essentially, it **provides “ground truth” reflection tokens** that the Generator can learn from 📖🧠.
<img width="805" height="353" alt="image" src="https://github.com/user-attachments/assets/5ca18279-ad46-44de-8f27-dcb86b040387" />


### **2. When it works - Training Phase Only 👩‍🏫**

- The Critic model is used **during the training of the Generator** 🏫📚.
- It **labels the training data** with reflection tokens:
    - Does the model need to retrieve external information? → `[RETRIEVE] 🔍`
    - Are retrieved docs relevant? → `[ISREL] ✅`
    - Is the output supported by the documents? → `[ISSUP] 📖`
    - Is the output useful to answer the query? → `[ISUSE] ⭐`
- This teaches the Generator **how to self-reflect** when generating answers.
- ✅ After this training is done, the Critic’s job is essentially complete. It’s **offline**.
  
---
### **3. Example (Morocco 2022 ⚽🇲🇦)**

Generator output:

> “Morocco became the first African and Arab team to reach the World Cup semifinals.”
> 

Critic evaluates:

```
✅ Relevant: doc discusses Morocco 2022 semifinals → [ISREL=1] ✅
✅ Supported: statement matches doc → [ISSUP=1] 📖
⭐ Useful doc → [ISUSE=5] ⭐
```

It annotates the training data like:

```
[RETRIEVE] Morocco became the first African and Arab team to reach 
the World Cup semifinals. [ISREL=1] ✅ [ISSUP=1] 📖 [ISUSE=5] ⭐
```

- Generator then learns:

> “When I write similar sentences in the future, I should mark them as supported and relevant.” 🧠💡
> 

---

## **4️⃣**How Critic and Generator Interact

### **Critic Model (C) — the Teacher 👩‍🏫📚**

- 👀 Looks at the **retrieved documents** 📄📄 and the **generator’s output** ✍️🤖.
- 🏷️ Assigns **reflection tokens** `([RETRIEVE] 🔍, [ISREL] ✅, [ISSUP] 📖, [ISUSE] ⭐)` to the training data.
- 📝 These tokens **label :  which passages are relevant ?**, **which statements are supported ?** , and **how useful the answer is ?**  📊.
- ❌ Does **not generate answers** — it only **teaches the generator where and how to reflect** 🧠.

### **Generator Model (M) — the Student ✍️🤖🧠**

- 📖 Learns from the **Critic-labeled data** where reflection tokens should appear ? .
- 🔄 During **inference**, it **generates answers** and **self-reflects** using these tokens.
- 🕵️‍♂️ Decides **when to retrieve ?** , **which docs are relevant ?** 📄, and **how well the answer is supported ?** 📊.
- ✨ Can **refine its output** using reflection tokens, essentially doing **self-critique** 🧐.

## **5️⃣Generator vs Critic – Quick Comparison Table**

| Feature | Generator (M) ✍️🤖 | Critic (C) 👩‍🏫📚 |
| --- | --- | --- |
| **Main job** | Generate text + predict reflection tokens 📝🧠 | Evaluate outputs + assign reflection tokens 🏷️📊 |
| **Active when** | Training & inference 🔄 | Only during training 🏫 |
| **User-facing** | Yes 💬 | No ❌ |
| **Purpose** | Self-reflection & adaptive retrieval 🔍🧠 | Provide ground truth labels for training 📖 |
| **Example output** | Text + `[ISREL] [ISSUP] [ISUSE]` 📝✅📖⭐ | Only `[ISREL] [ISSUP] [ISUSE]` labels 🏷️✅📖⭐ |
| **Key actions** | Decide retrieval 🕵️‍♂️, judge relevance 📄, refine output 🏆 | Label relevance ✅, support 📖, usefulness ⭐ |


###  ✅ Summary

- **Critic:** Offline teacher during training only 📚👩‍🏫
- **Generator:** Active student during training and real-time thinker during inference ✍️🤖🧠

---

# 📚 Part 5 — **Reflection Tokens in SELF-RAG**

**Reflection tokens** are **special markers** that help the model **analyze and monitor its own reasoning process** during generation.

They tell the model *how **confident, supported**, or **relevant*** its statements are.

## 1️⃣ 🧩 Main Reflection Tokens
| Token | Meaning | Purpose |
| --- | --- | --- |
| `[RETRIEVE]` | Indicates if the model should fetch external information 🔍 | Ensures answers are factually supported by relevant documents ✅ |
| `[ISREL]` | Is the **retrieved document relevant** ? 📚 | Judges if the **retrieved doc** actually helps **answer the question.** |
| `[ISSUP]` | Is the statement **supported** by evidence ***( retrieved doc)***? ✅ | Marks whether the **generated answer**  matches **retrieved docs.** |
| `[ISUSE]` | How **useful** the document is for answering ⭐ | Measures doc usefulness (e.g., 1–5 scale). |

<img width="861" height="392" alt="Capture d’écran 2025-11-06 205703" src="https://github.com/user-attachments/assets/cfd0f1c1-81fc-49aa-bba1-162adc1ee913" />

🔍 see more about  [Reflection Tokens](notes/3.%20Reflection%20tokens.md) you will find  Explanation of `[RETRIEVE]`, `[ISREL]`, `[ISSUP]`, `[ISUSE]` tokens 

---


## **2️⃣** ⚽ **Example: Morocco in the 2022 World Cup**

> User: “What did Morocco achieve in the 2022 FIFA World Cup?”
> 

**Generator thinking process:**

```
Hmm… I might need real facts → **[RETRIEVE]**
(retrieves sports news 2022)

Doc 1 = talks about Morocco reaching the semi-final **[ISREL=1]**

Doc 2 = talks about Argentina → irrelevant **[ISREL=0]**

Sentence: “ **Morocco became the first African team to reach a World Cup semi-final.** ” **[ISSUP=1]**

Doc usefulness = high **[ISUSE=5]**
```

So the model doesn’t just say:

> “Morocco reached the 2022 World Cup semi-final.”
> 

It actually generates something like:

> **[RETRIEVE]** Morocco reached the 2022 World Cup semi-final. **[ISREL=1] [ISSUP=1] [ISUSE=5]**
> 
---
### 💡 **Why It Matters**

These tokens let the model:

- Track **how well-supported** each statement is 🧠
- Learn to **self-correct** low-confidence answers ⚙️
- Make generation more **factually grounded and transparent** 🔍

---

# **🎓 Part 6 — How SELF-RAG Learns**

SELF-RAG training happens in **two main phases:**

## **1️⃣ Offline Annotation (Critic Model C)**

- Uses a powerful LLM (like GPT-4) to insert reflection tokens into training data.
- The Critic model learns to predict those tokens automatically.

## **2️⃣ Joint Training (Generator Model M)**

- The Generator learns both *what to write* and *when to reflect*.
- No need for reinforcement learning or reward models like RLHF — cheaper and more stable 💰.

---

# **⚖️ Part 7 — Controllable Decoding**

At inference, **Self-RAG** predicts reflection tokens like `[REL=YES]` and `[SUP=YES]` with associated probabilities.

These probabilities can **🎯 steer generation** toward better, evidence-based answers.

- 🎯 **Goal:** Prefer answers **supported by retrieved documents**, not just the most fluent ones.

- ⚖️ **Trade-off:** You can balance **factuality 🧩 vs fluency ✍️** depending on your needs.

## 📊 **Example:**

| 🗣️ Candidate Answer | 💬 Fluency Probability | 📚 Support Probability (`SUP=YES`) |
| --- | --- | --- |
| “Casablanca is the capital of Morocco.” | 90 % | 20 % |
| “Rabat is the capital of Morocco.” | 70 % | 95 % |
- 🤖 **Standard LLM** picks the first (more fluent).
- 🧠 **Self-RAG** picks the second (better supported by evidence).

## ⚖️ **The Trade-Off: Fluency vs Factuality**

The paper explains you can “**trade off fluency vs support**” — meaning you can **tune decoding** based on your goal:

- 🧩 **Prioritize factuality:** increase the weight of `[SUP=YES]` and `[REL=YES]`
    
    → Model generates **evidence-backed answers** but might sound slightly less natural.
    
- ✍️ **Prioritize fluency:** reduce their weight
    
    → Model sounds **smoother**, but may be **less grounded** in facts.
    

---

# **📈 Part 8 — Results & Impact**

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

### 📊 **Their findings**

- **Self-RAG (7B)** performed on par with or better than **standard RAG (13B)** on factual QA benchmarks like **Natural Questions** and **TriviaQA**.
- **Self-RAG (13B)** achieved the best overall factuality and citation support among all tested systems.

> They found that adding self-reflection (adaptive retrieval + critique tokens) improves factual reasoning more efficiently than simply increasing model size.
> 


### 🧩 **In short**

- They **used Self-RAG on both 7B and 13B models.**
- They **compared them** to non-RAG and standard RAG baselines.
- They discovered that **Self-RAG’s reasoning gains beat size gains** — meaning thoughtful retrieval > just “bigger model.”


**Insight:**

> A smaller SELF-RAG (7B) can outperform a much larger RAG (13B) because it learns *when* to retrieve and *how* to critique itself.
> 

| Benchmark | SELF-RAG 7B | Standard RAG 13B | ChatGPT | Result |
| --- | --- | --- | --- | --- |
| Factual QA | ✅ Higher | ❌ Lower | ⚖️ Close | Self-RAG wins 🏆 |
| Citation Accuracy | ✅ Better | ❌ Lower | ⚖️ Close | Better grounding |
| Efficiency | ✅ Adaptive | ❌ Always retrieves | — | Less cost |
| Hallucination | ⬇️ Reduced | ⬆️ Higher | ⚖️ Moderate | More reliable |

---

## 🧠🤖 **Part 9 — SELF-RAG: End-to-End Process**

---

### **Step 1️⃣ : User Query**

> User: “What was Morocco’s biggest achievement in the 2022 World Cup?” ⚽🇲🇦
> 

The system receives the query and the **Generator (M)** starts reasoning:

> 💭 “Do I have enough info?”
> 
> 
> 🤔 “Hmm, that’s a factual event… I need details about the 2022 World Cup.”
> 

### **Step 2️⃣ : Generator decides whether to retrieve** `[RETRIEVE]`

- The generator checks its internal knowledge:
    - If confident → `[RETRIEVE=NO]` ✅
    - If unsure → `[RETRIEVE=YES]` 🔍 *(triggers retrieval)*
        
        **In this case:**
        
        ```
        [RETRIEVE=YES] 🔍 (not stored in model memory)
        ```
        

### **Step 3️⃣: Retriever fetches documents**

- The **Adaptive Retriever** searches the knowledge base.
- It returns multiple documents related to Morocco’s 2022 World Cup.

**Retrieved docs:**

📄 **Doc A:** “Morocco became the first African and Arab team to reach the semifinals.”

📄 **Doc B:** “Morocco’s coach Walid Regragui took charge in August 2022.”

📄 **Doc C:** “Argentina won the 2022 World Cup.”

### **Step 4️⃣: Generator evaluates relevance** `[ISREL]`

- The generator reads all three documents and marks relevance:

```
Doc A → [ISREL=1] ✅ (directly relevant to Morocco’s achievement)
Doc B → [ISREL=1] ✅ (contextually relevant)
Doc C → [ISREL=0] 🚫 (irrelevant to Morocco)

```

✅ Focuses only on **Doc A** and **Doc B** for generating the answer.


### **Step 5️⃣: Generator writes outputs and predicts support** `[ISSUP]`

- The generator now creates factual sentences based on the relevant docs.

```
🏆 From Doc A → “Morocco was the first African and Arab team to reach the World Cup semifinals.”
👔 From Doc B → “Walid Regragui became Morocco’s coach in August 2022.”
```

- It checks if both statements are **supported** by the documents:

```
Doc A sentence → [ISSUP=1] ✅
Doc B sentence → [ISSUP=1] ✅
```

### **Step 6️⃣: Generator predicts usefulness** `[ISUSE]`

- The generator evaluates which answer best addresses the user’s question:

```
🏆 Doc A → [ISUSE=5] ⭐⭐⭐⭐⭐ → extremely useful (main achievement)
👔 Doc B → [ISUSE=3] ⭐⭐⭐ → moderately useful (background info)
```


### **Step 7️⃣ : Optional second retrieval**

- If the model finds missing evidence, it may **retrieve again**:

```
[RETRIEVE=CONTINUE] 🔄 → fetch new docs if needed

```

- In this case, **Doc A** and **Doc B** are enough — no second retrieval needed.

### **Step 8️⃣: Final output**

The model combines its decisions and produces the **final factual answer**:

> [RETRIEVE=YES] Morocco was the first African and Arab team to reach the World Cup semifinals. [ISREL=1] ✅ [ISSUP=1] ✅ [ISUSE=5] ⭐⭐⭐⭐⭐
> 

✅ The answer is **relevant**, **supported**, and **highly useful**.


### **Step 9️⃣: Critic model role (training phase)**

- During training, the **Critic (C)** labels retrieved docs and model outputs with reflection tokens.
- The **Generator (M)** learns to self-assess its relevance, support, and usefulness at inference time.

### **Step 🔟: Self-reflection & quality control**

- The generator can **reinspect** its reasoning path using reflection tokens:
    - Check relevance `[ISREL]`
    - Check support `[ISSUP]`
    - Check usefulness `[ISUSE]`

✅ This ensures the **final answer is grounded, clear, and trustworthy** before returning to the user.

![What was Morocco’s biggest achievement in the 2022 World Cup” 🏆🇲🇦](https://github.com/user-attachments/assets/2f780ebf-c201-4dab-b4b0-9beafe31c1ad)


---

## **💡 Key Takeaways**

> ✅ **RAG** improves factual grounding by adding retrieval — but it’s rigid.
> 

> ✅ **SELF-RAG** adds **self-awareness**: retrieves when needed, critiques its logic, and balances fluency vs factuality.
> 

> ✅ **Reflection tokens** turn reasoning into structured signals.
> 

> ✅ **Critic + Generator** training removes the need for complex reinforcement learning.
> 

> ✅ **End result:** Efficient, factual, transparent LLM reasoning.
> 

---
SELF-RAG combines **adaptive retrieval** + **self-reflection** to produce **more factual, efficient, and transparent outputs** than standard RAG or even larger LLMs.

---

### 📑 Reports
| File | Description |
| --- | --- |
| [SELF-RAG Study Guide](reports/SELF%20RAG%20Study%20Guide.md) | Quiz, essay questions, and glossary for learning SELF-RAG |
| [Self RAG Report](reports/Self%20RAG%20report%20.md) | Full briefing and technical analysis from the research paper |


---

# 📝 **Flashcards & Learning Tools**

To help you **memorize, practice, and test** the concepts, use these Quizlet links:

- 📇 **Cards:**  
  - https://quizlet.com/1104301639/flashcards/embed  
  - https://quizlet.com/1104347417/flashcards/embed  

- 📚 **Learn Mode:** [Practice & Learn](https://quizlet.com/1104347417/learn?funnelUUID=ab2047d9-3505-4ed4-af6f-9f2b84013ef3)  
- 📝 **Test Yourself:** [Take a Test](https://quizlet.com/1104347417/test?answerTermSides=6&promptTermSides=6&questionCount=41&questionTypes=4&showImages=true)  
- 🎮 **Play Games:**  
  - ⚡ [Blast](https://quizlet.com/blast/1104347417)  
  - 🏁 [Match](https://quizlet.com/1104347417/match?funnelUUID=684860c6-67ab-4aa1-b842-8fbde9549d62)  

💡 **Tip:** Review regularly, self-test, and make learning interactive & fun 🎉  

---

🔗 **References:**
- [Official Self-RAG GitHub](https://github.com/AkariAsai/self-rag?tab=readme-ov-file)  
- [Medium Summary](https://cobusgreyling.medium.com/self-reflective-retrieval-augmented-generation-self-rag-f5cbad4412d5)  
- [Notion Report](https://www.notion.so/SELF-RAG-Self-Reflective-Retrieval-Augmented-Generation-29f8343843d880a29b8ef54049665988?source=copy_link)
---

This repository is intended as a **learning and reference guide** for SELF-RAG based on the official research paper.
