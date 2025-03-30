Agent System for KMMLU Evaluation
===================================

This repository contains code to build an agent system for evaluating performance on the KMMLU benchmark (Criminal-Law category) using GPT-4o-mini and text-embedding-small models.

Overview:
---------
This pipeline:
1. Loads raw KMMLU data (both training and test splits) from the Hugging Face Hub.
2. Cleans the raw data.
3. Builds a retrieval index from the training data (using only question text and options, with no answer leakage) via the text-embedding-small model.
4. For each test question, retrieves similar examples from the training set.
5. Constructs a prompt that includes retrieval context plus the test question.
6. Calls the OpenAI batch API (using GPT-4o-mini) to obtain answers in structured JSON.
7. Evaluates the model’s accuracy against the ground truth.
8. Saves the batch API input and output JSONL files and benchmark score.

Setup Instructions:
-------------------
Requirements:
    - Python 3.9+
    - Poetry (for dependency management)
    - Docker and docker-compose

Using Poetry:
    1. Install Poetry if not already installed: https://python-poetry.org/docs/
    2. Run: `poetry install`
    3. Then execute: `poetry run python main.py`

Using Docker:
    1. Build and run the container using docker-compose:
         docker-compose up --build
    2. Make sure to set your OPENAI_API_KEY in the environment.

Files Submitted:
----------------
- main.py               : This script.
- Dockerfile            : (See below in the multi-line string.)
- docker-compose.yml    : (See below.)
- pyproject.toml        : (See below.)
- README.md             : (See this README.)
- batch_input.jsonl     : Input file for the batch API.
- batch_output.jsonl    : Output file from the batch API.
- benchmark_score.txt   : Final benchmark score.

-------------------------
Below are example configurations for Docker and Poetry:

Dockerfile (example):
---------------------
FROM python:3.9-slim
WORKDIR /app
COPY . /app
RUN pip install poetry && \
    poetry config virtualenvs.create false && \
    poetry install --no-dev
CMD ["python", "main.py"]

docker-compose.yml (example):
-----------------------------
version: "3.8"
services:
  agent:
    build: .
    container_name: agent_system
    volumes:
      - .:/app
    environment:
      - OPENAI_API_KEY=${OPENAI_API_KEY}

pyproject.toml (example):
--------------------------
[tool.poetry]
name = "agent_system"
version = "0.1.0"
description = "Agent System for KMMLU evaluation"
authors = ["Your Name <your.email@example.com>"]

[tool.poetry.dependencies]
python = "^3.9"
pandas = "^1.3.0"
openai = "^0.27.0"
numpy = "^1.21.0"
scikit-learn = "^1.0.0"
tqdm = "^4.0.0"

[build-system]
requires = ["poetry-core>=1.0.0"]
build-backend = "poetry.core.masonry.api"
