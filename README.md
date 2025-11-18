This project implements a Mini Retrieval-Augmented Generation (RAG) Assistant capable of answering questions based on a custom document corpus (PDFs, articles, or internal docs).
Instead of relying only on the LLM’s internal knowledge, the assistant retrieves information from a vector database and combines it with the model’s reasoning. This ensures accurate, grounded, and context-aware answers.
The project demonstrates understanding of:                  

GenAI concepts
Embedding and vector databases
Cloud deployment
Node.js integration with LLMs
RAG architecture


 Architecture
                 ┌───────────────────────────┐
                 │        User Query          │
                 └──────────────┬────────────┘
                                ▼
                     (1) Convert Query to Embedding
                                ▼
                 ┌───────────────────────────┐
                 │   Vector Store (FAISS)    │
                 │  Similarity Search (Top-K)│
                 └──────────────┬────────────┘
                                ▼
                     (2) Retrieve Relevant Chunks
                                ▼
                 ┌───────────────────────────┐
                 │    LLM (RAG Generation)   │
                 │  Query + Context → Answer │
                 └──────────────┬────────────┘
                                ▼
                           Final Output

1️) Corpus Preparation
.. What We Do

Choose 3–5 documents (PDFs, articles, internal text).

Extract text from them.

Preprocess and chunk them into smaller segments for embedding.

.. Why This Step Matters

LLMs cannot embed entire large documents.
Chunking ensures:

Each embedding represents one coherent idea

Better accuracy in similarity search

Faster responses

Reduced hallucination

..How We Did It
1. Text Extraction

Used tools like:

pdfplumber for PDFs

Plain .txt files for articles

2. Preprocessing

Remove headers, footers, extra spaces

Remove special characters

Normalize different formatting patterns

3. Chunking

Chunk size: 300–500 tokens
Overlap: 40 tokens


Reason for overlap:
Preserves context between chunks and improves retrieval quality.

2️⃣ Embedding & Vector Store
.. What We Do

Convert each chunk into a numeric vector using an embedding model.

Store embeddings inside a vector database (FAISS).

.. Why This Step Matters

Embeddings enable semantic similarity — meaning the system can understand what the user is asking even if exact keywords are not present.

FAISS was chosen because:

Free & open-source

Very fast for similarity search

Lightweight and ideal for hackathons

.. How We Did It
Embedding Model

We used one of the following (depending on plan):

HuggingFace: sentence-transformers/all-MiniLM-L6-v2

OR OpenAI embeddings

Vector Database

We built a FAISS Index:

Index type: IndexFlatL2

Stores vectors + metadata

Steps:

Embed each chunk

Insert embedding into FAISS

Save FAISS index + metadata locally

3️⃣ Query Handling
.. What We Do

Accept user input from a Node.js frontend/CLI

Convert the query into embeddings

Perform similarity search to get the most relevant chunks

Provide those chunks + question to the LLM for final answer generation

.. Why This Step Matters

If we give LLMs vague queries without context → hallucination.
If we give LLMs the exact relevant chunk → accurate answer.

This is the heart of the RAG system.

.. How It Works (Step-by-Step)

User enters:
“What is the refund policy?”

Convert this text → embedding

Use FAISS to get Top 3 relevant chunks

Build a RAG Prompt:

You are an assistant. Use ONLY the context below to answer.

Context:
<chunk1>
<chunk2>
<chunk3>

Question: What is the refund policy?
Answer:


LLM generates a grounded answer.

4️⃣ Cloud Deployment
.. What We Do

Deploy the backend & RAG logic to a cloud platform.

Preferred: Azure
.. Why Deployment Matters

Shows real-world integration

Demonstrates ability to run LLM-powered apps in production

Enables team collaboration

.. How We Deployed It

Code pushed to GitHub

Azure Web App connected to GitHub repository

Auto-build + environment variable setup

Backend + FAISS index served via REST API

Simple Node.js frontend deployed to the cloud

🧪 Tech Stack
Python (Backend & Processing)

pdfplumber

FAISS

sentence-transformers

numpy

Node.js (Query API + UI)

Express.js

Axios

dotenv

Cloud

Azure App Service

