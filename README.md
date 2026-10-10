# AI-Driven Network Intrusion Detection & Autonomous Response

[![CI Status](https://github.com/ldmduong/AI-Driven-Network-Intrusion-Detection-Autonomous-Response/actions/workflows/ci.yml/badge.svg)](https://github.com/ldmduong/AI-Driven-Network-Intrusion-Detection-Autonomous-Response/actions)
[![Python Version](https://img.shields.io/badge/python-3.10%2B-blue.svg)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.100%2B-009688.svg)](https://fastapi.tiangolo.com/)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.0%2B-ee4c2c.svg)](https://pytorch.org/)
[![Docker](https://img.shields.io/badge/docker-ready-2496ed.svg)](https://www.docker.com/)

A real-time network security framework that combines deep anomaly detection, multi-class attack classification, and reinforcement learning (RL) to execute closed-loop autonomous defense actions on incoming telemetry flows.

---

## Table of Contents

- [Overview](#overview)
- [Key Features](#key-features)
- [System Architecture](#system-architecture)
- [Repository Structure](#repository-structure)
- [Model Artifacts](#model-artifacts)
- [Installation & Setup](#installation--setup)
- [Running the Service](#running-the-service)
- [MLflow Experiment Tracking](#mlflow-experiment-tracking)
- [Docker Deployment](#docker-deployment)
- [Testing](#testing)

---

## Overview

Traditional perimeter defenses often struggle to balance sensitivity and operational overhead. This framework implements a three-tier AI defense pipeline:
1. **Anomaly Detection:** Employs a **PyTorch Autoencoder** to flag anomalous network traffic by calculating reconstruction error against a pre-calibrated baseline threshold.
2. **Threat Classification:** Uses an **XGBoost Classifier** to categorize confirmed anomalies into known attack classes (e.g., DoS, Brute Force, Port Scan).
3. **Autonomous Mitigation:** Evaluates threat context with a **Deep Q-Network (DQN Policy)** agent to select and execute the optimal defensive action with minimal disruption.

---

## Key Features

- **Multi-Stage AI Pipeline:** Joint evaluation using Autoencoder reconstruction error, XGBoost classification probabilities, and DQN policy decisions.
- **Production REST API:** High-throughput telemetry ingestion and inference built with **FastAPI** and **Pydantic** validation.
- **Real-Time Telemetry & Monitoring:** Dedicated **Prometheus** metrics export (`/metrics`) tracking anomaly rates, prediction latencies, and mitigation decisions.
- **Model Versioning:** Experiment and artifact tracking configured for **MLflow**.
- **Automated CI:** Pipeline integration via **GitHub Actions** (`.github/workflows/ci.yml`).

---

## System Architecture

```text
       Incoming Flow Telemetry (JSON Payload)
                         │
                         ▼
             [ FastAPI /app/main.py ]
                         │
                         ▼
          [ Pydantic Schema Validation ]
                         │
                         ▼
        ┌────────────────────────────────┐
        │      app/ml_engine.py          │
        │                                │
        │  1. PyTorch Autoencoder        │ ──> Reconstruction Error > Threshold?
        │  2. XGBoost Multi-Class Model  │ ──> Predicted Attack Signature
        │  3. DQN Mitigation Agent       │ ──> Selected Action:
        │                                │      (PASS | ALERT | RATE_LIMIT | BLOCK_IP)
        └────────────────────────────────┘
                         │
        ┌────────────────┴────────────────┐
        ▼                                 ▼
[ Mitigation Action Result ]   [ Prometheus /metrics ]
