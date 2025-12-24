# Course Materials RAG System

A Retrieval-Augmented Generation (RAG) system designed to answer questions about course materials using semantic search and AI-powered responses.

## Overview

This application is a full-stack web application that enables users to query course materials and receive intelligent, context-aware responses. It uses ChromaDB for vector storage, Anthropic's Claude for AI generation, and provides a web interface for interaction.


## Prerequisites

- Python 3.13 or higher
- uv (Python package manager)
- An Anthropic API key (for Claude AI)
- **For Windows**: Use Git Bash to run the application commands - [Download Git for Windows](https://git-scm.com/downloads/win)

## Installation

1. **Install uv** (if not already installed)
   ```bash
   curl -LsSf https://astral.sh/uv/install.sh | sh
   ```

2. **Install Python dependencies**
   ```bash
   uv sync
   ```

3. **Set up environment variables**
   
   Create a `.env` file in the root directory:
   ```bash
   ANTHROPIC_API_KEY=your_anthropic_api_key_here
   ```

## Running the Application

### Quick Start

Use the provided shell script:
```bash
chmod +x run.sh
./run.sh
```

### Manual Start

```bash
cd backend
uv run uvicorn app:app --reload --port 8000
```

The application will be available at:
- Web Interface: `http://localhost:8000`
- API Documentation: `http://localhost:8000/docs`

## GitHub Actions

This repository includes GitHub Actions workflows that use Claude Sonnet 4.5 via AWS Bedrock:

### 1. Claude Code Review
Automatically reviews pull requests using Claude AI, providing feedback on code quality, potential bugs, security concerns, and best practices.

**Triggers:** Automatically on pull requests or manually via workflow dispatch

### 2. Claude Assistant
General-purpose AI assistant that can answer questions about the codebase or help with coding tasks.

**Triggers:** 
- Add `claude-assist` label to any issue
- Manually via workflow dispatch with a custom query

### Setup for GitHub Actions

To use these workflows, configure the following GitHub repository secrets:
- `AWS_ACCESS_KEY_ID` - Your AWS access key ID
- `AWS_SECRET_ACCESS_KEY` - Your AWS secret access key  
- `AWS_SESSION_TOKEN` - (Optional) For temporary credentials
- `AWS_REGION` - (Optional) Defaults to `us-east-1`

For detailed documentation, see [.github/workflows/README.md](.github/workflows/README.md)

