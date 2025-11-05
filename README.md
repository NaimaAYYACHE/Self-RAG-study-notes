# 🤖 SELF-RAG Study Notes

## 🌟 What is SELF-RAG?

**SELF-RAG (Self-Reflective Retrieval-Augmented Generation)** is an advanced framework for Large Language Models (LLMs) that:  

- **Generates text** ✍️  
- **Retrieves relevant facts** 🔍  
- **Reflects on its own output** 💭  

- **Evaluates quality and usefulness** ✅  

It combines **reasoning** 🧠, **retrieval** 📚, and **self-reflection** 🪞 to produce **high-quality, evidence-backed answers**.  

**How it works**:  
1. The model decides **when to retrieve information** 📌  
2. It retrieves and evaluates **relevance** [ISREL] ✅, **support** [ISSUP] 📖, and **usefulness** [ISUSE] ⭐  
3. The model **self-critiques** its output ✨ and selects the best answer  
![agentic_crag](https://github.com/user-attachments/assets/72e1f7a8-6fe7-4129-8742-ee40bbbd1ef9)
---


## 📚 Repository Structure

This repository contains notes, reports, and examples based on the **SELF-RAG research paper**.  
<img width="461" height="443" alt="Capture d’écran 2025-11-05 130453" src="https://github.com/user-attachments/assets/91b6ce00-059a-4542-8451-3016dc1a75e8" />
### 📝 Notes
| File | Description |
| --- | --- |
| [Self RAG Overview](notes/1.%20Self%20RAG%20overview.md) | Overview of SELF-RAG framework and concepts |
| [Architecture Schema Self RAG](notes/2.%20Architecture%20schema%20Self%20RAG%20.md) | Visual schema of SELF-RAG architecture |
| [Reflection Tokens](notes/3.%20Reflection%20tokens.md) | Explanation of `[RETRIEVE]`, `[ISREL]`, `[ISSUP]`, `[ISUSE]` tokens |
| [Controllable Decoding in Self RAG](notes/4.%20Controllable%20Decoding%20in%20Self%20RAG%20.md) | How SELF-RAG controls output with reflection tokens |
| [SELF-RAG: End-to-End Process](notes/5.%20SELF-RAG:%20End-to-End%20Process.md) | Step-by-step workflow from user query to final answer |

### 📑 Reports
| File | Description |
| --- | --- |
| [SELF-RAG Study Guide](reports/SELF%20RAG%20Study%20Guide.md) | Quiz, essay questions, and glossary for learning SELF-RAG |
| [Self RAG Report](reports/Self%20RAG%20report%20.md) | Full briefing and technical analysis from the research paper |

---

## 🖼️ Visual Aids

- **Architecture Schema:**  
<img width="8948" height="4734" alt="teaser_self_rag_v8" src="https://github.com/user-attachments/assets/3fa7f7d2-9041-417a-a4e0-8c844603b7c6" />

- **Mind Map of SELF-RAG Concepts:**  

<img width="6706" height="10736" alt="NotebookLM Mind Map (2)" src="https://github.com/user-attachments/assets/8ea72d17-8faf-4a11-8fa4-c66ee1a6d27c" />


---

This repository is intended as a **learning and reference guide** for SELF-RAG based on the official research paper.
