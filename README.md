# Agentic RAG Proof-of-Concept (Local LLM)

## Overview
This project is a Proof-of-Concept (PoC) demonstrating a fully local, privacy-first Retrieval-Augmented Generation (RAG) system. It allows users to ask questions about local documents (PDFs, text files) without sending any sensitive data to external cloud APIs like OpenAI. 

## Tech Stack
* **Language:** Python 3.10+
* **Framework:** LangChain
* **Local LLM Runner:** Ollama (running Llama 3 or Mistral)
* **Vector Database:** ChromaDB (Local SQLite-based vector store)
* **API Layer:** FastAPI

---

## Step-by-Step Implementation Guide

### Phase 1: Environment Setup
1. **Install Ollama:** Download and install Ollama to run local AI models easily.
2. **Pull a Model:** Open your terminal and run `ollama run llama3`. This downloads the model to your machine.
3. **Set up Python:** Create a virtual environment and install the required libraries:
   `pip install langchain langchain-community chromadb sentence-transformers fastapi uvicorn pypdf`

### Phase 2: Document Ingestion & Chunking (The Data Pipeline)
*Goal: Take a PDF, read it, and break it into smaller pieces.*
1. Create a `data/` folder and put a sample PDF inside (e.g., a university syllabus or a research paper).
2. Write a Python script using `langchain_community.document_loaders.PyPDFLoader` to load the PDF.
3. Use `RecursiveCharacterTextSplitter` from LangChain to chop the text into chunks of 1000 characters. (LLMs have a context limit, so we feed them small chunks).

### Phase 3: The Vector Database (The Memory)
*Goal: Turn those text chunks into numbers (vectors) so the AI can search them.*
1. Initialize **ChromaDB** using LangChain.
2. Use a free, local embedding model like `HuggingFaceEmbeddings` (from the `sentence-transformers` library) to convert your text chunks into vectors.
3. Save these embeddings into a local ChromaDB folder. 

### Phase 4: The RAG Chain (The Brain)
*Goal: Connect the database to the local LLM.*
1. Set up the local LLM in your code using `langchain_community.llms.Ollama`.
2. Create a "Retriever" that searches your ChromaDB for chunks of text related to a user's question.
3. Use LangChain's `create_retrieval_chain` to link the Retriever and the Ollama LLM. 
   * *Logic:* User asks a question -> ChromaDB finds the relevant paragraph -> Ollama reads that paragraph and answers the question.

### Phase 5: The API Layer (The Interface)
*Goal: Make it usable as a web service.*
1. Create a `main.py` file using **FastAPI**.
2. Write a single POST endpoint `/ask` that accepts a JSON payload with a `"question"`.
3. Pass the question to your LangChain RAG chain and return the AI's answer as a JSON response.
4. Run the server using `uvicorn main:app --reload`.

---

## Future Enhancements (Agentic Capabilities)
Once the basic RAG pipeline is working, the next step is adding "Agentic" tools. This involves giving the LangChain LLM access to external Python functions (e.g., a tool that calculates math, or a tool that queries a SQL database) and letting the LLM decide when to use them.
