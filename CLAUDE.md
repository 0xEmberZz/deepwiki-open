# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

DeepWiki-Open is an automated documentation generator that creates interactive wikis for GitHub/GitLab/Bitbucket repositories. It uses AI (supporting multiple LLM providers) to analyze code, generate documentation, and create Mermaid diagrams with RAG-powered Q&A capabilities.

## Development Commands

### Frontend (Next.js)
```bash
npm run dev          # Start dev server (localhost:3000, uses Turbopack)
npm run build        # Production build
npm run lint         # ESLint
```

### Backend (Python/FastAPI)
```bash
python -m api.main                # Start API server (localhost:8001)
poetry install -C api             # Install dependencies
pytest                            # Run all tests
pytest tests/ -m unit             # Unit tests only
pytest tests/ -m integration      # Integration tests only
pytest tests/unit/test_xxx.py -v  # Run single test file
```

### Docker
```bash
docker-compose up                 # Run full stack
```

## Architecture

### Tech Stack
- **Frontend**: Next.js 15 (App Router), React 19, Tailwind CSS 4, TypeScript
- **Backend**: FastAPI, Python 3.11, Poetry
- **AI/ML**: Multiple LLM providers (Google Gemini, OpenAI, OpenRouter, Azure, Bedrock, Ollama, DashScope), FAISS for vector search

### Key Directories
```
api/                    # FastAPI backend
├── api.py              # Main API routes and WebSocket endpoints
├── rag.py              # RAG implementation for Q&A
├── data_pipeline.py    # Code parsing and embedding pipeline
├── websocket_wiki.py   # Real-time wiki generation via WebSocket
├── *_client.py         # LLM provider clients (openai, bedrock, azure, etc.)
└── config/             # JSON configs for models (generator.json, embedder.json)

src/                    # Next.js frontend
├── app/                # App Router pages
│   ├── [owner]/[repo]/ # Dynamic wiki routes
│   └── api/            # API routes (chat streaming, auth, projects)
├── components/         # React components
│   ├── Ask.tsx         # RAG-powered Q&A with DeepResearch
│   └── Mermaid.tsx     # Diagram rendering
└── utils/              # WebSocket client, URL utilities
```

### Data Flow
1. Repository cloning → Code parsing → Embeddings creation (FAISS)
2. AI documentation generation with Mermaid diagrams
3. Real-time progress via WebSocket
4. RAG-powered Q&A using vector search

### LLM Provider Configuration
Models are configured in `api/config/generator.json` and `api/config/embedder.json`. Each provider (google, openai, openrouter, azure, ollama, bedrock, dashscope) has its own client in `api/*_client.py`.

## Environment Variables

**Required**:
- `GOOGLE_API_KEY` or `OPENAI_API_KEY` (at least one for generation)

**Optional**:
- `OPENROUTER_API_KEY`, `OLLAMA_HOST` (default: http://localhost:11434)
- `AWS_ACCESS_KEY_ID`, `AWS_SECRET_ACCESS_KEY`, `AWS_REGION` (Bedrock)
- `AZURE_OPENAI_API_KEY`, `AZURE_OPENAI_ENDPOINT`, `AZURE_OPENAI_VERSION`
- `DEEPWIKI_EMBEDDER_TYPE` (openai|google|ollama|bedrock)
- `DEEPWIKI_AUTH_MODE`, `DEEPWIKI_AUTH_CODE` (for auth)

## Storage Paths
- `~/.adalflow/repos/` - Cloned repositories
- `~/.adalflow/databases/` - Vector embeddings
- `~/.adalflow/wikicache/` - Generated wiki cache
