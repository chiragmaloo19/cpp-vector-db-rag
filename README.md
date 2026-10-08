# C++ Vector Database & Local RAG System

## Overview
A fully working Vector Database built from scratch in C++ with a web UI. It implements HNSW, KD-Tree, and Brute Force search algorithms, plus a RAG pipeline powered by a local LLM via Ollama. It was built as an educational project to demonstrate how production vector databases like Pinecone and Chroma work internally.

## Features
- **3 Search Algorithms**: HNSW (production-grade), KD-Tree, Brute Force.
- **3 Distance Metrics**: Cosine similarity, Euclidean distance, Manhattan distance.
- **Live PCA Scatter Plot**: 2D visualization of semantic space clustering.
- **Local Embedding & RAG**: End-to-end RAG pipeline using local LLMs.
- **REST API**: Built-in C++ HTTP server for vector operations.

## Architecture
```text
User
↓
Frontend (index.html)
↓
REST API (cpp-httplib)
↓
C++ Backend (main.cpp)
↓
Vector DB & Document DB
↓
Search (HNSW / KD-Tree / Brute Force)
```

## How It Works

### Vector Search
Vectors are represented as multi-dimensional floats. Searching compares a query vector to stored vectors using distances like Cosine Similarity, retrieving the closest items.

### HNSW
Hierarchical Navigable Small World graph. It builds a multi-layered graph where top layers have long-range links and bottom layers are dense, enabling fast approximate O(log N) search.

### KD-Tree
A binary space partitioning tree. It splits space along alternating dimensions to speed up searches. It struggles with high dimensions but excels in low ones.

### Brute Force
Computes the distance between the query and *every* vector. O(N) complexity. It is perfect for exact matches and serving as a baseline.

### RAG Pipeline
```text
User Question → Embed (Ollama) → HNSW Search → Context Construction → Llama 3.2 → Answer
```

## Technology Stack
- **Backend**: C++17
- **HTTP Server**: cpp-httplib
- **Frontend**: Vanilla HTML/JS/CSS
- **Local AI**: Ollama

## Technologies Used
- `nomic-embed-text`: Generates 768D embeddings from text.
- `llama3.2`: Generates answers using retrieved context.
- `cpp-httplib`: Lightweight C++ HTTP library.

## Local Setup

1. Install **MSYS2** (for Windows `g++`) and **Git**.
2. Install **Ollama**.
3. Pull the required models:
```powershell
ollama pull nomic-embed-text
ollama pull llama3.2
```
4. Compile the server:
```powershell
g++ -std=c++17 -O2 main.cpp -o db -lws2_32
```
5. Run the server:
```powershell
./db
```
6. Open your browser to `http://localhost:8080`.

## Example
1. Navigate to the **Documents** tab.
2. Insert a document titled "Operating Systems" with text "An operating system manages hardware...".
3. Navigate to **Ask AI** and ask "What does an operating system manage?".
4. The system queries the local HNSW database, pulls the context, feeds it to Llama 3.2 via Ollama, and returns the response.

## API Overview
| Endpoint | Method | Purpose | Response |
|---|---|---|---|
| `/search` | GET | K-NN search | Top matching vectors |
| `/insert` | POST | Insert demo vector | New vector ID |
| `/benchmark` | GET | Compare all 3 algorithms | Execution time per algorithm |
| `/doc/insert` | POST | Embed and store doc | Chunk IDs |
| `/doc/ask` | POST | Full RAG pipeline | LLM generated answer |

## Limitations
- **Scalability**: Runs completely in-memory with `std::unordered_map`. No persistence to disk.
- **Single-Threaded Indexing**: Vector insertion requires a global mutex lock, limiting concurrent writes.
- **RAG Context**: Limited by the static chunking size of 250 words and basic context injection.
- **No Authentication**: The API is open and meant for local use.

## Credits / Attribution
This project uses `cpp-httplib` by yhirose and relies on Ollama for AI model execution. It was adapted from an open-source vector database project for educational purposes.
