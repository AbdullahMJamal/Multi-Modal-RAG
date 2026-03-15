<div align="center">

Multi-Modal RAG Document Intelligence

Advanced Retrieval-Augmented Generation pipeline for processing complex PDFs containing text, HTML tables, and Base64 images as first-class citizens.

</div>

📖 Overview

Standard RAG pipelines fail when encountering complex document structures, often garbling tables and completely ignoring images.

This project solves that by leveraging Unstructured.io for high-resolution PDF partitioning. It intelligently extracts text, preserves tables as structured HTML, and captures images in Base64. By employing GPT-4o for multimodal summarization and ChromaDB for vector retrieval, this pipeline enables complex reasoning across visual and tabular data structures.

✨ Key Features

High-Fidelity Document Parsing: Uses Unstructured (backed by Poppler and Tesseract OCR) to "see" document layouts, preserving structural hierarchy.

First-Class Tables & Images: Extracts tables into structured HTML and images into Base64 formats so zero visual context is lost.

AI-Enhanced Chunking & Summarization: GPT-4o generates highly searchable semantic metadata for charts, diagrams, and tables before vector embedding.

Vision-Language Synthesis: Passes the retrieved raw text, HTML tables, and Base64 images back to GPT-4o for a final, highly accurate multimodal answer generation.

🏗️ System Architecture

Ingestion: Partition PDFs using the hi_res strategy to extract elements.

Chunking: Intelligent chunking by document title and visual structure.

Summarization: AI analyzes mixed content (text + images + tables) to generate dense semantic descriptions for the vector database.

Vectorization: Embed summaries using text-embedding-3-small and store them locally in ChromaDB.

Generation: Retrieve top-$k$ chunks and feed the original multimodal context directly to GPT-4o to answer user queries.

🛠️ Installation & Setup

1. System Prerequisites

Unstructured requires core system libraries to process PDFs and run OCR.

Linux (Ubuntu/Debian):

sudo apt-get update
sudo apt-get install poppler-utils tesseract-ocr libmagic-dev


macOS (Homebrew):

brew install poppler tesseract libmagic


2. Python Environment

Create a virtual environment and install the required Python packages:

# Create and activate virtual environment
python3 -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate

# Install core dependencies
pip install -U "unstructured[all-docs]"
pip install -U langchain_chroma langchain langchain-community langchain-openai python-dotenv


3. Environment Variables

Create a .env file in the root directory and add your API keys:

OPENAI_API_KEY=sk-your_openai_api_key_here


💻 Quick Start Usage

1. Ingesting a Document

Point the pipeline at your target PDF to partition, summarize, and vectorize. This creates your local ChromaDB instance.

from your_script_name import run_complete_ingestion_pipeline

# Run the complete pipeline to build the vector store
db = run_complete_ingestion_pipeline("./docs/attention-is-all-you-need.pdf")


2. Querying the Multi-Modal Engine

Ask complex questions that require interpreting both text and visual/tabular data.

from your_script_name import generate_final_answer

query = "How many attention heads does the Transformer use, and what is the dimension of each head?"

# Retrieve top 3 multimodal chunks
retriever = db.as_retriever(search_kwargs={"k": 3})
chunks = retriever.invoke(query)

# Generate final answer using GPT-4o Vision
final_answer = generate_final_answer(chunks, query)
print(final_answer)


📂 Output Artifacts & Debugging

The pipeline includes utility functions (export_chunks_to_json) to export processed chunks to clean JSON formats (chunks_export.json and rag_results.json). This is highly recommended for debugging and verifying the exact HTML tables and Base64 image strings being sent to the LLM.

