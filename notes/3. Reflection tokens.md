# SELF-RAG — Self-Reflective Retrieval-Augmented Generation — **Reflection Tokens in SELF-RAG**

**Reflection tokens** are **special markers** that help the model **analyze and monitor its own reasoning process** during generation.

They tell the model *how **confident, supported**, or **relevant*** its statements are.


## 🧩 **Main Reflection Tokens**

| Token | Meaning | Purpose |
| --- | --- | --- |
| `[RETRIEVE]` | Indicates if the model should fetch external information 🔍 | Ensures answers are factually supported by relevant documents ✅ |
| `[ISREL]` | Is the **retrieved document relevant** ? 📚 | Judges if the **retrieved doc** actually helps **answer the question.** |
| `[ISSUP]` | Is the statement **supported** by evidence ***( retrieved doc)***? ✅ | Marks whether the **generated answer**  matches **retrieved docs.** |
| `[ISUSE]` | How **useful** the document is for answering ⭐ | Measures doc usefulness (e.g., 1–5 scale). |

## 🔍 **[RETRIEVE] — “Should I look something up?”**

### 🧠 **Meaning**

The `[RETRIEVE]` token tells the model **when to search for external information** (from a knowledge base, documents, or database).

It’s like the model saying to itself:

> “Hmm… I’m not sure enough — I should check the facts before answering.”
> 

---

### ⚙️ **When the model uses [RETRIEVE]**

- When the **question is factual**, and the model **isn’t confident in its memory** ❓
- When it needs **up-to-date or detailed info** that might **not** be in **its internal knowledge** 🗂️
- It can also **skip retrieval** if it’s **confident** → `[RETRIEVE=NO]` 🚫

---

### ⚽ **Example 1: Morocco in the World Cup**

### 🍲 **Example 2: How to make couscous**

> User: “How did Morocco perform in the 2022 FIFA World Cup?” 🇲🇦⚽
> 

**Model thinking:**

> “Hmm, that’s a factual event… I might not remember the details.” 🤔
> 

So it triggers retrieval:

```
**[RETRIEVE]**
(retrieves FIFA articles 📄)
Finds: “Morocco reached the semifinals.”
Generates: “Morocco became the first African team to reach the World Cup semifinals.”

```

✅ **Result:** Retrieval was necessary because the model needed ***real-world data*.**

> User: “How do Moroccans traditionally prepare couscous?” 🍲🇲🇦
> 

**Model thinking:**

> “I already know the general recipe — I don’t need to look it up.” 😎
> 

So it skips retrieval:

```
**[RETRIEVE=NO]**
Generates: “Moroccan couscous is steamed semolina served with vegetables and meat, usually on Fridays.”

```

✅ **Result:** No retrieval needed — **the answer relies on stored cultural knowledge.**

---

### 💡 **Summary**

| Mode | Token | Description | Example |
| --- | --- | --- | --- |
| Retrieve | `[RETRIEVE]` | Model decides to look for info 🔍 | Morocco in World Cup ⚽ |
| Skip | `[RETRIEVE=NO]` | Model confident, no search 🚫 | Moroccan couscous 🍲 |

### 🧩 **In short**

**[RETRIEVE]** = the model’s *curiosity switch* 🪄

It tells the system:

> “Do I know this, or should I check before I speak?” 🧠🔍
> 

---

## ✅ **[ISREL] — “Is this document relevant?”**

### 🧠 **Meaning**

The `[ISREL]` token helps the model **judge how relevant** a retrieved document or passage is to the user’s question.

It’s like the model saying:

> “I found some info… but is it actually about what the user asked?” 🤔
> 

---

### ⚙️ **When the model uses [ISREL]**

- After **retrieving** several documents 📚
- It checks each one and decides:
    - `[ISREL=1]` ✅ → The doc is **relevant** to the question
    - `[ISREL=0]` 🚫 → The doc is **irrelevant** or off-topic

This helps the model **focus only on useful evidence** for generating the final answer 💡

---

### ⚽ **Example 1: Morocco in the 2022 World Cup** 🇲🇦⚽

> User: “How did Morocco perform in the 2022 World Cup?”
> 

**Model retrieves two documents:**

1️⃣ Doc A: “Morocco reached the semifinals after defeating Portugal.”

2️⃣ Doc B: “France won the 2018 World Cup.”

**Model evaluation:**

```
Doc A → [ISREL=1] ✅ (directly about Morocco 2022)
Doc B → [ISREL=0] 🚫 (irrelevant year/event)
```

✅ It then focuses on **Doc A** to generate:

> “Morocco became the first African team to reach the semifinals in 2022.” ⚽🌍
> 

### 🏜️ **Example 2: Morocco’s desert climate** ☀️🏜️

> User: “What kind of climate does Morocco have?”
> 

**Model retrieves:**

1️⃣ Doc A: “Morocco has a Mediterranean climate in the north and desert climate in the south.”

2️⃣ Doc B: “Morocco’s football team made history in 2022.”

**Model evaluation:**

```
Doc A → [ISREL=1] ✅
Doc B → [ISREL=0] 🚫
```

✅ Only **Doc A** is used to generate:

> “Morocco has a mix of Mediterranean and desert climates.” 🌦️🌵
> 

---

### 💡 **Summary**

| Token | Meaning | Example | Decision |
| --- | --- | --- | --- |
| `[ISREL=1]` | Doc is relevant to the query ✅ | Morocco in 2022 World Cup | Keep it |
| `[ISREL=0]` | Doc is irrelevant 🚫 | France 2018 World Cup | Ignore it |

### 🧩 **In short**

**[ISREL]** = the model’s *focus filter* 🎯

It helps the generator **ignore noise** and **keep only the right context**:

> “Is this really about what the user asked?” 🧐✅
> 

---

## 📖 **[ISSUP] — “Is this statement supported by evidence?”**

### 🧠 **Meaning**

The `[ISSUP]` token helps the model decide **whether what it’s saying is truly backed by the retrieved documents** 🧾🔍.

It’s like the model asking itself:

> “Can I prove what I just said with evidence ? (retrieved docs)” 🤔📚
> 

---

### ⚙️ **When the model uses [ISSUP]**

- After **retrieving** and **checking relevance** (`[ISREL]`) ✅
- The model reads the selected docs and compares them to its own generated statements 🧩
- Then it assigns:
    - `[ISSUP=1]` ✅ → statement is **supported** by evidence
    - `[ISSUP=0]` 🚫 → statement is **unsupported** or **contradicted**

This token teaches the model to **speak only when it has proof** 📊

---

### ⚽ **Example 1: Morocco in the 2022 World Cup** 🇲🇦⚽

> User: “Did Morocco reach the semifinals in the 2022 World Cup?” 🏆
> 

**Model reasoning:**

```
I’m not 100% sure 🤔 → [RETRIEVE=YES] 🔍
(retrieves FIFA 2022 documents 📑)
```

**Model retrieves:**

- Doc A: “Morocco reached the semifinals after defeating Portugal.”
- Doc B: “Croatia won the third-place match against Morocco.”

**Model evaluation:**

```
Doc A → [ISREL=1] ✅
Doc B → [ISREL=1] ✅

```

**Generated statement:**

> “Morocco reached the semifinals in the 2022 World Cup.” ⚽🇲🇦
> 

**Support check:**

```
Statement matches Doc A → [ISSUP=1] ✅
```

✅ So the model marks it as supported and confident:

> “[RETRIEVE] Morocco reached the semifinals in the 2022 World Cup. [ISSUP=1] ✅”
> 

---

### 🏔️ **Example 2: Morocco’s mountains** ⛰️🇲🇦

> User: “Is the tallest mountain in Morocco Mount Everest?” 😅
> 

**Model reasoning:**

```
That sounds suspicious... 🤨 → [RETRIEVE=YES] 🔍
(retrieves geography docs 📄)
```

**Model retrieves:**

- Doc A: “Toubkal is the highest peak in Morocco and North Africa.”
- Doc B: “Mount Everest is located in Nepal.”

**Model evaluation:**

```
Doc A → [ISREL=1] ✅
Doc B → [ISREL=1] ✅
```

**Generated statement:**

> “The tallest mountain in Morocco is Mount Everest.” 🏔️
> 

**Support check:**

```
No document supports this ❌ → [ISSUP=0] 🚫
```

🚫 The model realizes the claim is **not supported**, so it **revises** the answer:

> “The tallest mountain in Morocco is Mount Toubkal.” ⛰️🇲🇦 → [ISSUP=1] ✅
> 

---

### 💡 **Summary**

| Token | Meaning | Example | Decision |
| --- | --- | --- | --- |
| `[ISSUP=1]` | Statement is supported by evidence ✅ | Morocco reached semifinals | Keep it |
| `[ISSUP=0]` | Statement not supported 🚫 | Everest in Morocco | Correct it |

---

### 🧩 **In short**

**[ISSUP]** = the model’s *truth detector* 🕵️‍♂️📖

It ensures every generated fact has **evidence** behind it.

> “Am I saying something that’s actually in the documents?” 🧐✅
> 

---

Here’s a full and visual explanation of the **[ISUSE]** token — with **2 detailed examples** and **emojis** 👇

---

## ⭐ **[ISUSE] — “How useful is this document or sentence for answering the question?”**

### 🧠 **Meaning**

The `[ISUSE]` token measures **how valuable** a piece of information (a document, passage, or sentence) is for producing the *best possible answer* 🏆.

It’s like the model asking itself:

> “Okay, this info is relevant… but is it actually helpful for answering the user?” 🤔💭
> 

---

### ⚙️ **When the model uses [ISUSE]**

- After checking **relevance** (`[ISREL]`) and **support** (`[ISSUP]`) ✅📖
- It assigns a **usefulness score** (often from 1 to 5 ⭐) to show how *strongly* a piece of text contributes to the final answer:

| Token |  |
| --- | --- |
| `[ISUSE=5]` ⭐⭐⭐⭐⭐ → Extremely useful |  |
| `[ISUSE=3]` ⭐⭐⭐ → Moderately useful |  |
| `[ISUSE=1]` ⭐ → Barely useful |  |

This helps the model **prioritize** the most informative parts when generating its response 💬⚙️

---

### ⚽ **Example 1: Morocco in the 2022 World Cup** 🇲🇦⚽

> User: “What was Morocco’s biggest achievement in the 2022 World Cup?” 🏆🇲🇦
> 

**Model reasoning:**

```
I’ll need details about Morocco’s results → [RETRIEVE=YES] 🔍
```

**Model retrieves:**

1️⃣ Doc A: “Morocco became the first African and Arab team to reach the semifinals.”

2️⃣ Doc B: “Morocco’s coach Walid Regragui took charge in August 2022.”

**Model evaluation:**

```
Doc A → [ISREL=1] ✅ [ISSUP=1] ✅ [ISUSE=5] ⭐⭐⭐⭐⭐
Doc B → [ISREL=1] ✅ [ISSUP=1] ✅ [ISUSE=2] ⭐⭐ (less useful for the question)

```

✅ **Final generation:**

> “[RETRIEVE] Morocco became the first African and Arab team to reach the semifinals — its greatest achievement in 2022. [ISUSE=5] ⭐⭐⭐⭐⭐”
> 

💡 The model *kept* Doc A because it was **the most useful** for the specific question.

---

### 🏜️ **Example 2: Morocco’s desert climate** ☀️🏜️

> User: “Describe Morocco’s main climate zones.” 🌦️🌵
> 

**Model reasoning:**

```
Let’s fetch info about Moroccan geography → [RETRIEVE=YES] 🔍
```

**Model retrieves:**

1️⃣ Doc A: “Morocco has a Mediterranean climate in the north and a desert climate in the south.”

2️⃣ Doc B: “Morocco’s national dish is couscous.” 🍲

**Model evaluation:**

```
Doc A → [ISREL=1] ✅ [ISSUP=1] ✅ [ISUSE=5] ⭐⭐⭐⭐⭐ (directly useful)
Doc B → [ISREL=0] 🚫 [ISSUP=0] 🚫 [ISUSE=1] ⭐ (off-topic)

```

✅ **Final generation:**

> “[RETRIEVE] Morocco has both Mediterranean and desert climates, creating diverse weather conditions. [ISUSE=5] ⭐⭐⭐⭐⭐”
> 

🚫 The model **ignored Doc B** because it was **irrelevant and not useful** for describing climate.

---

### 💡 **Summary**

| Token | Meaning | Example | Action |
| --- | --- | --- | --- |
| `[ISUSE=5]` ⭐⭐⭐⭐⭐ | Extremely useful info | Morocco reached semifinals | Keep |
| `[ISUSE=2]` ⭐⭐ | Minor detail | Coach name | Optional |
| `[ISUSE=1]` ⭐ | Off-topic | Moroccan food | Ignore |

---
<img width="8948" height="4734" alt="teaser_self_rag_v8" src="https://github.com/user-attachments/assets/eeb94b85-d6d7-4dec-b9bc-15e952c9ea54" />

### 🧩 **In short**

**[ISUSE]** = the model’s *priority meter* 🎯⭐

It helps the model choose **the most helpful evidence** when forming answers — filtering what’s **essential** vs. **extra**.

> “This info is true… but is it actually useful for the user’s question?” 🧠💬⭐
>
