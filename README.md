# ✈️ Voyage Analytics: Production MLOps in Travel

[![Python](https://img.shields.io/badge/Python-3.10%2B-3776AB?style=for-the-badge&logo=python&logoColor=white)](https://python.org)
[![Docker](https://img.shields.io/badge/Docker-Containerized-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![Jenkins](https://img.shields.io/badge/Jenkins-CI%2FCD-D24939?style=for-the-badge&logo=jenkins&logoColor=white)](https://www.jenkins.io/)
[![MLflow](https://img.shields.io/badge/MLflow-Experiment%20Tracking-0194E2?style=for-the-badge&logo=mlflow&logoColor=white)](https://mlflow.org/)
[![Streamlit](https://img.shields.io/badge/Streamlit-Interactive%20UI-FF4B4B?style=for-the-badge&logo=streamlit&logoColor=white)](https://streamlit.io/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](LICENSE)

An enterprise-grade, end-to-end Machine Learning Operations (MLOps) pipeline built for travel analytics and dynamic flight pricing. Combines automated CI/CD deployment, dual predictive models (flight price regression + customer propensity classification), centralized experiment tracking with MLflow, and microservice orchestration with Docker.

---

## 📌 Table of Contents
- [Executive Overview](#-executive-overview)
- [System Architecture](#-system-architecture)
- [Key Features](#-key-features)
- [Data Pipeline & Features](#-data-pipeline--features)
- [Machine Learning Models](#-machine-learning-models)
- [MLOps & CI/CD Infrastructure](#-mlops--cicd-infrastructure)
- [Repository Structure](#-repository-structure)
- [Installation & Getting Started](#-installation--getting-started)
- [Running with Docker](#-running-with-docker)
- [Author & Connect](#-author--connect)

---

## 🚀 Executive Overview
In modern airline and hospitality industries, pricing models and customer targeting require real-time inference, reproducibility, and automated release cycles. 

**Voyage Analytics** bridges raw travel data to containerized production services:
- **Flight Price Prediction:** Gradient Boosting Regressor predicting flight fares based on routes, agencies, travel distances, and temporal demand.
- **Customer Segmentation:** Gradient Boosting Classifier identifying high-propensity passenger groups with SMOTE class imbalance resolution.
- **Experiment Tracking:** Centralized MLflow SQLite backend (`mlflow.db`) logging parameters, metrics (RMSE, MAE, R², F1), and model artifacts (`.joblib`).
- **DevOps Lifecycle:** Automated Jenkins pipelines, multi-container Docker Compose definitions, and interactive Streamlit web dashboard.

---

## 🏗️ System Architecture

```text
[ Raw Datasets: flights.csv | hotels.csv | users.csv ]
                       │
                       ▼
┌────────────────────────────────────────────────────────┐
│             Data Preprocessing & Feature Eng           │
│   • LabelEncoding routes & agencies                    │
│   • Temporal extraction (Month, DayOfWeek, Quarter)    │
│   • Distance ratios: price_per_km, time_dist           │
│   • SMOTE resampling on demographic targets            │
└────────────────────────────────────────────────────────┘
                       │
       ┌───────────────┴───────────────┐
       ▼                               ▼
┌─────────────────────────┐ ┌─────────────────────────┐
│ Flight Price Regressor  │ │ Passenger Classifier    │
│ (Gradient Boosting)     │ │ (Gradient Boosting)     │
└─────────────────────────┘ └─────────────────────────┘
       │                               │
       └───────────────┬───────────────┘
                       ▼
┌────────────────────────────────────────────────────────┐
│                   MLflow Tracking Server               │
│  Logs runs, hyperparameters, metrics & joblib models   │
└────────────────────────────────────────────────────────┘
                       │
       ┌───────────────┴───────────────┐
       ▼                               ▼
┌─────────────────────────┐ ┌─────────────────────────┐
│     Flask REST API      │ │   Streamlit Dashboard   │
│  Port 5001 / Microserve │ │   Interactive Forecasts │
└─────────────────────────┘ └─────────────────────────┘
                       │
                       ▼
┌────────────────────────────────────────────────────────┐
│           Docker & Jenkins Automation (CI/CD)          │
│   Builds, tests, and deploys images via Jenkinsfile    │
└────────────────────────────────────────────────────────┘
```

---

## 🎯 Key Features
- **Production-Ready Models:** Serialized model artifacts (`flight_price_model.joblib`, `gender_clf_model.joblib`) with pre-fitted `StandardScaler` and `LabelEncoder` dictionaries.
- **Centralized Metrics Logging:** MLflow tracking engine capturing model lineage, training runs, and hyperparameter drift.
- **RESTful Serving:** Lightweight Flask API service configured with health checks and prediction endpoints on port `5001`.
- **Interactive UI:** Streamlit application offering flight route selection, agency selection, distance inputs, and instant price predictions.
- **Reproducible Containers:** Multi-stage `Dockerfile` (Python 3.10+) and `docker-compose.yaml` for one-command local orchestrations.
- **CI/CD Automation:** Declarative `Jenkinsfile` automating checkout, docker build, and deployment validation.

---

## 📊 Data Pipeline & Features
The pipeline processes three relational travel tables:
1. `flights.csv`: Historical flight transactions (from, to, flightType, agency, date, price, time, distance).
2. `hotels.csv`: Hotel stays, booking duration, daily rates.
3. `users.csv`: Traveler demographics (age, company, gender).

### Engineered Features
- **Temporal Indicators:** `month`, `dayofweek`, `quarter` extracted from transaction timestamps.
- **Efficiency Metrics:** `price_per_km` ($price / distance$) and `time_dist` ($time / distance$) isolating velocity and route premium.
- **Encoded Categoricals:** `flightType_enc`, `agency_enc`, `from_enc`, `to_enc`.
- **Imbalance Handling:** Synthetic Minority Over-sampling Technique (SMOTE) applied to demographic classifications.

---

## 🧠 Machine Learning Models

| Model Component | Algorithm | Objective | Key Metrics |
|---|---|---|---|
| **Flight Price Model** | `GradientBoostingRegressor` | Predict ticket fare in USD | R²: 0.88+, low RMSE |
| **Customer Classifier** | `GradientBoostingClassifier` | Predict traveler propensity | Precision & F1 > 0.84 |
| **Feature Scaling** | `StandardScaler` | Normalizes distance & duration | Zero mean, Unit variance |
| **Categorical Pipeline** | `LabelEncoder` | Encodes airports, agencies, classes | Deterministic lookup dict |

---

## 📁 Repository Structure
```text
├── Capstone_Project_7_Productionization_of_ML_Systems.ipynb  # Full research, EDA & modeling notebook
├── Dockerfile                                                 # Production Docker container definition
├── Jenkinsfile                                                # Automated Jenkins CI/CD pipeline script
├── docker-compose.yaml                                        # Multi-service composition file
├── deployment.yml                                             # Deployment manifest
├── requirements.txt                                           # Python dependencies
├── app.py                                                     # Flask REST microservice
├── streamlit_app.py                                           # Streamlit dashboard interface
├── train_models.py                                            # Automated batch model training script
├── mlflow_tracking.py                                         # MLflow logging and experiment tracking
├── mlflow.db                                                  # Local SQLite experiment database
├── flight_price_model.joblib                                  # Serialized regressor artifact
├── gender_clf_model.joblib                                    # Serialized classifier artifact
├── feature_scaler.joblib                                      # Serialized scaler
├── label_encoders.joblib                                      # Encoded categorical mappings
└── travel_dataset/                                            # Raw CSV datasets (flights, hotels, users)
```

---

## 🛠️ Installation & Getting Started

### 1. Clone the Repository
```bash
git clone https://github.com/abhik99/Voyage-Analytics-Integrating-MLOps-in-Travel.git
cd Voyage-Analytics-Integrating-MLOps-in-Travel
```

### 2. Create Virtual Environment & Install Dependencies
```bash
python -m venv venv
# On Windows
venv\Scripts\activate
# On Linux/macOS
source venv/bin/activate

pip install -r requirements.txt
```

### 3. Retrain Models & Log to MLflow
```bash
python train_models.py
```

### 4. Launch MLflow UI
```bash
mlflow ui --backend-store-uri sqlite:///mlflow.db
# Open http://localhost:5000 in your browser
```

### 5. Run the Streamlit Dashboard
```bash
streamlit run streamlit_app.py
```

---

## 🐳 Running with Docker

### Build and Run with Docker Compose
```bash
# Build container image
docker build -t voyage-analytics:latest .

# Run container on port 5001
docker run -p 5001:5001 voyage-analytics:latest
```

---

## 👨‍💻 Author & Connect

**Abhishek Kumar (Abhi)**  
*Data Scientist & Analytics Engineer*

- 🌐 **Portfolio:** [abhicodes.online](https://abhicodes.online)
- 💼 **LinkedIn:** [linkedin.com/in/abhik99](https://linkedin.com/in/abhik99)
- 🐙 **GitHub:** [@abhik99](https://github.com/abhik99)
- 📧 **Email:** [akdevil27boy@gmail.com](mailto:akdevil27boy@gmail.com)

---
*Distributed under the MIT License. Contributions and feedback are welcome!*
