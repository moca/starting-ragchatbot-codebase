# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

A Retrieval-Augmented Generation (RAG) system for course materials that uses ChromaDB for vector storage, AWS Bedrock Claude with tool use for AI generation, and FastAPI for the backend. Course documents are automatically loaded on startup and made searchable through semantic search.

## Development Commands

### Setup
```bash
# Install dependencies
uv sync

# Create .env file with AWS credentials
# For temporary credentials, also include AWS_SESSION_TOKEN
cat > .env << EOF
AWS_ACCESS_KEY_ID=your-aws-access-key-id-here
AWS_SECRET_ACCESS_KEY=your-aws-secret-access-key-here
AWS_SESSION_TOKEN=your-aws-session-token-here
AWS_REGION=us-east-1
EOF
```

### Running the Application
```bash
# Using shell script (recommended)
./run.sh

# Manual start
cd backend && uv run uvicorn app:app --reload --port 8000
```

The application serves both the API (http://localhost:8000/api/*) and the frontend static files (http://localhost:8000).

## Architecture

### Core Components Flow

1. **RAGSystem** (`rag_system.py`) - Main orchestrator that coordinates all components
   - Initializes and connects: DocumentProcessor, VectorStore, AIGenerator, SessionManager, ToolManager
   - Handles two main operations: `add_course_folder()` and `query()`

2. **VectorStore** (`vector_store.py`) - ChromaDB wrapper with two collections:
   - `course_catalog` - Stores course metadata (title, instructor, lessons, links) indexed by course title
   - `course_content` - Stores content chunks with metadata (course_title, lesson_number, chunk_index)
   - Key method: `search()` resolves course names via catalog, then searches content with filters

3. **AIGenerator** (`ai_generator.py`) - AWS Bedrock Claude API integration with tool use
   - Uses tool-based approach where Claude decides when to search
   - Handles tool execution flow: initial response → tool execution → final response
   - System prompt restricts to one search per query

4. **ToolManager & CourseSearchTool** (`search_tools.py`) - Search abstraction layer
   - Provides tool definition for Claude API
   - Executes searches via VectorStore
   - Tracks sources for citation

5. **DocumentProcessor** (`document_processor.py`) - Parses course documents
   - Extracts course metadata from structured text format
   - Chunks content with overlap for better retrieval
   - Creates Course and CourseChunk objects

6. **SessionManager** (`session_manager.py`) - Conversation history
   - Maintains per-session conversation context
   - Configurable history length (MAX_HISTORY in config)

### Key Design Patterns

**Two-Collection Strategy**: Course catalog enables fuzzy name matching before content search. This allows users to search "intro to python" and match "Introduction to Python Programming".

**Tool-Based Search**: Claude decides when to use search tool rather than always searching. This enables handling general questions without unnecessary searches.

**Startup Document Loading**: FastAPI startup event (`startup_event` in `app.py`) automatically loads all documents from `../docs/` directory. Only new courses are added (checked by title).

**Filter Building**: VectorStore supports filtering by course_title and/or lesson_number using ChromaDB's where clause.

## Configuration

All settings in `backend/config.py`:
- `CHUNK_SIZE: 800` - Text chunk size for vector storage
- `CHUNK_OVERLAP: 100` - Overlap between chunks
- `MAX_RESULTS: 5` - Search results returned per query
- `MAX_HISTORY: 2` - Conversation turns to remember
- `AWS_BEDROCK_MODEL: "global.anthropic.claude-sonnet-4-20250514-v1:0"` - AWS Bedrock Claude model ID
- `EMBEDDING_MODEL: "all-MiniLM-L6-v2"` - Sentence transformer model

## Data Models

Located in `backend/models.py`:
- `Course` - Title, instructor, lessons list, course_link
- `Lesson` - Lesson number, title, link
- `CourseChunk` - Course title, lesson number, chunk index, content

## Adding New Course Documents

Place `.txt`, `.pdf`, or `.docx` files in `docs/` folder. They will be auto-loaded on server restart. Documents must follow structured format with:
- Course title
- Instructor name
- Lesson sections with numbers and titles
- Lesson links

See existing `docs/course*.txt` files for format examples.

## Frontend

Static files in `frontend/` served by FastAPI:
- `index.html` - Main UI
- `script.js` - API calls to `/api/query` and `/api/courses`
- `style.css` - Styling

`DevStaticFiles` class in `app.py` adds no-cache headers for development.
