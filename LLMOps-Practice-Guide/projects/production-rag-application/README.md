# Production-Ready RAG Application

## Project Overview

This project demonstrates a complete LLMOps pipeline for a Retrieval-Augmented Generation (RAG) application. It includes document processing, vector storage, prompt management, LLM integration, evaluation, monitoring, and production deployment.

## Architecture

```
┌─────────────┐
│  Documents  │
│  (PDF, TXT) │
└──────┬──────┘
       │
       ▼
┌─────────────────┐
│  Document       │
│  Processing     │ ◄── Text extraction, chunking
│  Pipeline       │
└──────┬──────────┘
       │
       ├──► Text Chunking
       ├──► Embedding Generation
       │
       ▼
┌─────────────┐
│   Vector    │
│  Database   │ ◄── Pinecone/Weaviate/Chroma
│ (Embeddings)│
└──────┬──────┘
       │
       ▼
┌─────────────────┐
│   RAG           │
│   Application   │ ◄── LangChain/LlamaIndex
│   (API)         │
└──────┬──────────┘
       │
       ├──► Query Processing
       ├──► Vector Search
       ├──► Context Retrieval
       ├──► Prompt Construction
       │
       ▼
┌─────────────┐
│   LLM API   │ ◄── OpenAI/Anthropic/Self-hosted
│  (GPT/Claude)│
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  Response   │
│  Generation │
└──────┬──────┘
       │
       ├──► Response Validation
       ├──► Guardrails
       │
       ▼
┌─────────────────┐
│   Monitoring    │ ◄── LangSmith/LangFuse
│   & Evaluation  │     Cost, Quality, Latency
└─────────────────┘
```

## Prerequisites

- Python 3.9+
- Docker Desktop or Docker Engine
- Kubernetes cluster (minikube, kind, or cloud provider)
- OpenAI API key (or Anthropic API key)
- Vector database account (Pinecone/Weaviate) or self-hosted
- kubectl

## Project Structure

```
production-rag-application/
├── data/                    # Sample documents
│   ├── documents/
│   └── processed/
├── src/
│   ├── ingestion/          # Document processing
│   │   ├── document_loader.py
│   │   ├── text_splitter.py
│   │   └── embedding_generator.py
│   ├── rag/                # RAG application
│   │   ├── vector_store.py
│   │   ├── retriever.py
│   │   ├── prompt_templates.py
│   │   └── rag_chain.py
│   ├── api/                # API server
│   │   ├── main.py
│   │   └── routes.py
│   ├── evaluation/         # Evaluation framework
│   │   ├── evaluators.py
│   │   └── test_suite.py
│   └── monitoring/         # Monitoring and observability
│       ├── langsmith_client.py
│       └── metrics.py
├── prompts/                # Prompt templates (versioned)
│   ├── v1/
│   ├── v2/
│   └── templates.yaml
├── infrastructure/
│   ├── kubernetes/         # K8s manifests
│   ├── docker/            # Dockerfiles
│   └── terraform/        # Infrastructure as Code
├── evaluation/            # Evaluation datasets
│   ├── test_questions.json
│   └── ground_truth.json
├── monitoring/            # Monitoring configs
│   └── dashboards/
├── .github/
│   └── workflows/
│       └── llm-cicd.yml   # CI/CD for LLM app
└── tests/                 # Test suites
    ├── unit/
    ├── integration/
    └── evaluation/
```

## Getting Started

### 1. Clone the Repository

```bash
git clone <repository-url>
cd LLMOps-Practice-Guide/projects/production-rag-application
```

### 2. Setup Environment

```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Set environment variables
export OPENAI_API_KEY="your-api-key"
export PINECONE_API_KEY="your-pinecone-key"  # or use Weaviate
export LANGSMITH_API_KEY="your-langsmith-key"  # optional
```

### 3. Setup Vector Database

#### Option A: Pinecone (Cloud)

```bash
# Create Pinecone index
python scripts/setup_pinecone.py
```

#### Option B: Weaviate (Self-hosted)

```bash
# Start Weaviate with Docker
docker-compose -f infrastructure/weaviate/docker-compose.yml up -d
```

### 4. Ingest Documents

```bash
# Process and ingest documents
python src/ingestion/document_loader.py --input data/documents/ --output data/processed/

# Generate embeddings and store in vector DB
python src/ingestion/embedding_generator.py --input data/processed/ --vector-db pinecone
```

### 5. Run RAG Application Locally

```bash
# Start API server
python src/api/main.py

# Test the API
curl -X POST http://localhost:8000/query \
  -H "Content-Type: application/json" \
  -d '{"question": "What is the main topic of the documents?"}'
```

### 6. Run Evaluation

```bash
# Run evaluation suite
python src/evaluation/evaluators.py --test-set evaluation/test_questions.json

# View evaluation results
# Results saved to evaluation/results/
```

### 7. Deploy to Kubernetes

```bash
# Build Docker image
docker build -t rag-application:latest -f infrastructure/docker/Dockerfile .

# Deploy to Kubernetes
kubectl apply -f infrastructure/kubernetes/

# Check deployment
kubectl get pods
kubectl get services

# Port forward to access API
kubectl port-forward svc/rag-api 8000:8000
```

## Features

- ✅ Document ingestion pipeline (PDF, TXT, Markdown)
- ✅ Text chunking and embedding generation
- ✅ Vector database integration (Pinecone/Weaviate)
- ✅ RAG implementation with LangChain
- ✅ Prompt versioning and management
- ✅ Multiple LLM provider support (OpenAI, Anthropic)
- ✅ Evaluation framework (automated + human)
- ✅ Monitoring and observability (LangSmith/LangFuse)
- ✅ Cost tracking and optimization
- ✅ Response caching
- ✅ Guardrails and safety filters
- ✅ Kubernetes deployment
- ✅ CI/CD pipeline

## API Endpoints

### Query Endpoint

```bash
POST /api/v1/query
{
  "question": "What is the main topic?",
  "top_k": 5,
  "temperature": 0.7,
  "prompt_version": "v2"
}

Response:
{
  "answer": "...",
  "sources": [...],
  "metadata": {
    "tokens_used": 150,
    "latency_ms": 1200,
    "cost_usd": 0.002
  }
}
```

### Document Ingestion

```bash
POST /api/v1/ingest
{
  "document_path": "/path/to/document.pdf",
  "metadata": {...}
}
```

### Evaluation

```bash
POST /api/v1/evaluate
{
  "test_set": "evaluation/test_questions.json",
  "metrics": ["accuracy", "relevance", "latency"]
}
```

## Prompt Management

Prompts are versioned in the `prompts/` directory:

```yaml
# prompts/templates.yaml
prompts:
  v1:
    system: "You are a helpful assistant..."
    user_template: "Context: {context}\nQuestion: {question}"
  v2:
    system: "You are an expert assistant..."
    user_template: "Based on the following context:\n{context}\n\nAnswer: {question}"
```

## Monitoring

### LangSmith Integration

```bash
# Set up LangSmith
export LANGSMITH_API_KEY="your-key"
export LANGSMITH_PROJECT="rag-application"

# View traces and metrics at https://smith.langchain.com
```

### Custom Metrics

- Response quality scores
- Token usage and costs
- Latency (p50, p95, p99)
- Error rates
- Cache hit rates
- User satisfaction scores

## Evaluation Metrics

1. **Retrieval Metrics**
   - Precision@K
   - Recall@K
   - MRR (Mean Reciprocal Rank)

2. **Generation Metrics**
   - BLEU score
   - ROUGE score
   - Semantic similarity
   - Answer relevance

3. **System Metrics**
   - Latency
   - Cost per query
   - Cache hit rate
   - Error rate

## Cost Optimization

1. **Caching**: Cache frequent queries
2. **Model Selection**: Use appropriate models for tasks
3. **Prompt Optimization**: Reduce prompt size
4. **Batch Processing**: Batch similar queries
5. **Token Limits**: Set max token limits

## Safety & Guardrails

- Content filtering (toxicity detection)
- PII detection and redaction
- Output validation
- Rate limiting
- Usage quotas

## Testing

```bash
# Run unit tests
pytest tests/unit/

# Run integration tests
pytest tests/integration/

# Run evaluation suite
pytest tests/evaluation/

# Test API endpoints
python tests/test_api.py
```

## Cleanup

```bash
# Remove Kubernetes resources
kubectl delete -f infrastructure/kubernetes/

# Stop Docker services
docker-compose down

# Clean vector database
python scripts/cleanup_vector_db.py
```

## Next Steps

- Add support for more document types
- Implement fine-tuning pipeline
- Add multi-modal RAG (images, audio)
- Implement advanced caching strategies
- Add A/B testing framework for prompts
- Implement human-in-the-loop workflows
- Add support for streaming responses

## Resources

- [LangChain Documentation](https://python.langchain.com/)
- [LlamaIndex Documentation](https://docs.llamaindex.ai/)
- [LangSmith Documentation](https://docs.smith.langchain.com/)
- [Pinecone Documentation](https://docs.pinecone.io/)
- [Weaviate Documentation](https://weaviate.io/developers/weaviate)
