# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Retrieval-Augmented Generation (RAG) system for course materials using FastAPI backend and vanilla JavaScript frontend. The system processes course documents, stores them in ChromaDB for semantic search, and uses Anthropic's Claude AI to generate intelligent responses about course content.

## Common Commands

**Start the application:**
```bash
./run.sh
```

**Start manually:**
```bash
cd backend
uv run uvicorn app:app --reload --port 8000
```

**Install dependencies:**
```bash
uv sync
```

**Access the application:**
- Web Interface: http://localhost:8000
- API Documentation: http://localhost:8000/docs

## Architecture

The system follows a modular architecture with clear separation of concerns:

### Backend Components (`backend/`)

- **`app.py`**: FastAPI application with CORS middleware, API endpoints (`/api/query`, `/api/courses`), and static file serving
- **`rag_system.py`**: Main orchestrator that coordinates all components for document processing and query handling
- **`vector_store.py`**: ChromaDB integration with separate collections for course metadata and content chunks
- **`ai_generator.py`**: Anthropic Claude integration with tool-calling support
- **`document_processor.py`**: Text processing and chunking for various document formats (.pdf, .docx, .txt)
- **`search_tools.py`**: Tool system for semantic search that the AI can call during conversations
- **`session_manager.py`**: Conversation history management for multi-turn dialogues
- **`models.py`**: Pydantic models for `Course`, `Lesson`, and `CourseChunk` data structures
- **`config.py`**: Configuration management with environment variables

### Data Models

- **Course**: Contains title, instructor, lessons, and course link
- **Lesson**: Individual lesson with number, title, and optional link
- **CourseChunk**: Text segments for vector storage with course/lesson metadata

### Key Architectural Patterns

1. **Tool-based AI interaction**: The AI uses function calling to search the vector store rather than traditional RAG retrieval
2. **Dual collections**: Separate ChromaDB collections for course metadata and content chunks
3. **Session-aware conversations**: Multi-turn dialogue support with conversation history
4. **Chunked processing**: Documents split into overlapping chunks (800 chars, 100 overlap) for semantic search
5. **Incremental loading**: Avoids reprocessing existing courses when adding new documents

## Environment Setup

Required environment variables in `.env`:
```
ANTHROPIC_API_KEY=your_anthropic_api_key_here
```

## Document Processing

Course documents are loaded from the `docs/` directory on startup. The system:
1. Processes PDF, DOCX, and TXT files
2. Extracts course structure (title, lessons)
3. Creates text chunks with metadata
4. Stores in ChromaDB for semantic search
5. Avoids duplicate processing of existing courses

## Development Notes

- Uses `uv` for Python dependency management
- FastAPI serves both API and static frontend files
- ChromaDB storage is persisted in `backend/chroma_db/`
- Development mode includes no-cache headers for static files
- CORS is enabled for all origins (development setup)
- make sure to use uv to manage all dependencies