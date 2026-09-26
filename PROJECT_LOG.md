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
Stores and efficiently searches vectors
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

## Topics Learned

- What is RAG?
- Why RAG is needed
- Retrieval vs generation
- RAG pipeline
- Indexing pipeline vs query pipeline
- Context construction
- Grounded generation
- Basic RAG architecture
- RAG failure points
- RAG vs fine-tuning

## Key Learnings

RAG stands for **Retrieval-Augmented Generation**.

RAG retrieves relevant information from an external source and provides that information as context to an LLM so that the LLM can generate a response using the retrieved information.

For YTRAG, the external knowledge source is primarily the YouTube transcript.

## Indexing vs Query Pipeline

A major distinction learned on Day 5 is that a RAG system can be viewed as two pipelines.

### Indexing Pipeline

The indexing pipeline prepares information for later retrieval:

```text
YouTube Video
      ↓
  Transcript
      ↓
    Chunks
      ↓
  Embeddings
      ↓
 Vector Store
```

The transcript embeddings are normally created when the video is indexed, not again for every question.

### Query Pipeline

The query pipeline handles a user's question:

```text
User Question
      ↓
Query Embedding
      ↓
Similarity Search
      ↓
Top-K Relevant Chunks
      ↓
    Context
      ↓
Prompt + Context
      ↓
      LLM
      ↓
    Answer
```

This distinction will be important when implementation begins because indexing and querying have different responsibilities and execution patterns.

## Context Construction

The vector search operates on embeddings, but the LLM needs the actual retrieved transcript text.

Therefore, a useful stored record conceptually contains:

```text
Vector
+
Chunk Text
+
Metadata
```

The retrieved chunk text is used to construct the context provided to the LLM along with the user's question.

## Grounded Generation

Grounded generation means that the generated response should be supported by the retrieved context.

For YTRAG, the retrieved transcript chunks should act as the primary evidence for questions about the video.

RAG does not guarantee zero hallucinations. A system can still fail when retrieval returns the wrong chunks, context is poorly constructed, or the LLM produces an unsupported interpretation.

## RAG vs Fine-Tuning

**RAG** uses external information at query time by retrieving relevant content and providing it as context.

**Fine-tuning** uses additional training data to change model parameters so that the model behaves or responds in a more suitable way for a specific requirement.

For YTRAG, RAG allows new or different videos to be indexed without retraining the underlying LLM for every new video.

## Important Distinction

The key distinction remains:

**RAG retrieves. The LLM generates.**

More precisely:

```text
Retrieval → Finds relevant information
Augmentation → Adds retrieved information to the model input/context
Generation → LLM produces the response
```

## RAG Failure Points

A basic RAG system can fail at multiple stages:

```text
Question
   ↓
Retrieval
   ↓
Context
   ↓
Generation
   ↓
Answer
```

If the correct chunk exists in the vector store but unrelated chunks are returned, the observed failure is a **retrieval failure**. The underlying cause could be related to chunking, embeddings, query representation, similarity settings, Top-K, or other retrieval choices.

If useful context is retrieved but the LLM produces an incorrect or unsupported response, the failure is in generation or in how the context was used.

These failure modes provide the motivation for the later production RAG, advanced RAG, and evaluation topics in the roadmap.

## Day 5 Mental Model

```text
             INDEXING
                ↓
        YouTube Transcript
                ↓
              Chunks
                ↓
            Embeddings
                ↓
           Vector Store
                │
════════════════╪══════════════════
                │
              QUERY
                ↓
          User Question
                ↓
        Query Embedding
                ↓
        Similarity Search
                ↓
      Top-K Relevant Chunks
                ↓
             Context
                ↓
               LLM
                ↓
             Answer
```

## Status

**Completed**

---

# Day 6 — Basic RAG Architecture

## Topics Learned

- End-to-end basic RAG flow
- Indexing pipeline vs query pipeline in greater detail
- Query embedding
- Retrieval
- Top-K retrieval
- Retrieved vectors vs retrieved text
- Context construction
- Prompt construction
- Generation
- Retrieval failure
- Context failure
- Generation failure

## Key Learnings

Day 6 focused on understanding how the individual RAG components work together rather than treating RAG as a single black box.

The complete basic architecture is:

```text
                    INDEXING
                       │
YouTube Video          │
      ↓                │
Transcript             │
      ↓                │
    Chunks              │
      ↓                │
  Embeddings            │
      ↓                │
Vector Database         │
                       │
═══════════════════════╪════════════════════
                       │
                     QUERY
                       ↓
                 User Question
                       ↓
                Query Embedding
                       ↓
               Similarity Search
                       ↓
              Top-K Relevant Chunks
                       ↓
                Context Construction
                       ↓
                       LLM
                       ↓
                     Answer
```

## Indexing Pipeline

The indexing pipeline prepares external information for retrieval.

```text
External Document
      ↓
    Chunks
      ↓
   Embeddings
      ↓
Vector + Text + Metadata
      ↓
Vector Store
```

For YTRAG:

```text
YouTube Video
      ↓
Transcript
      ↓
Chunks
      ↓
Embeddings
      ↓
Vector Store
```

The important idea is that indexing prepares knowledge before the user asks questions.

## Query Pipeline

The query pipeline retrieves relevant information for an individual question.

```text
User Question
      ↓
Query Embedding
      ↓
Similarity Search
      ↓
Top-K Relevant Chunks
      ↓
Context Construction
      ↓
LLM
      ↓
Answer
```

The query and stored chunk embeddings need to be represented in the same vector space so that their semantic relationship can be measured.

## Vector vs Retrieved Text

A vector is a numerical representation used for semantic retrieval.

The retrieved text is the actual information from the original document that can be provided to the LLM.

The key distinction is:

```text
Vector → FIND
Text   → INFORM
```

The vector helps determine which information is relevant.

The text provides the actual evidence that the LLM can use when generating the response.

## Retrieval vs Context Construction

**Retrieval** answers:

```text
Which information should I use?
```

It finds relevant chunks according to the user query.

**Context Construction** answers:

```text
How should I present the retrieved information to the LLM?
```

It can combine:

```text
System Instructions
+
Retrieved Text
+
User Question
```

to construct the input/context provided to the LLM.

## Basic RAG Mental Model

```text
Question
   ↓
Embedding
   ↓
Vector Search
   ↓
Retrieve Chunks
   ↓
Context Construction
   ↓
LLM
   ↓
Answer
```

The compressed version is:

```text
Retrieve → Prepare Context → Generate
```

## RAG Failure Analysis

A basic RAG system can fail at three major stages.

### Retrieval Failure

The correct information exists in the vector store, but the relevant chunk is not retrieved.

```text
Correct Chunk Exists
       ↓
Similarity Search
       ↓
Wrong / Irrelevant Chunks
```

The problem is not necessarily caused by Top-K itself.

Possible underlying causes include:

- Poor chunking
- Poor embedding representation
- Query representation
- Similarity method or configuration
- Retrieval parameters

### Context Failure

Relevant information is retrieved, but the context provided to the LLM is poorly constructed, incomplete, or contains too much irrelevant information.

```text
Correct Information
        ↓
Poor Context Construction
        ↓
LLM receives incomplete or irrelevant context
```

### Generation Failure

The LLM receives appropriate context but produces an unsupported or incorrect answer.

```text
Correct Context
      ↓
     LLM
      ↓
Unsupported / Incorrect Answer
```

A prompt can instruct the LLM to stay within the retrieved context, but such instructions are a mitigation rather than a guarantee.

## Important Day 6 Distinctions

```text
Indexing
→ Prepare external information for retrieval.

Query
→ Use a user question to retrieve relevant information.

Vector
→ Numerical representation used for retrieval.

Retrieved Text
→ Actual information provided to the LLM.

Retrieval
→ Find relevant information.

Augmentation
→ Add retrieved information to the model input/context.

Context Construction
→ Organize retrieved information and other inputs for the LLM.

Generation
→ LLM produces the response.
```

## Day 6 Checkpoint

The following concepts were reviewed:

- Query embeddings
- Vector vs retrieved text
- Retrieval vs context construction
- Retrieval failure
- Context failure
- Generation failure
- Indexing vs query pipeline
- Complete basic RAG architecture

The checkpoint confirmed that the core architecture is understood.

## Status

**Completed**

---

# Day 7 — Retrieval Engineering

## Topics Learned

- Query embeddings
- Similarity search
- Similarity scores
- Top-K retrieval
- Top-K trade-offs
- Similarity thresholds
- Top-K vs similarity thresholds
- Precision
- Recall
- Retrieval failure
- Similarity score vs guaranteed relevance
- Retrieval vs generation

## Key Learnings

Day 7 focused on understanding the retrieval stage in greater depth.

The core retrieval pipeline is:

```text
User Question
      ↓
Query Embedding
      ↓
Similarity Search
      ↓
Similarity Ranking
      ↓
Top-K / Threshold
      ↓
Retrieved Chunks
      ↓
Context Construction
      ↓
LLM
      ↓
Answer
```

## Query Embedding

The user's question must be converted into a vector so that it can be compared against stored transcript chunk vectors.

```text
User Question
      ↓
Embedding Model
      ↓
Query Vector
```

The query and stored embeddings need to exist in a compatible vector space.

## Similarity Score

A similarity score measures vector-level similarity according to the selected similarity or distance method.

It is an important signal for estimating relevance.

However:

```text
Similarity Score
      ≠
Guaranteed Relevance
      ≠
Truth
```

A highly similar chunk can still be incomplete, irrelevant in context, or insufficient to answer the user's question.

## Top-K Retrieval

Top-K selects the K highest-ranked retrieval results.

Example:

```text
A → 0.95
B → 0.91
C → 0.88
D → 0.70
E → 0.42
```

With:

```text
K = 3
```

the system retrieves:

```text
A
B
C
```

## Top-K Trade-off

Small K:

```text
Less context
Less noise
Potentially lower recall
```

Large K:

```text
More context
Potentially higher recall
More noise
Higher token usage
```

Therefore, K needs to be tuned according to the actual retrieval task.

## Similarity Threshold

A similarity threshold defines the minimum similarity required for a result to be included.

For example:

```text
Threshold = 0.80
```

would keep:

```text
A → 0.95
B → 0.91
C → 0.88
```

while excluding:

```text
D → 0.70
E → 0.42
```

Thresholds are dependent on the embedding model, dataset, similarity method, and retrieval setup.

## Top-K vs Threshold

```text
Top-K
→ Return the K highest-ranked results.

Threshold
→ Return only results above the minimum similarity.
```

They can be combined:

```text
Similarity Search
      ↓
Threshold Filtering
      ↓
Top-K Selection
      ↓
Final Context
```

## Precision and Recall

Precision asks:

> How much of the retrieved information is relevant?

Recall asks:

> How much of the relevant information was successfully retrieved?

Conceptually:

```text
Precision =
Relevant Retrieved
-----------------
All Retrieved
```

```text
Recall =
Relevant Retrieved
-----------------
All Relevant
```

For example, if there are 10 relevant chunks and the system retrieves 5 chunks, of which 4 are relevant:

```text
Precision = 4 / 5 = 80%

Recall = 4 / 10 = 40%
```

This shows that a retriever can retrieve mostly relevant information while still missing a large amount of relevant information.

## Retrieval Failure

A retrieval failure occurs when relevant information exists but the appropriate chunk is not retrieved.

Possible causes include:

- Chunking
- Embeddings
- Query representation
- Similarity method
- Top-K configuration
- Similarity threshold
- Other retrieval parameters

Top-K itself should not automatically be considered the cause of every retrieval failure.

## Irrelevant Context and Generation

Suppose retrieval returns:

```text
Chunk A → Relevant
Chunk B → Relevant
Chunk C → Irrelevant
Chunk D → Irrelevant
```

Even if the prompt instructs the LLM to use retrieved context, C and D are still present in the context.

The LLM may ignore them, but this is not guaranteed.

It may instead:

- Correctly focus on A and B.
- Be distracted by C and D.
- Interpret an irrelevant chunk as relevant.
- Combine relevant and irrelevant information.
- Produce an unsupported response.

Therefore, improving retrieval quality directly improves the quality of the context provided to the LLM.

## Important Day 7 Distinctions

```text
Query Embedding
→ User Question → Vector

Similarity Score
→ Vector-level similarity signal

Top-K
→ Number of highest-ranked results selected

Threshold
→ Minimum similarity requirement

Precision
→ Relevance of retrieved information

Recall
→ Coverage of relevant information

Retrieval
→ Find information

Context Construction
→ Prepare information for the LLM

Generation
→ Produce the final response
```

## Day 7 Checkpoint Review

### Q1 — Why embed the user question?

Because similarity search operates on vectors, the user question must also be represented as a vector so that it can be compared with stored chunk vectors.

### Q2 — What does a similarity score tell us?

It tells us how similar two vectors are according to the selected similarity or distance method.

It is a relevance signal, but it does not guarantee that the chunk is actually relevant, correct, or sufficient to answer the question.

### Q3 — Top-K and threshold

Given:

```text
A → 0.95
B → 0.91
C → 0.88
D → 0.70
E → 0.42
```

For:

```text
K = 3
```

the result is:

```text
A, B, C
```

For:

```text
Threshold = 0.80
```

the result is also:

```text
A, B, C
```

### Q4 — Top-K vs threshold

Top-K specifies how many top-ranked results to return.

A similarity threshold specifies the minimum similarity required for a result to qualify.

### Q5 — Why can Top-K = 1 be problematic?

Because the answer may require information from multiple chunks.

### Q6 — Precision vs recall

Precision measures how much of the retrieved information is relevant.

Recall measures how much of the relevant information was successfully retrieved.

### Q7 — Missing relevant chunks

If the correct information exists but the retriever fails to return it, the primary problem is retrieval.

### Q8 — Why can't we guarantee that the LLM ignores irrelevant chunks?

Because prompt instructions do not guarantee model behavior.

If irrelevant chunks are included in the context, the LLM can potentially use or be influenced by them.

This reinforces the importance of retrieval quality.

## Day 7 Mental Model

```text
User Question
      ↓
Query Embedding
      ↓
Similarity Search
      ↓
Similarity Ranking
      ↓
Top-K / Threshold
      ↓
Relevant Chunks
      ↓
Context Construction
      ↓
LLM
      ↓
Answer
```

## Status

**Completed**

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
- Retrieval testing
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

**Current Day:** Day 7

**Current Phase:** AI & RAG Fundamentals → Basic RAG Prototype

**Completed:** Days 1–7

**Next:** Day 8 — Retrieval Testing & Basic RAG Prototype