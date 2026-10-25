FROM python:3.11-slim

# Install system build dependencies required for compiling C extensions (like httptools)
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Install uv using the official installer script
COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

# Set the working directory
WORKDIR /app

# Enable bytecode compilation and disable uv update checks for container builds
ENV UV_COMPILE_BYTECODE=1 \
    UV_LINK_MODE=copy

# Copy dependency management files first for efficient caching
COPY pyproject.toml uv.lock ./

# Install dependencies without installing the project itself yet (--frozen ensures exact lockfile match)
RUN uv sync --frozen --no-dev --no-install-project

# Copy the rest of the application code
COPY . .

# Run the FastAPI development server, listening on all interfaces at port 5000
CMD ["uv", "run", "fastapi", "dev", "app/main.py", "--host", "0.0.0.0", "--port", "5000"]