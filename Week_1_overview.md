# Week 1 Deliverable: Technical Selection & Execution Roadmap

## 1. Technical Selection:

To meet the requirement of "high accuracy and low hallucination" for financial summarization, we have selected the following stack. This architecture avoids paid API tokens (like OpenAI) by utilizing "Free Tier" enterprise access and local inference.

### A. Large Language Model (LLM) Selection

**Selected Model:** **Google Gemini 1.5 Flash** (via Google AI Studio)

* **Justification:**
* **Cost:** Free of charge (within rate limits suitable for development/MVP).
* **Context Window:** 1 Million tokens. This is critical for reading long payroll history logs or tax documents without truncation, a key advantage over Llama 3 or GPT-3.5.
* **Performance:** High capability in tabular data reasoning, essential for the "Narrative Intelligence" workflow.




* **Fallback/Privacy Option:** **Llama 3 (8B)** via **Groq** or **Ollama** (Local).
* **Use case:** If data privacy regulations (GDPR/DPDP) strictly forbid cloud processing, we switch to Llama 3 running locally using Ollama.



### B. Chatbot Framework (RAG Architecture)

* **Selected Framework:** **LlamaIndex** (formerly GPT Index)

* **Justification:** While LangChain is popular, **LlamaIndex** is specifically optimized for **indexing structured data** (like SQL databases and CSVs), which constitutes the bulk of Zenvy’s payroll data. It handles the "retrieval" of specific salary slips better than general text retrievers.



### C. Vector Database (Memory)

**Selected Database:** **ChromaDB**

* **Justification:** Fully open-source, runs locally on the server (no cloud costs), and integrates natively with LlamaIndex. It will store policy documents and context for the chatbot.



---

## 2. Domain Alignment & Competitor Analysis

Per the requirement to review standard features of competitors like Zoho and Keka, we have performed a gap analysis to position Zenvy's AI effectively.

| Feature Category | **Zoho Payroll / Keka** (Standard) | **Zenvy Gen AI** (Proposed Innovation) |
| --- | --- | --- |
| **Payslips** | Static PDF generation. | **Dynamic Explanation:** "Why is my tax higher this month?" (Narrative Layer). |
| **Reporting** | Visual Dashboards (Bar charts/Pie charts). | <br>**Narrative Synthesis:** "Expenses rose 12% due to year-end bonuses in Engineering."|
| **Query Handling** | FAQ links or Support Ticket creation. | <br>**Conversational Resolution:** Instant answers via Chatbot using RAG.|

**Actionable Insight for Python Team:**
We must request the "Payroll Basics" data  in **JSON format**, not just CSV. The LLM processes hierarchical JSON (nested deductions) more accurately than flat CSV rows when generating narratives.

---

## 3. Environment Setup (Technical Execution)

This section covers the "Environment Setup (API Keys, SDK installation)" deliverable.

### Step 1: Virtual Environment Configuration

We will use a dedicated Python environment to prevent dependency conflicts with the Fullstack team's backend.

```bash
# Create project directory
mkdir zenvy_genai_module
cd zenvy_genai_module

# Initialize virtual environment
python -m venv venv

# Activate environment
# Windows:
venv\Scripts\activate
# Mac/Linux:
source venv/bin/activate

```

### Step 2: Dependency Installation

We will install the core libraries for Gemini (Google), LlamaIndex (Orchestration), and ChromaDB (Memory).

```bash
# Core AI & Utility Libraries
pip install google-generativeai llama-index llama-index-llms-gemini llama-index-embeddings-huggingface chromadb python-dotenv pandas

```

* **`google-generativeai`**: The SDK for accessing Gemini 1.5 Flash.
* **`llama-index-embeddings-huggingface`**: We will use local HuggingFace embeddings (e.g., `all-MiniLM-L6-v2`) to save costs on embedding APIs.

### Step 3: API Key Configuration

Since we cannot hardcode keys, we will set up a `.env` file.

1. **Generate Key:** Go to [Google AI Studio](https://aistudio.google.com/) -> "Get API Key" -> Create Key in New Project.
2. **Create `.env` file:**

```ini
# .env file content
GOOGLE_API_KEY="AIzaSyYourGeneratedKeyHere"
# Set environment to development to enable debug logs
ENV="DEVELOPMENT"

```

### Step 4: Verification Script (Sanity Check)

Run this Python script to verify the connection to the Free Tier LLM.

```python
import os
import google.generativeai as genai
from dotenv import load_dotenv

# 1. Load Environment Variables
load_dotenv()
api_key = os.getenv("GOOGLE_API_KEY")

# 2. Configure Gemini
genai.configure(api_key=api_key)

# 3. Test Generation (Zero Cost Check)
try:
    model = genai.GenerativeModel('gemini-1.5-flash')
    response = model.generate_content("Explain the concept of 'Net Salary' vs 'Gross Salary' in one sentence.")
    print(f" SUCCESS: API Connected. \nResponse: {response.text}")
except Exception as e:
    print(f" ERROR: Connection failed. Details: {e}")

```

---

## 4. Next Steps (Transition to Week 2)

With the technology selected and the environment active, we are ready to move to **Phase 2: Architecture Design**.
