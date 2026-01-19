# Intelligent IT Operations Platform

## Project Overview

This project demonstrates a complete AIOps platform that leverages AI/ML to enhance IT operations through intelligent monitoring, anomaly detection, event correlation, automated alerting, and remediation.

## Architecture

```
┌─────────────┐     ┌─────────────┐     ┌─────────────┐
│ Applications│     │Infrastructure│     │   Services  │
│  (Metrics)  │     │   (Logs)    │     │  (Traces)   │
└──────┬──────┘     └──────┬──────┘     └──────┬──────┘
       │                   │                   │
       └───────────────────┼───────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────┐
│      Data Collection Layer              │
│  ┌──────────┐  ┌──────────┐  ┌──────┐ │
│  │Prometheus│  │   Loki   │  │Jaeger│ │
│  └────┬─────┘  └────┬─────┘  └───┬───┘ │
└───────┼─────────────┼────────────┼─────┘
        │             │            │
        └─────────────┼────────────┘
                      │
                      ▼
┌─────────────────────────────────────────┐
│      Data Processing & Storage          │
│  ┌──────────┐  ┌──────────┐           │
│  │TimescaleDB│  │PostgreSQL│           │
│  └────┬─────┘  └────┬─────┘           │
└───────┼─────────────┼──────────────────┘
        │             │
        ▼             ▼
┌─────────────────────────────────────────┐
│         AI/ML Layer                     │
│  ┌──────────────┐  ┌──────────────┐    │
│  │Anomaly      │  │Event         │    │
│  │Detection    │  │Correlation   │    │
│  │(LSTM/IF)    │  │Engine        │    │
│  └──────┬──────┘  └──────┬───────┘    │
│         │                │             │
│  ┌──────▼────────────────▼───────┐    │
│  │   Root Cause Analysis         │    │
│  └───────────────┬───────────────┘    │
└──────────────────┼────────────────────┘
                   │
                   ▼
┌─────────────────────────────────────────┐
│      Automation & Alerting              │
│  ┌──────────┐  ┌──────────┐  ┌──────┐ │
│  │Intelligent│  │Automated │  │Runbook││
│  │Alerting  │  │Remediation│  │Engine ││
│  └────┬─────┘  └────┬─────┘  └───┬───┘ │
└───────┼─────────────┼────────────┼─────┘
        │             │            │
        ▼             ▼            ▼
┌─────────────┐  ┌─────────────┐  ┌─────────────┐
│   Grafana   │  │  Ansible    │  │  Kubernetes │
│  Dashboard  │  │  Playbooks  │  │  Operators  │
└─────────────┘  └─────────────┘  └─────────────┘
```

## Prerequisites

- Python 3.8+
- Docker Desktop or Docker Engine
- Kubernetes cluster (minikube, kind, or cloud provider)
- Prometheus
- Grafana
- kubectl

## Project Structure

```
intelligent-ops-platform/
├── data-collectors/        # Data collection agents
│   ├── prometheus/
│   ├── loki/
│   └── jaeger/
├── ml-models/              # ML models for AIOps
│   ├── anomaly_detection/
│   │   ├── lstm_anomaly.py
│   │   └── isolation_forest.py
│   ├── forecasting/
│   │   └── prophet_forecast.py
│   └── correlation/
│       └── event_correlation.py
├── services/
│   ├── anomaly-service/    # Anomaly detection service
│   ├── correlation-service/# Event correlation service
│   ├── alerting-service/   # Intelligent alerting
│   └── remediation-service/# Automated remediation
├── infrastructure/
│   ├── kubernetes/         # K8s manifests
│   ├── docker/            # Dockerfiles
│   └── terraform/         # Infrastructure as Code
├── dashboards/            # Grafana dashboards
│   ├── anomaly-dashboard.json
│   ├── correlation-dashboard.json
│   └── remediation-dashboard.json
├── automation/
│   ├── ansible/           # Ansible playbooks
│   └── runbooks/          # Automated runbooks
└── monitoring/            # Monitoring configs
    └── prometheus/
```

## Getting Started

### 1. Clone the Repository

```bash
git clone <repository-url>
cd AIOps-Practice-Guide/projects/intelligent-ops-platform
```

### 2. Setup Environment

```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### 3. Start Infrastructure

```bash
# Start Prometheus, Loki, Grafana
docker-compose -f infrastructure/docker-compose.yml up -d

# Verify services
docker ps
```

### 4. Train Anomaly Detection Models

```bash
# Collect sample data (or use provided sample)
python scripts/collect_sample_data.py

# Train LSTM anomaly detection model
python ml-models/anomaly_detection/train_lstm.py

# Train Isolation Forest model
python ml-models/anomaly_detection/train_isolation_forest.py
```

### 5. Deploy Services

```bash
# Build Docker images
docker build -t aiops-anomaly-service:latest -f services/anomaly-service/Dockerfile .
docker build -t aiops-correlation-service:latest -f services/correlation-service/Dockerfile .
docker build -t aiops-alerting-service:latest -f services/alerting-service/Dockerfile .

# Deploy to Kubernetes
kubectl apply -f infrastructure/kubernetes/
```

### 6. Setup Grafana Dashboards

```bash
# Import dashboards
# Access Grafana at http://localhost:3000
# Import dashboards from dashboards/ directory
```

### 7. Configure Data Collection

```bash
# Configure Prometheus to scrape metrics
kubectl apply -f infrastructure/prometheus/

# Configure Loki for log collection
kubectl apply -f infrastructure/loki/
```

## Features

- ✅ Multi-source data collection (metrics, logs, traces)
- ✅ Real-time anomaly detection (LSTM, Isolation Forest)
- ✅ Event correlation and grouping
- ✅ Intelligent alerting (reduces noise)
- ✅ Root cause analysis automation
- ✅ Automated remediation workflows
- ✅ Predictive capacity planning
- ✅ Interactive dashboards
- ✅ Historical analysis and trending

## AIOps Capabilities

### 1. Anomaly Detection

Detects anomalies in:
- CPU/Memory usage
- Network traffic
- Application response times
- Error rates
- Custom business metrics

### 2. Event Correlation

- Groups related events
- Reduces alert noise
- Identifies incident patterns
- Correlates across services

### 3. Intelligent Alerting

- Context-aware alerts
- Alert prioritization
- Reduces false positives
- Smart notification routing

### 4. Automated Remediation

- Auto-scaling based on predictions
- Auto-healing failed services
- Automated rollback on anomalies
- Self-healing infrastructure

### 5. Root Cause Analysis

- Identifies root causes quickly
- Learns from historical incidents
- Provides context and recommendations

## API Endpoints

### Anomaly Detection Service

```bash
# Detect anomalies in metrics
POST /api/v1/anomaly/detect
{
  "metric": "cpu_usage",
  "values": [45, 46, 47, 85, 86, 87],
  "timestamp": "2024-01-01T00:00:00Z"
}

# Get anomaly predictions
GET /api/v1/anomaly/predictions?metric=cpu_usage&hours=24
```

### Correlation Service

```bash
# Correlate events
POST /api/v1/correlation/analyze
{
  "events": [...],
  "time_window": "5m"
}

# Get correlated incidents
GET /api/v1/correlation/incidents?time_range=1h
```

### Alerting Service

```bash
# Create intelligent alert
POST /api/v1/alerting/create
{
  "metric": "error_rate",
  "threshold": "anomaly",
  "severity": "high"
}

# Get alert recommendations
GET /api/v1/alerting/recommendations
```

## Monitoring

Access Grafana dashboards:
```bash
# Port forward Grafana
kubectl port-forward svc/grafana 3000:3000

# Access at http://localhost:3000
# Default credentials: admin/admin
```

## Testing

```bash
# Run unit tests
pytest tests/

# Run integration tests
pytest tests/integration/

# Test anomaly detection
python scripts/test_anomaly_detection.py

# Test event correlation
python scripts/test_correlation.py
```

## Cleanup

```bash
# Remove Kubernetes resources
kubectl delete -f infrastructure/kubernetes/

# Stop Docker services
docker-compose -f infrastructure/docker-compose.yml down

# Remove Docker images
docker rmi aiops-anomaly-service aiops-correlation-service aiops-alerting-service
```

## Next Steps

- Add more ML models (autoencoders, clustering)
- Implement multi-domain correlation
- Add natural language processing for log analysis
- Implement predictive maintenance
- Add integration with ITSM tools (ServiceNow, Jira)
- Implement advanced root cause analysis

## Resources

- [Prometheus Documentation](https://prometheus.io/docs/)
- [Grafana Documentation](https://grafana.com/docs/)
- [Loki Documentation](https://grafana.com/docs/loki/latest/)
- [Prophet Documentation](https://facebook.github.io/prophet/)
