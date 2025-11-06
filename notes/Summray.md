# 🧠 SELF-RAG — Self-Reflective Retrieval-Augmented Generation

**Paper:** *Self‑RAG: Learning to Retrieve, Generate, and Critique through Self‑Reflection* (Akari Asai et al., arXiv 2310.11511)

---

## 🌟 What is SELF-RAG?

**SELF-RAG** is an advanced LLM framework that **retrieves information on-demand, generates answers, and self-reflects** 🪞🧠. It improves factuality, relevance, and transparency over standard RAG systems.

- 🔍 **Retrieval:** Fetches supporting documents only when needed.
- ✍️ **Generation:** Writes fluent text using both memory and retrieved info.
- 💭 **Self-Reflection:** Uses **reflection tokens** to evaluate relevance, support, and usefulness.

> **In short:** *Think, check, correct, repeat!* 🔄✅

---

## 🛠 How it Works

1. **Adaptive Retrieval**  
   - `[RETRIEVE]` → “Need docs”  
   - `[NO_RETRIEVE]` → “I know enough”  
   - `[CONTINUE]` → “Use previous evidence”  

2. **Generator Model (M) ✍️🤖**  
   - Writes answers **and predicts reflection tokens**: `[ISREL] ✅ [ISSUP] 📖 [ISUSE] ⭐`  
   - Decides **what to retrieve**, **what is relevant**, and **what is supported**  

3. **Critic Model (C) 👩‍🏫**  
   - Labels data with reflection tokens **during training only**  
   - Teaches the Generator **how to self-reflect**  

4. **Reflection Tokens 🪞**  

| Token | Purpose |
| --- | --- |
| `[RETRIEVE]` 🔍 | Trigger retrieval |
| `[ISREL]` ✅ | Relevance of doc |
| `[ISSUP]` 📖 | Supported by evidence |
| `[ISUSE]` ⭐ | Usefulness of output |

---

## ⚡ SELF-RAG Pipeline (Simplified)

1. **User Query** → “What did Morocco achieve in 2022 FIFA WC?” ⚽🇲🇦  
2. **Generator checks memory** → `[RETRIEVE=YES]` 🔍  
3. **Retriever fetches documents** 📄📑  
4. **Generator evaluates relevance** → `[ISREL]`  
5. **Generator produces answer** → `[ISSUP]` for support, `[ISUSE]` for usefulness  
6. **Optional second retrieval** `[RETRIEVE=CONTINUE]` 🔄  
7. **Final output** → Factually supported, relevant, useful ✅💡  

**Example Output:**  
[RETRIEVE=YES] Morocco reached the semifinals in 2022. [ISREL=1] ✅ [ISSUP=1] 📖 [ISUSE=5] ⭐⭐⭐⭐⭐

---

## 📊 Key Advantages

- ✅ Adaptive retrieval → efficient & cost-saving  
- ✅ Self-reflection → fewer hallucinations  
- ✅ Controllable inference → balance factuality vs fluency 🎛️  
- ✅ Outperforms larger RAG models using smarter reasoning, not just scale 🏆  

---

## 🗂 What’s in this repo

- **`notes/`** 📝  
  - Self RAG overview.md  
  - Architecture schema Self RAG.md  
  - Reflection tokens.md  
  - Controllable Decoding in Self RAG.md  
  - Summary.md (this file)  

- **`reports/`** 📄  
  - SELF RAG Study Guide.md  
  - Self RAG report.md  

> Based on the research paper by Akari Asai et al.  
> Links to key references and papers are included for deeper reading:  

🔗 **References:**
- [Official Self-RAG GitHub](https://github.com/AkariAsai/self-rag?tab=readme-ov-file)  
- [Medium Summary](https://cobusgreyling.medium.com/self-reflective-retrieval-augmented-generation-self-rag-f5cbad4412d5)  
- [Notion Report](https://www.notion.so/SELF-RAG-Self-Reflective-Retrieval-Augmented-Generation-29f8343843d880a29b8ef54049665988?source=copy_link)
- [SELF-RAG Paper](https://arxiv.org/abs/2310.11511)  
---

**💡 Takeaway:**

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

This repository is intended as a **learning and reference guide** for SELF-RAG based on the official research paper.
