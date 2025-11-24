# Stage 1: build / install dependencies
FROM python:3.11-slim AS builder
WORKDIR /app

# install build deps
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
# install into a target folder to keep final image lean
RUN python -m pip install --upgrade pip \
 && python -m pip install --prefix=/install -r requirements.txt

# Stage 2: runtime
FROM python:3.11-slim
WORKDIR /app

# non-root user
RUN useradd --create-home appuser
COPY --from=builder /install /usr/local
COPY app ./app

EXPOSE 8000
USER appuser
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
