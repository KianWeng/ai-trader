# AiTrader

## Project Overview

AiTrader is a quantitative trading platform for the OKX ecosystem. It features a decoupled architecture with three main components:

*   **Frontend (React/TypeScript):** Provides a dashboard for market data, account information, strategy interaction, and manual intervention.
*   **Backend (Rust/Axum):** Acts as a proxy for the OKX REST API, manages account data, positions, and trades, schedules strategies, and handles database operations.
*   **Agent (Python/FastAPI):** Houses a DeepSeek large language model for inference, strategy analysis, and execution of trades and market data queries.

These components communicate via HTTP/WebSocket. The frontend calls the Rust API, which in turn notifies the Python agent via WebSocket when strategy analysis is required. The agent, after analysis, writes results back to the database and API. All modules are configured through a `.env` file, allowing for easy switching between simulated and live trading environments.

## Building and Running

### Prerequisites

*   Rust (stable) and cargo
*   Python 3.11+ (virtual environment recommended)
*   Node.js 18+ and pnpm
*   PostgreSQL

### Environment Setup

1.  Copy `.env.example` to `.env`.
2.  Fill in the required environment variables:
    *   `OKX_API_KEY`, `OKX_API_SECRET`, `OKX_API_PASSPHRASE`
    *   `AGENT_BASE_URL`
    *   `DATABASE_URL`
    *   `DEEPSEEK_API_KEY`
3.  Adjust `STRATEGY_*` and `OKX_INST_IDS` parameters as needed.

### Running the Services

**Python Agent:**

```bash
cd agent
uv pip install -r requirements.txt
uvicorn llm.main:app --host 0.0.0.0 --port 8001
```

**Rust API:**

```bash
cd backend
cargo run
```

**React Frontend:**

```bash
cd frontend
pnpm install
pnpm dev
```

## Development Conventions

*   **Configuration:** The entire stack is driven by environment variables defined in a `.env` file.
*   **Strategy Triggers:** The system supports three ways to trigger trading strategies:
    1.  **Manual:** Via a frontend or CLI call.
    2.  **Scheduled:** A periodic check as a fallback.
    3.  **Volatility:** Triggered by significant price movements.
*   **Deployment:** The repository includes a `nginx/build.sh` script for creating a production build and `nginx/deploy.sh` for deploying the application with systemd and Nginx.
