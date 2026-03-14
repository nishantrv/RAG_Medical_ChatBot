# 🎬 ML Recommender System — End-to-End Pipeline

[![Python](https://img.shields.io/badge/Python-3.9+-3776AB?logo=python&logoColor=white)](https://python.org)
[![Flask](https://img.shields.io/badge/Flask-2.x-000000?logo=flask)](https://flask.palletsprojects.com)
[![MLflow](https://img.shields.io/badge/MLflow-Tracking%20%26%20Registry-0194E2?logo=mlflow)](https://mlflow.org)
[![Docker](https://img.shields.io/badge/Docker-Containerised-2496ED?logo=docker&logoColor=white)](https://docker.com)
[![GCP](https://img.shields.io/badge/GCP-Cloud%20Run-4285F4?logo=googlecloud&logoColor=white)](https://cloud.google.com/run)
[![Jenkins](https://img.shields.io/badge/Jenkins-CI%2FCD-D24939?logo=jenkins&logoColor=white)](https://jenkins.io)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A **production-grade, end-to-end machine learning recommender system** covering everything from data ingestion and model training to CI/CD-automated deployment on Google Cloud Run. Built to demonstrate real-world MLOps practices — not just a notebook experiment.

---

## 📑 Table of Contents

- [Why This Project](#-why-this-project)
- [Architecture](#-architecture)
- [ML Models & Algorithms](#-ml-models--algorithms)
- [Tech Stack](#-tech-stack)
- [Project Structure](#-project-structure)
- [Getting Started](#-getting-started)
- [CI/CD Pipeline](#-cicd-pipeline)
- [MLflow Tracking](#-mlflow-tracking)
- [Deployment](#-deployment)
- [Results & Metrics](#-results--metrics)
- [Future Improvements](#-future-improvements)

---

## 🎯 Why This Project

Most ML portfolio projects stop at a Jupyter notebook. This one doesn't. The goal was to build a recommender system the way you'd build one at a real company — with automated pipelines, experiment tracking, containerised deployment, and a serving layer that handles live traffic.

---

## 🏗 Architecture

```
┌──────────────┐     ┌──────────────┐     ┌──────────────────┐
│  Raw Data     │────▶│  Data        │────▶│  Feature         │
│  (CSV/API)    │     │  Validation  │     │  Engineering     │
└──────────────┘     └──────────────┘     └────────┬─────────┘
                                                    │
                                                    ▼
┌──────────────┐     ┌──────────────┐     ┌──────────────────┐
│  Cloud Run    │◀────│  Docker      │◀────│  Model Training  │
│  (Serving)    │     │  Container   │     │  + MLflow Track  │
└──────┬───────┘     └──────────────┘     └──────────────────┘
       │                     ▲
       │              ┌──────┴───────┐
       │              │   Jenkins    │
       └──────────────│   CI/CD     │
                      └──────────────┘
```

**Data Flow:** Raw data → Validation & Cleaning → Feature Engineering → Model Training (with MLflow experiment tracking) → Containerise with Docker → Push to GCP Container Registry → Deploy to Cloud Run via Jenkins pipeline.

---

## 🤖 ML Models & Algorithms

| Component | Model / Technique | Purpose |
|---|---|---|
| **Collaborative Filtering** | User-based & Item-based KNN (cosine similarity) | Recommend items based on similar users' preferences or similar item profiles |
| **Matrix Factorisation** | SVD (Singular Value Decomposition) | Decompose the user-item interaction matrix into latent factors to predict missing ratings |
| **Content-Based Filtering** | TF-IDF + Cosine Similarity | Represent item features as TF-IDF vectors and recommend items with the highest content similarity to a user's past preferences |
| **Hybrid Approach** | Weighted ensemble of collaborative + content-based | Combine multiple signals to improve recommendation quality and handle cold-start scenarios |
| **Evaluation Framework** | RMSE, MAE, Precision@K, Recall@K, Hit Rate | Offline evaluation of recommendation quality across multiple metrics |

### Key ML Libraries

- **scikit-learn** — TF-IDF vectorisation, cosine similarity, preprocessing, train/test splitting
- **Surprise** — Collaborative filtering algorithms (SVD, KNNBasic, KNNWithMeans)
- **pandas / NumPy** — Data manipulation, feature engineering, matrix operations
- **MLflow** — Experiment tracking, model versioning, parameter logging, artifact storage

---

## 🛠 Tech Stack

### ML & Data

| Tool | Role |
|---|---|
| **Python 3.9+** | Core language for data processing, training, and serving |
| **scikit-learn** | Feature extraction (TF-IDF), similarity metrics, preprocessing pipelines |
| **Surprise** | Recommendation algorithms (SVD, KNN variants) |
| **pandas / NumPy** | Data wrangling, feature engineering |
| **MLflow** | Experiment tracking — logs hyperparameters, metrics (RMSE, MAE, Precision@K), and model artifacts per run; model registry for versioning and promotion |

### Application & Serving

| Tool | Role |
|---|---|
| **Flask** | Lightweight REST API serving recommendations via `/recommend` endpoint |
| **Gunicorn** | Production WSGI server for Flask |
| **HTML/CSS (Jinja2)** | Simple frontend for demo interaction |

### Infrastructure & DevOps

| Tool | Role |
|---|---|
| **Docker** | Containerises the full application (model + API + dependencies) for reproducible builds |
| **Google Cloud Platform (GCP)** | Hosting platform — Container Registry for images, Cloud Run for serverless deployment |
| **Cloud Run** | Fully managed serverless container platform — auto-scales from zero, HTTPS out of the box |
| **Jenkins** | CI/CD orchestration — triggers on Git push, runs tests, builds Docker image, pushes to GCR, deploys to Cloud Run |
| **Git / GitHub** | Version control and collaboration |

### How They Connect

```
Developer pushes code
        │
        ▼
   ┌─────────┐    lint, test     ┌───────────┐    docker build    ┌─────────┐
   │ GitHub   │ ──────────────▶  │  Jenkins   │ ────────────────▶ │ Docker  │
   └─────────┘                   └───────────┘                    └────┬────┘
                                                                       │
                                                                  push image
                                                                       │
                                                                       ▼
                                  ┌───────────┐   deploy          ┌─────────────┐
                                  │ Cloud Run  │ ◀─────────────── │ GCP         │
                                  │ (live API) │                  │ Container   │
                                  └───────────┘                  │ Registry    │
                                                                  └─────────────┘
```

---

## 📁 Project Structure

```
ML-Recommender-System_End2End/
├── data/                    # Raw and processed datasets
├── notebooks/               # Exploration and prototyping notebooks
├── src/
│   ├── data_processing/     # Data cleaning, validation, feature engineering
│   ├── models/              # Training scripts for collaborative, content-based, hybrid
│   ├── evaluation/          # Offline evaluation (RMSE, Precision@K, Hit Rate)
│   └── serving/             # Flask app, API routes, model loading
├── templates/               # Jinja2 HTML templates for demo UI
├── mlruns/                  # MLflow experiment tracking artifacts
├── Dockerfile               # Multi-stage container build
├── Jenkinsfile              # CI/CD pipeline definition
├── requirements.txt         # Python dependencies
├── config.yaml              # Hyperparameters and runtime config
└── README.md
```

---

## 🚀 Getting Started

### Prerequisites

- Python 3.9+
- Docker
- GCP account (for Cloud Run deployment)
- Jenkins instance (for CI/CD, optional for local dev)

### Local Development

```bash
# 1. Clone the repo
git clone https://github.com/nishantrv/ML-Recommender-System_End2End_-Jenkins-Docker-GCP-Flask-MLFlow-CloudRun-.git
cd ML-Recommender-System_End2End_-Jenkins-Docker-GCP-Flask-MLFlow-CloudRun-

# 2. Create virtual environment
python -m venv venv
source venv/bin/activate  # Windows: venv\Scripts\activate

# 3. Install dependencies
pip install -r requirements.txt

# 4. Run training (logs to MLflow)
python src/models/train.py

# 5. Launch the Flask app
python src/serving/app.py
# API available at http://localhost:5000
```

### Docker

```bash
docker build -t ml-recommender .
docker run -p 5000:5000 ml-recommender
```

---

## 🔄 CI/CD Pipeline

The Jenkins pipeline automates the full lifecycle on every push to `main`:

```
┌────────────┐   ┌────────────┐   ┌────────────┐   ┌──────────────┐   ┌────────────┐
│  Checkout   │──▶│  Lint &    │──▶│  Docker    │──▶│  Push to GCP │──▶│ Deploy to  │
│  Code       │   │  Test      │   │  Build     │   │  Registry    │   │ Cloud Run  │
└────────────┘   └────────────┘   └────────────┘   └──────────────┘   └────────────┘
```

**Jenkinsfile stages:**
1. **Checkout** — Pull latest code from GitHub
2. **Lint & Test** — Run linting and unit tests
3. **Docker Build** — Build container image with all dependencies + trained model
4. **Push** — Push image to Google Container Registry
5. **Deploy** — Deploy new revision to Cloud Run (zero-downtime)

---

## 📊 MLflow Tracking

Every training run is logged to MLflow with:

- **Parameters** — algorithm type, number of latent factors, learning rate, regularisation, similarity metric
- **Metrics** — RMSE, MAE, Precision@K, Recall@K, Hit Rate
- **Artifacts** — serialised model files, evaluation plots

Launch the MLflow UI to compare experiments:

```bash
mlflow ui --port 5001
# Open http://localhost:5001
```

---

## ☁️ Deployment

The application is deployed as a **serverless container on Google Cloud Run**:

- **Auto-scaling:** Scales from 0 to N instances based on incoming traffic
- **HTTPS:** Managed TLS certificates out of the box
- **Cold start:** ~2-3s for the first request after scale-to-zero
- **Cost:** Pay-per-request — no charges when idle

---

## 📈 Results & Metrics

| Model | RMSE | MAE | Precision@10 |
|---|---|---|---|
| User-based KNN | — | — | — |
| Item-based KNN | — | — | — |
| SVD | — | — | — |
| Content-based (TF-IDF) | — | — | — |
| Hybrid (Weighted) | — | — | — |

> *Fill in your actual metrics from MLflow runs. Showing a comparison table demonstrates you evaluated multiple approaches — a strong signal in portfolio projects.*

---

## 🔮 Future Improvements

- [ ] Add neural collaborative filtering (NCF) using PyTorch
- [ ] Implement A/B testing framework for online evaluation
- [ ] Add real-time feature store (Redis) for user session features
- [ ] Integrate monitoring/alerting for model drift (Evidently AI)
- [ ] Expand to implicit feedback signals (clicks, watch time)

---

## 🤝 Contributing

Contributions and feedback are welcome! Feel free to open an issue or submit a pull request.

---

## 📄 License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

---

**Built by [Nishant Ranjan Verma](https://github.com/nishantrv)** | Cork, Ireland