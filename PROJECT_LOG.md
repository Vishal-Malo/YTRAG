# YTRAG Project Log

This file tracks the development progress, learning milestones, experiments, decisions, and important lessons learned while building YTRAG.

---

# Day 1 — Project Definition & Setup

## Completed

- Defined the YTRAG project idea.
- Finalized the project name: **YTRAG**.
- Defined the initial problem YTRAG aims to solve.
- Defined the initial MVP.
- Created the Git repository.
- Created the initial project structure.
- Added the README.
- Added the learning notes.
- Added project configuration files.

## Initial Project Structure

```text
ytrag/
├── README.md
├── PROJECT_LOG.md
├── LEARNING.md
├── requirements.txt
├── .gitignore
└── .env.example
```

## Key Decision

The project will start with a minimal structure.

Additional folders and abstractions will be introduced only when they are required by the implementation.

## Status

**Completed**

---

# Day 2 — LLM Fundamentals

## Topics Learned

- What is an LLM?
- Tokens
- Context window
- Temperature
- System prompts
- User prompts
- Prompt engineering

## Key Learnings

An LLM generates text based on the context provided to it.

A token is a basic unit of text processed by the model.

A context window defines how much tokenized information the model can consider at once.

Temperature affects the variability of generated output but does not provide additional knowledge.

Prompt engineering involves designing instructions, context, constraints, examples, and output requirements to guide the model.

## Day 2 Challenge

### Question

If an LLM can already answer questions, why do we need RAG?

### Conclusion

An LLM does not automatically have access to every external, private, or newly provided document.

RAG retrieves relevant information from an external source and provides that information to the LLM as context.

The key distinction:

**RAG retrieves. LLM generates.**

## Status

**Completed**

---

# Day 3 — Embeddings

## Topics Learned

- What embeddings are
- Why embeddings are needed
- Semantic similarity
- Vector representation
- Query embeddings
- Transcript chunk embeddings

## Key Learnings

An embedding is a numerical representation of a piece of text that captures useful semantic information.

Both transcript chunks and user questions can be converted into embeddings.

The embeddings can then be compared to identify semantically relevant transcript chunks.

Keyword-based search may fail when the words in the user's question differ from the wording used in the transcript.

## Mental Model

```text
YouTube Transcript
       ↓
    Chunks
       ↓
   Embeddings
       ↓
    Vectors
       ↓
Semantic Similarity
       ↓
Relevant Chunks
       ↓
      LLM
       ↓
    Answer
```

## Important Distinction

Embedding is a numerical representation of text.

It is not the same as generating an answer or directly "understanding" text like an LLM does.

## Status

**Completed**

---

# Day 4 — Vector Databases & Similarity Search

## Topics Learned

- Vector databases
- Vector storage
- Similarity search
- Top-K retrieval
- Vector search vs normal list iteration
- Query embeddings
- Retrieval flow

## Key Learnings

A vector database is designed to store and efficiently search vector representations such as embeddings.

It is useful because YTRAG may eventually contain a large number of transcript chunks and embeddings.

A vector database is not only responsible for storing vectors. It also provides mechanisms for efficiently searching those vectors based on similarity.

## Similarity Search

Similarity search compares the embedding of a user query against stored embeddings to identify the most relevant chunks.

The exact meaning of a similarity score depends on the similarity or distance method being used.

## Top-K Retrieval

Top-K retrieval means selecting the K most relevant chunks from the similarity search results.

Multiple chunks may be required because the answer to a question may span multiple transcript chunks.

## Important Data Model

A retrieved vector should be associated with the information needed to reconstruct useful context:

```text
Vector
   +
Chunk Text
   +
Metadata
```

Potential metadata can include:

```text
video_id
timestamp
chunk_id
```

## Day 4 Mental Model

```text
YouTube Transcript
       ↓
     Chunks
       ↓
   Embeddings
       ↓
      Vectors
       ↓
Vector Database
       ↓
   Query Vector
       ↓
Similarity Search
       ↓
  Top-K Chunks
       ↓
     Context
       ↓
      LLM
       ↓
    Answer
```

## Important Distinctions

**Embedding**

```text
Text → Vector
```

**Vector Database**

```text
Stores + efficiently searches vectors
```

**Similarity Search**

```text
Query Vector → Relevant Vectors
```

**Top-K Retrieval**

```text
All Search Results → K Most Relevant Chunks
```

**LLM**

```text
Retrieved Context + User Question → Answer
```

## Technology Decision

No vector database technology has been selected yet.

Potential technologies such as Chroma, FAISS, Pinecone, Qdrant, or pgvector will be evaluated later after understanding the underlying retrieval problem and trade-offs.

## Status

**Completed**

---

# Day 5 — RAG Fundamentals

## Planned Topics

- What is RAG?
- Why RAG is needed
- Retrieval vs generation
- RAG pipeline
- Context construction
- Grounded generation
- Basic RAG architecture

## Status

**Next**

---

# Future Learning Plan

## Phase 1 — Fundamentals

Learn the concepts required to understand the components of a RAG system.

```text
LLM
 ↓
Embeddings
 ↓
Vector Search
 ↓
Retrieval
 ↓
RAG
```

---

## Phase 2 — Basic RAG Prototype

Build a simple RAG pipeline and understand every component before introducing additional abstractions.

Focus areas:

- Document ingestion
- Chunking
- Embeddings
- Vector storage
- Retrieval
- Prompt construction
- Context injection
- LLM generation

---

## Phase 3 — LangChain

Learn how LangChain represents and connects the components already understood conceptually.

Focus areas:

- Models
- Prompt templates
- Documents
- Text splitters
- Embeddings
- Vector stores
- Retrievers
- Runnables
- Chains

---

## Phase 4 — YouTube Ingestion

Adapt the generic RAG pipeline to YouTube content.

Focus areas:

- YouTube URL handling
- Transcript extraction
- Transcript cleaning
- Timestamps
- Chunking
- Metadata

---

## Phase 5 — First Complete YTRAG

Connect the complete pipeline:

```text
YouTube URL
     ↓
Transcript
     ↓
Chunks
     ↓
Embeddings
     ↓
Vector Store
     ↓
Retriever
     ↓
Context
     ↓
LLM
     ↓
Answer
```

---

## Phase 6 — Learning Features

Potential features:

- Summaries
- Structured notes
- Conversational Q&A
- Timestamp references
- Key concepts
- ELI5 explanations
- Quizzes
- Flashcards
- Video chapters

Features will be added based on actual learning and product value.

---

## Phase 7 — Production RAG

Focus on making retrieval and the application more robust.

Topics:

- Chunk-size optimization
- Chunk overlap
- Metadata filtering
- Similarity thresholds
- Top-K tuning
- Context optimization
- Error handling
- Logging
- Caching
- Testing
- Docker

---

## Phase 8 — Advanced RAG

Explore advanced retrieval architectures:

- Hybrid search
- Reranking
- Query rewriting
- Query expansion
- Multi-query retrieval
- HyDE
- Parent-child retrieval
- Contextual compression
- Multi-video RAG
- Agentic RAG
- Corrective/self-reflective RAG

Advanced techniques will be introduced only after the basic RAG system is understood and working.

---

## Phase 9 — RAG Evaluation

Learn how to measure whether retrieval and generation are actually improving.

Potential areas:

- Retrieval recall
- Retrieval precision
- Context relevance
- Answer relevance
- Faithfulness / groundedness
- Golden datasets
- Failure analysis
- Chunking experiments
- Retrieval experiments

---

## Phase 10 — Production & Portfolio

Final goals:

- Clean architecture
- Automated testing
- Dockerization
- Deployment
- Documentation
- Architecture diagrams
- Performance considerations
- Portfolio presentation

---

# Development Philosophy

YTRAG is primarily a learning project.

The goal is not to use as many AI frameworks or technologies as possible.

The development approach is:

```text
Understand
    ↓
Explain
    ↓
Experiment
    ↓
Implement
    ↓
Evaluate
    ↓
Improve
```

Complexity should be introduced only when there is a clear problem that requires it.

---

# Current Status

**Current Day:** Day 4

**Current Phase:** AI & RAG Fundamentals

**Completed:** Days 1–4

**Next:** Day 5 — RAG Fundamentals