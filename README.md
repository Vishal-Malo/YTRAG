# YTRAG

AI-powered YouTube learning assistant built with Python, LangChain, RAG, and Streamlit.

## Problem Statement

YouTube contains a huge amount of educational content, but learning from long videos can be time-consuming. Users may need to watch an entire video to find a specific concept, create their own notes, revise the content, or ask questions about something discussed in the video.

YTRAG aims to make educational YouTube videos interactive by allowing users to provide a video and use AI to understand and explore its content.

## Project Goal

Build an AI-powered application that can process a YouTube video's transcript and allow users to interact with the video's knowledge through Retrieval-Augmented Generation (RAG).

The initial goal is to build a reliable pipeline for:

YouTube Video → Transcript → Chunks → Embeddings → Vector Store → Retrieval → LLM → Answer

## MVP

The first version of YTRAG will support:

- YouTube video transcript extraction
- Transcript processing and chunking
- Embedding generation
- Vector-based retrieval
- Question answering using RAG
- Basic Streamlit interface

## Planned Features

- AI-generated video summaries
- Structured notes
- Conversational Q&A
- Source and timestamp citations
- Quiz generation
- Flashcard generation
- Key concept extraction
- ELI5 explanations
- Video chapter generation
- Multi-video RAG
- Video comparison
- RAG evaluation dashboard

## Architecture

```text
YouTube URL
     │
     ▼
Transcript Extraction
     │
     ▼
Text Processing
     │
     ▼
Text Chunking
     │
     ▼
Embedding Model
     │
     ▼
Vector Store
     │
     ▼
Retriever
     │
     ▼
Relevant Context
     │
     ▼
LLM
     │
     ▼
Answer
     │
     ▼
Streamlit UI
```

## Tech Stack

| Component | Technology |
|---|---|
| Language | Python |
| UI | Streamlit |
| LLM Framework | LangChain |
| LLM | TBD |
| Embedding Model | TBD |
| Vector Store | TBD |
| Video Source | YouTube |
| Version Control | Git / GitHub |

Technologies marked as TBD will be selected after understanding the project requirements rather than being added arbitrarily.

## Project Roadmap

### Phase 1 — Fundamentals

- Understand LLMs
- Understand tokens and context windows
- Understand embeddings
- Understand vector databases
- Understand similarity search
- Understand RAG
- Understand LangChain abstractions

### Phase 2 — RAG Prototype

- Extract YouTube transcript
- Process transcript
- Implement chunking
- Generate embeddings
- Store embeddings
- Implement retrieval
- Generate answers using retrieved context

### Phase 3 — Application

- Build Streamlit interface
- Add video processing workflow
- Add question answering
- Add basic error handling

### Phase 4 — Learning Features

- Summary generation
- Structured notes
- Conversational chat
- Quiz generation
- Flashcards
- Chapter generation

### Phase 5 — Evaluation

- Create evaluation dataset
- Evaluate retrieval quality
- Evaluate answer relevance
- Analyze hallucinations
- Improve retrieval and prompting

### Phase 6 — Production & Portfolio

- Refactor project
- Improve documentation
- Add tests
- Containerize application
- Deploy application
- Add demo and screenshots

## Project Status

🚧 Early Development — Project Definition

## Future Improvements

Future improvements will be driven by actual problems discovered during development rather than adding technologies or features solely for complexity.

## License

To be decided.