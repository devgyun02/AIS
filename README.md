# 🛡️ AIS — 산업 인프라 위협 탐지 시스템 (Anomaly Intrusion Detection System)

**AIS (Anomaly Intrusion Detection System)**는 딥러닝 기반의 비지도 학습과 지도 학습 파이프라인을 결합하여 스마트 제조(ICS/OT) 및 항공 관제(ATC) 네트워크 환경에서 발생하는 침입 위협을 실시간으로 탐지하고 제어하는 보안 관제 플랫폼입니다.

---

## 📌 주요 특징 (Key Features)

* **비지도 시계열 이상 탐지 (Unsupervised Anomaly Detection)**
  * Recurrent Autoencoder (`LSTM` / `GRU`)를 통해 5 Timestep 시계열 트래픽의 컨텍스트 의존성을 학습하고 비지도 방식으로 이상 패턴 포착
  * Z-score 기반 동적 임계값 조정을 통한 현장 환경 맞춤 제어
* **다중 위협 분류 파이프라인 (Multi-Class Classification)**
  * `XGBoost` / `RandomForest` 분류기를 활용하여 Brute Force, DoS, DDoS, Web Attack 등 5가지 위협 유형 자동 다중 분류
* **실시간 관제 & 방화벽 연동 UI (Interactive Dashboard)**
  * `Streamlit` 기반의 실시간 통합 제어 대시보드
  * 스마트 방화벽 차단 정책(ACL Policy) 자동 생성 및 포트 격리 시뮬레이션
* **학술적/정량적 성능 검증**
  * 20% Hold-out Test Set 기반 AUC-ROC, PR-AUC, Confusion Matrix, KDE 오차 분포 검증 리포트 제공

---

## 📂 데이터셋 다운로드 및 배치 안내 (Dataset Setup)

본 시스템은 Kaggle의 **[IDS-Intrusion-CSV (CIC-IDS2018)](https://www.kaggle.com/datasets/solarmainframe/ids-intrusion-csv)** 데이터셋을 활용합니다.

### 1. 다운로드 대상 파일
Kaggle 링크에서 아래 **3개 CSV 파일**을 다운로드해 주세요.
* `02-14-2018.csv`
* `02-15-2018.csv`
* `02-16-2018.csv`

### 2. Google Drive / 로컬 저장 경로 설정
파이프라인이 데이터를 정상적으로 로드할 수 있도록 Google Drive 내 `/ML/` 경로에 위치시켜 주어야 합니다.

#### 📍 권장 경로 (Google Drive / Colab 환경):
```text
/content/drive/MyDrive/ML/
├── 02-14-2018.csv
├── 02-15-2018.csv
└── 02-16-2018.csv
📍 로컬 원본 파일 참고 경로:
Plaintext
C:\Users\0jmg0\OneDrive\바탕 화면\정민균_220161119_기계학습_과제2\02-14-2018.csv
C:\Users\0jmg0\OneDrive\바탕 화면\정민균_220161119_기계학습_과제2\02-15-2018.csv
C:\Users\0jmg0\OneDrive\바탕 화면\정민균_220161119_기계학습_과제2\02-16-2018.csv
🚀 실행 방법 (Execution Guide)
1. Google Colab 환경 실행
Google Colab 런타임에서 아래 파이프라인 코드를 실행하면 필요한 패키지(streamlit, plotly, torch, xgboost 등)를 자동으로 설치하고 Streamlit 앱을 구동합니다.

Python
import os, subprocess, sys

# 패키지 자동 설치 및 구동
subprocess.check_call([sys.executable, '-m', 'pip', 'install',
                       'streamlit', 'plotly', 'pandas', 'numpy',
                       'scikit-learn', 'xgboost', 'scipy', 'torch', '-q'])
2. Streamlit 관제 앱 실행
Bash
streamlit run app.py
🏗️ 시스템 아키텍처 (System Architecture)
[ CIC-IDS2018 Data ] (02-14, 02-15, 02-16)
         │
         ▼
[ Preprocessing & Scaling ] ── RobustScaler, Log1p, 5-Timestep Sequencing
         │
         ├───► [ Recurrent Autoencoder ] (LSTM/GRU) ──► Anomaly Score (MSE) & Z-score Thresholding
         │
         └───► [ Multi-class Classifier ] (XGBoost) ──► Attack Type Classification
                                                             │
                                                             ▼
                                                [ AIS Interactive Dashboard ]
                                                • Real-time Monitoring
                                                • Automated ACL Firewalls
                                                • Evaluation Reports
⚠️ 한계점 및 향후 과제 (Limitations & Future Work)
1. 학술적 한계점
단일 공개 데이터셋 의존: CIC-IDS2018 단일 데이터셋 사용으로 타 교차 데이터셋(Cross-Dataset)에 대한 일반화 성능 검증 필요

샘플링 제한: 파일당 20,000건 샘플링 사용으로 인한 극소수 공격 유형 탐지 왜곡 가능성

Z-score 정규분포 가정: 복원 오차가 Heavy-tail 분포를 보임에 따라 비파라미터적 임계값 기법 보완 필요

에포크 및 튜닝: 10 Epoch 고정 학습 및 하이퍼파라미터(Grid/Bayesian Search) 미적용

2. 산업 현장 적용 관점 한계점
실시간 처리 지연: PyTorch CPU 추론 기반으로 PLC 제어 주기(< 100ms) 충족을 위한 FPGA/Edge AI 가속 미검토

OT 프로토콜 미지원: Modbus, DNP3, EtherNet/IP 등 산업 특화 프로토콜 피처 부재

차단 연동 및 감사 추적: 실제 방화벽/스위치 장비 hardware-level 차단 배포 연동 및 IEC 62443/NERC CIP 영속 Audit Trail 미구현
