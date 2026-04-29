---
title: "INTELLIGENT MULTI-MODEL ML ORCHESTRATION PLATFORM"
excerpt: "Production-grade MLOps system that uses a Reinforcement Learning agent to dynamically route video frames across three YOLOv8 variants — achieving 58% latency reduction, 2.6× throughput improvement, and 42% cost savings while retaining 95%+ detection accuracy."
collection: portfolio
---

<h1>IE7374 MLOps — Adaptive ML Inference</h1>

A production-grade MLOps system that tackles a critical real-world problem: robots and drones waste 40–50 ms per inference frame using a single heavy ML model, causing "blind spots" during high-speed navigation, while 60–70% of compute is wasted on simple scenes that don't need heavy models.

**GitHub:** [hemanth1403/IE7374-MLOps-Adaptive-ML-Inference](https://github.com/hemanth1403/IE7374-MLOps-Adaptive-ML-Inference)

---

<h2>The Solution</h2>

An intelligent routing system that deploys three YOLOv8 variants (Nano, Small, Large) simultaneously and uses a **Reinforcement Learning (PPO) agent** to dynamically select the optimal model for each video frame in real time — trading off detection accuracy, latency, and cost based on scene complexity.

---

<h2>Key Results</h2>

| Metric | Before | After |
|--------|--------|-------|
| Avg Inference Latency | 48 ms | **20 ms** (−58%) |
| Throughput | 21 FPS | **55 FPS** (2.6×) |
| Cost per 1M Inferences | $200 | **$115** (−42%) |
| GPU Utilization | 95% | **30%** |
| Detection Accuracy | — | **95%+ retained** |

At scale (1,000 robots at 30 FPS): **$72K/month in compute savings**.

---

<h2>System Architecture</h2>

The system is structured across three tiers:

**1. Data Pipeline**
- Downloads and converts COCO 2017 (123K images) to YOLO format
- 8-stage Apache Airflow DAG with DVC caching for reproducibility
- Automated schema validation, anomaly detection, bias detection, and stratified splitting
- 106,411 training / 5,000 validation / 11,876 test images

**2. RL Agent & Model Training**
- Pre-profiles all three YOLO variants on the dataset (latency, confidence, detection counts)
- Trains PPO agent with Behavioral Cloning warm-start to overcome RL value-function collapse
- Observation space: 1028-dim vector (32×32 grayscale + Canny edge density)
- Reward function: quality + efficiency with ranking normalization
- Learned routing behavior:
  - Simple scenes (≤2 objects) → YOLOv8-Nano (~70%)
  - Moderate scenes (3–7 objects) → YOLOv8-Small (~20%)
  - Complex/dense scenes (≥8 objects) → YOLOv8-Large (~10%)

**3. Production Serving & Orchestration**
- FastAPI backend with WebSocket streaming (`/ws/stream`)
- Streamlit dashboard (live camera + video upload)
- MLflow for per-session experiment tracking
- Kubernetes (GKE) deployment with HPA autoscaling, HTTPS ingress, rolling updates
- Prometheus + Grafana monitoring with alert rules
- Drift detection via K8s CronJob (every 6 hours) with automated retraining on decay

---

<h2>Tech Stack</h2>

- **ML / Training:** YOLOv8 (Ultralytics), Stable Baselines3 (PPO), Behavioral Cloning, PyTorch, ONNX Runtime
- **Data Pipeline:** Apache Airflow, DVC, COCO 2017
- **Serving:** FastAPI, WebSocket, Streamlit
- **MLOps:** MLflow, Evidently AI (drift detection), Prometheus, Grafana
- **Infra:** Docker, Kubernetes (GKE), GitHub Actions (6 CI/CD workflows)

---

<h2>Training Journey</h2>

The RL training went through 7 different approaches before arriving at the final solution. The root cause of failure was PPO value-function collapse on this domain. The solution was **Behavioral Cloning** from analytically-derived optimal labels — a supervised learning warm-start that sidesteps pure RL instability. Final BC classifier accuracy: **43.5%** (vs. 33% random baseline).

---

### Submitted By

- **Name:** Hemanth Sai Madadapu
- **Program:** M.S. — Khoury College of Computer Sciences, Northeastern University
- **Contact:**
  - _E-Mail:_ madadapu.h@northeastern.edu
  - _Website:_ [hemanth1403.github.io](https://hemanth1403.github.io)
