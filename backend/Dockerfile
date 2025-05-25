FROM python:3.11-slim

# Set environment variables
ENV PYTHONUNBUFFERED=1 \
    PYTHONDONTWRITEBYTECODE=1 \
    ENV_MODE="production" \
    PYTHONPATH=/app

WORKDIR /app

ENV SUPABASE_URL="https://zqpphxufzpveldcydqyj.supabase.co"
ENV SUPABASE_ANON_KEY="eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InpxcHBoeHVmenB2ZWxkY3lkcXlqIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NDQ0MjA0MjAsImV4cCI6MjA1OTk5NjQyMH0.9bENh7_v7ynGZdxjxBYbDlpj37ZrtlDgir3hi1k-WWw"
ENV SUPABASE_SERVICE_ROLE_KEY="eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJzdXBhYmFzZSIsInJlZiI6InpxcHBoeHVmenB2ZWxkY3lkcXlqIiwicm9sZSI6ImFub24iLCJpYXQiOjE3NDQ0MjA0MjAsImV4cCI6MjA1OTk5NjQyMH0.9bENh7_v7ynGZdxjxBYbDlpj37ZrtlDgir3hi1k-WWw"
ENV DAYTONA_API_KEY="your-daytona-api-key-here"
ENV DAYTONA_SERVER_URL="https://api.daytona.io"
ENV DAYTONA_TARGET="us"
ENV TAVILY_API_KEY="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"
ENV RAPID_API_KEY="7ec192d321msh105663e07ab3c7cp17583bjsn9227eb48b77b"
ENV FIRECRAWL_API_KEY="fc-f3246dc7add64baca918251b06f679f0"

# Install system dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    curl \
    && rm -rf /var/lib/apt/lists/*

# Create non-root user and set up directories
RUN useradd -m -u 1000 appuser && \
    mkdir -p /app/logs && \
    chown -R appuser:appuser /app

# Install Python dependencies
COPY --chown=appuser:appuser requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt gunicorn

# Switch to non-root user
USER appuser

# Copy application code
COPY --chown=appuser:appuser . .

# Expose the port the app runs on
EXPOSE 8000

# Calculate optimal worker count based on 16 vCPUs
# Using (2*CPU)+1 formula for CPU-bound applications
ENV WORKERS=33
ENV THREADS=2
ENV WORKER_CONNECTIONS=2000

# Gunicorn configuration
CMD ["sh", "-c", "gunicorn api:app \
     --workers $WORKERS \
     --worker-class uvicorn.workers.UvicornWorker \
     --bind 0.0.0.0:8000 \
     --timeout 1800 \
     --graceful-timeout 600 \
     --keep-alive 1800 \
     --max-requests 0 \
     --max-requests-jitter 0 \
     --forwarded-allow-ips '*' \
     --worker-connections $WORKER_CONNECTIONS \
     --worker-tmp-dir /dev/shm \
     --preload \
     --log-level info \
     --access-logfile - \
     --error-logfile - \
     --capture-output \
     --enable-stdio-inheritance \
     --threads $THREADS"]
