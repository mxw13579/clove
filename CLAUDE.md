# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Clove is a Claude.ai reverse proxy written in Python with FastAPI backend and React frontend. It provides two modes of operation:
- **OAuth mode**: Direct access to Claude API with full functionality (preferred)
- **Web reverse proxy mode**: Fallback mode that simulates Claude.ai web interface

The project serves as a bridge allowing various AI applications to connect to Claude through a standardized API interface.

## Development Commands

### Python Backend Commands
- `python -m app.main` - Run the backend application directly
- `clove` - Run installed version (after `pip install`)
- `python scripts/build_wheel.py` - Build frontend and create Python wheel
- `make run` - Run application in development mode
- `make build` - Build both frontend and Python wheel
- `make install` - Build and install the package
- `make install-dev` - Install in development mode with `-e` flag
- `make clean` - Clean all build artifacts

### Frontend Commands (in /front directory)
- `pnpm dev` - Start frontend development server
- `pnpm build` - Build frontend for production (outputs to dist/)
- `pnpm lint` - Run ESLint on frontend code
- `pnpm preview` - Preview built frontend

### Code Quality
- `ruff` - Python linting (included in dev dependencies)
- No test framework is currently configured

## Architecture Overview

### Backend Structure
- **FastAPI application** (`app/main.py`) with lifespan management
- **Processing pipeline** system for request/response handling through modular processors
- **Account management** system supporting multiple Claude accounts with automatic OAuth
- **Session management** for maintaining Claude.ai web sessions
- **Dual-mode operation**: Automatic switching between Claude API and web reverse proxy

### Key Components
- **Processors** (`app/processors/claude_ai/`): Modular pipeline for request processing including model injection, tool calls, event parsing, and response streaming
- **Services**: Account management, session handling, caching, tool calls, and OAuth flow
- **External clients**: HTTP clients for both Claude API and Claude.ai web interface
- **Configuration**: Environment-based settings with JSON override support

### Frontend Structure
- **React + TypeScript** with Vite build system
- **Tailwind CSS + Shadcn UI** for styling and components
- **Account management UI** for adding/managing Claude accounts
- **OAuth flow UI** for authentication setup
- **Settings management** for proxy configuration

### Request Flow
1. API request received through FastAPI routes (`/v1/` endpoints)
2. Request processed through Claude AI pipeline with multiple processors
3. Account selection based on model requirements and availability
4. Automatic mode selection (OAuth API vs web reverse proxy)
5. Response streaming back to client with proper formatting

## Configuration

### Environment Variables
- Core settings via `.env` file (see `.env.example` for full reference)
- `PORT=5201` - Server port
- `HOST=0.0.0.0` - Server host
- `DATA_FOLDER` - Persistent data storage location
- Account credentials via `COOKIES` or `ADMIN_API_KEYS`

### Configuration Priority
1. JSON config file (`data_folder/config.json`)
2. Environment variables
3. `.env` file
4. Default values

## Important Implementation Notes

### Multi-Account Support
The system manages multiple Claude accounts with intelligent routing based on:
- Model availability (Free/Pro/Max tiers)
- Account quota status
- OAuth availability vs web-only access

### Processing Pipeline
Request processing follows a configurable pipeline pattern where each processor handles specific aspects:
- Authentication and account selection
- Model compatibility checking
- Tool call handling and event processing
- Response streaming and formatting
- Token counting and stop sequence processing

### Session Management
Web reverse proxy mode maintains persistent sessions with Claude.ai, including:
- Conversation UUID tracking
- Automatic cleanup on completion
- Connection pooling and resource management

### Security Considerations
- OAuth client credentials for Claude API access
- Admin API key system for management interface
- Cookie-based authentication for web reverse proxy mode
- CORS enabled for frontend integration