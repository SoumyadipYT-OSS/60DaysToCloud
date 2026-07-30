# Production-Ready ML Model Pipeline

## Project Overview

This project demonstrates a complete MLOps pipeline for deploying a machine learning model to production. It includes data versioning, experiment tracking, automated training, model serving, and comprehensive monitoring.

## Architecture

```
┌─────────────┐
│   Data      │
│   Sources   │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   DVC       │ ◄── Data Versioning
│  (Storage)  │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│  Training   │
│  Pipeline   │ ◄── Kubeflow/Airflow
└──────┬──────┘
       │
       ├──► Feature Engineering
       ├──► Model Training
       ├──► Hyperparameter Tuning
       │
       ▼
┌─────────────┐
│   MLflow    │ ◄── Experiment Tracking
│  (Registry) │     Model Registry
└──────┬──────┘
       │
       ▼
┌─────────────┐
│   Model     │
│   Serving   │ ◄── FastAPI + Docker
│   (API)     │
└──────┬──────┘
       │
       ▼
┌─────────────┐
│ Kubernetes  │ ◄── Production Deployment
│  Cluster    │
└──────┬──────┘
       │
       ├──► Model Endpoints
       ├──► A/B Testing
       │
       ▼
┌─────────────┐
│ Monitoring  │ ◄── Evidently AI + Grafana
│  Dashboard  │     Performance & Drift
└─────────────┘
```

## Prerequisites

- Python 3.8+
- Docker Desktop or Docker Engine
- Kubernetes cluster (minikube, kind, or cloud provider)
- MLflow server
- kubectl

## Project Structure

```
ml-production-pipeline/
├── data/                   # Data files (versioned with DVC)
│   ├── raw/
│   ├── processed/
│   └── .dvc/
├── notebooks/              # Jupyter notebooks for exploration
├── src/
│   ├── data/              # Data processing scripts
│   ├── features/          # Feature engineering
│   ├── models/            # Model training code
│   ├── api/               # Model serving API
│   └── monitoring/        # Monitoring scripts
├── pipelines/             # ML pipeline definitions
│   ├── training_pipeline.py
│   └── inference_pipeline.py
├── infrastructure/
│   ├── kubernetes/        # K8s manifests
│   └── docker/            # Dockerfiles
├── .github/
│   └── workflows/
│       └── ml-cicd.yml    # CI/CD for ML
├── mlflow/                # MLflow configuration
├── monitoring/            # Monitoring configs
│   ├── evidently/
│   └── grafana/
└── dvc.yaml              # DVC pipeline definition
```

## Getting Started

### 1. Clone the Repository

```bash
git clone <repository-url>
cd MLOps-Practice-Guide/projects/ml-production-pipeline
```

### 2. Setup Environment

```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt

# Setup DVC
dvc init
dvc remote add -d storage <your-storage-path>
```

### 3. Data Pipeline

```bash
# Pull data
dvc pull

# Run data processing pipeline
dvc repro data/processed
```

### 4. Model Training

```bash
# Start MLflow tracking server
mlflow server --backend-store-uri sqlite:///mlflow.db --default-artifact-root ./mlruns

# Run training pipeline
python pipelines/training_pipeline.py

# View experiments in MLflow UI
# Open http://localhost:5000
```

### 5. Model Serving

```bash
# Build Docker image
docker build -t ml-model-api:latest -f infrastructure/docker/Dockerfile.api .

# Run locally
docker run -p 8000:8000 ml-model-api:latest

# Test API
curl -X POST http://localhost:8000/predict \
  -H "Content-Type: application/json" \
  -d '{"features": [...]}'
```

### 6. Kubernetes Deployment

```bash
# Deploy to Kubernetes
kubectl apply -f infrastructure/kubernetes/

# Check deployment
kubectl get pods
kubectl get services

# Port forward to access API
kubectl port-forward svc/ml-model-api 8000:8000
```

### 7. Monitoring Setup

```bash
# Start monitoring services
docker-compose -f monitoring/docker-compose.yml up -d

# Access Grafana dashboard
# Open http://localhost:3000
```

## Features

- ✅ Data versioning with DVC
- ✅ Experiment tracking with MLflow
- ✅ Automated training pipeline
- ✅ Model registry and versioning
- ✅ RESTful API for model serving
- ✅ Docker containerization
- ✅ Kubernetes deployment
- ✅ Model performance monitoring
- ✅ Data drift detection
- ✅ CI/CD pipeline for ML
- ✅ A/B testing framework

## ML Pipeline Stages

1. **Data Ingestion**: Load data from sources
2. **Data Validation**: Validate data quality
3. **Feature Engineering**: Create features
4. **Model Training**: Train ML model
5. **Model Evaluation**: Evaluate model performance
6. **Model Registry**: Register model version
7. **Model Deployment**: Deploy to serving
8. **Monitoring**: Monitor performance and drift

## Model Monitoring

### Metrics Tracked
- Prediction accuracy
- Prediction latency
- Request volume
- Data drift (feature distributions)
- Model drift (prediction distributions)
- Target drift (if available)

### Access Monitoring Dashboard

```bash
# Evidently AI dashboard
kubectl port-forward svc/evidently-service 8080:8080

# Grafana dashboard
kubectl port-forward svc/grafana 3000:3000
```

## CI/CD Pipeline

The CI/CD pipeline automatically:
1. Runs data validation tests
2. Trains model on new data
3. Evaluates model performance
4. Registers model if metrics meet threshold
5. Deploys model to staging
6. Runs integration tests
7. Promotes to production

## Model Retraining

Automated retraining is triggered by:
- Scheduled intervals (daily/weekly)
- Data drift detection
- Performance degradation
- New data availability

## Cleanup

```bash
# Remove Kubernetes resources
kubectl delete -f infrastructure/kubernetes/

# Stop monitoring services
docker-compose -f monitoring/docker-compose.yml down

# Clean DVC cache
dvc cache dir
```

## Next Steps

- Implement feature store (Feast)
- Add distributed training
- Implement model explainability (SHAP, LIME)
- Add multi-model serving
- Implement canary deployments
- Add automated hyperparameter tuning

## Resources

- [MLflow Documentation](https://mlflow.org/docs/latest/index.html)
- [DVC Documentation](https://dvc.org/doc)
- [Evidently AI Documentation](https://docs.evidentlyai.com/)
- [Kubeflow Documentation](https://www.kubeflow.org/docs/)
