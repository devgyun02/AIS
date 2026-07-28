# AIS - 산업 인프라 위협 탐지 시스템 (Anomaly Intrusion Detection System)

AIS (Anomaly Intrusion Detection System)는 딥러닝 기반의 비지도 학습과 지도 학습 파이프라인을 결합하여 스마트 제조(ICS/OT) 및 항공 관제(ATC) 네트워크 환경에서 발생하는 침입 위협을 실시간으로 탐지하고 제어하는 보안 관제 플랫폼입니다.

---

## 1. 주요 특징 (Key Features)

* **비지도 시계열 이상 탐지 (Unsupervised Anomaly Detection)**
  * Recurrent Autoencoder (LSTM / GRU)를 통해 5 Timestep 시계열 트래픽의 컨텍스트 의존성을 학습하고 비지도 방식으로 이상 패턴 포착
  * Z-score 기반 동적 임계값 조정을 통한 현장 환경 맞춤 제어
* **다중 위협 분류 파이프라인 (Multi-Class Classification)**
  * XGBoost / RandomForest 분류기를 활용하여 Brute Force, DoS, DDoS, Web Attack 등 5가지 위협 유형 자동 다중 분류
* **실시간 관제 & 방화벽 연동 UI (Interactive Dashboard)**
  * Streamlit 기반의 실시간 통합 제어 대시보드
  * 스마트 방화벽 차단 정책(ACL Policy) 자동 생성 및 포트 격리 시뮬레이션
* **학술적/정량적 성능 검증**
  * 20% Hold-out Test Set 기반 AUC-ROC, PR-AUC, Confusion Matrix, KDE 오차 분포 검증 리포트 제공

---

## 2. 데이터셋 다운로드 및 배치 안내 (Dataset Setup)

본 시스템은 Kaggle의 [IDS-Intrusion-CSV (CIC-IDS2018)](https://www.kaggle.com/datasets/solarmainframe/ids-intrusion-csv) 데이터셋을 활용합니다.

### 2.1 다운로드 대상 파일
Kaggle 링크에서 아래 3개 CSV 파일을 다운로드합니다.
* `02-14-2018.csv`
* `02-15-2018.csv`
* `02-16-2018.csv`

### 2.2 Google Drive / 로컬 저장 경로 설정
파이프라인이 데이터를 정상적으로 로드할 수 있도록 Google Drive 내 `/ML/` 경로에 위치시켜 주어야 합니다.

#### 권장 경로 (Google Drive / Colab 환경):
```text
/content/drive/MyDrive/ML/
├── 02-14-2018.csv
├── 02-15-2018.csv
└── 02-16-2018.csv
```

#### 로컬 원본 파일 참고 경로:
```text
C:\Users\0jmg0\OneDrive\바탕 화면\정민균_220161119_기계학습_과제2\02-14-2018.csv
C:\Users\0jmg0\OneDrive\바탕 화면\정민균_220161119_기계학습_과제2\02-15-2018.csv
C:\Users\0jmg0\OneDrive\바탕 화면\정민균_220161119_기계학습_과제2\02-16-2018.csv
```

---

## 3. 실행 방법 (Execution Guide)

### 3.1 Google Colab 환경 패키지 설치
Google Colab 런타임에서 아래 파이프라인 코드를 실행하면 필요한 패키지(`streamlit`, `plotly`, `torch`, `xgboost` 등)를 자동으로 설치합니다.

```python
import os, subprocess, sys

# 패키지 자동 설치
subprocess.check_call([sys.executable, '-m', 'pip', 'install',
                       'streamlit', 'plotly', 'pandas', 'numpy',
                       'scikit-learn', 'xgboost', 'scipy', 'torch', '-q'])
```

### 3.2 외부 터널링 연결 및 Streamlit 관제 앱 실행
Colab에서 Streamlit 앱을 외부 웹 브라우저로 구동하기 위한 가이드입니다.

```bash
# 1. 터널링 도구 설치
npm install -g localtunnel

# 2. Tunnel 접속용 IP 확인 (Password)
curl ipv4.icanhazip.com

# 3. Streamlit 앱 구동 및 터널 연동
streamlit run app.py & npx localtunnel --port 8501
```

---

## 4. 시스템 아키텍처 (System Architecture)

```text
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
```

---

## 5. 연구 성과 및 기능 상세 (Key Achievements)

### 5.1 딥러닝 기반 이상 탐지
* LSTM/GRU Recurrent Autoencoder 구조를 채택하여 5 Timestep 시계열 컨텍스트를 보존.
* 단순 통계 기반 탐지 대비 네트워크 트래픽의 시간적 의존성(Temporal Dependency) 포착 가능.

### 5.2 다중 위협 분류 파이프라인
* XGBoost 기반으로 Brute Force, DDoS, DoS, Web Attack 4종 및 미분류(Other) 위협을 자동 분류.
* 스마트 제조 현장 및 항공 관제 시나리오별 파급 효과 분석 및 권고 대응 조치 제공.

### 5.3 실용적 관제 UI
* Streamlit 기반 실시간 관제 대시보드 구현.
* Z-score 슬라이더, 스마트 방화벽 ACL 자동 배포, KPI 모니터링을 단일 인터페이스에서 통합 운영.

### 5.4 정량적 성능 검증
* AUC-ROC, PR-AUC, F1-Score, Confusion Matrix 등 학술 표준 지표 적용.
* 20% Hold-out Test Set 기준으로 모델 성능 평가 및 투명한 검증 리포트 제공.

---

## 6. 한계점 분석 (Limitations)

### 6.1 학술적 한계점 (Academic Limitations)
1. **단일 공개 데이터셋 의존**: CIC-IDS2018 단일 데이터셋 사용으로 다양한 네트워크 환경(병원, 금융, 공항) 교차 검증(Cross-Dataset Evaluation) 필요.
2. **샘플링 제한**: 파일당 20,000건 샘플링 사용으로 인한 극소수 공격 유형(Botnet 등) 탐지 성능 영향 가능성.
3. **피처 선택 근거 부재**: 칼럼 이름 패턴 매칭으로 5개 피처 선정. 상호정보량(Mutual Information), Lasso 등 정보이론적 최적 피처 집합 검증 미흡.
4. **학습 에포크 및 튜닝 부족**: 10 Epoch 고정 학습(Underfitting 가능성) 및 Grid Search/Bayesian Optimization을 통한 하이퍼파라미터 체계적 탐색 부재.
5. **정규분포 가정의 부적절성**: Z-score 적용 시 복원 오차가 Heavy-tail 분포를 보임에 따라 비파라미터적 임계값 기법 보완 필요.

### 6.2 산업 현장 적용 관점 한계점 (Industrial Application Limitations)
1. **실시간 처리 지연 (Latency)**: PyTorch CPU 추론 기반으로 대용량 패킷 처리 시 지연 발생. 산업 제어망(PLC 제어 주기 < 100ms) 적용을 위한 FPGA/Edge AI 가속 미검토.
2. **OT 프로토콜 미지원**: Modbus, DNP3, EtherNet/IP 등 산업 특화 프로토콜 데이터셋 부재. 일반 IT 네트워크 피처(Flow IAT, Packet Length)가 OT 환경에서 동일한 의미를 갖는다고 보장할 수 없음.
3. **Google Colab 의존 아키텍처**: Google Drive 마운트 및 Colab 런타임 필수 구조로, 산업 현장의 폐쇄망(Air-gap) 환경 구동 불가. 온프레미스 배포 및 엣지 서버 구조로의 전환 필요.
4. **실제 차단 배포 미구현**: 위협 발생 포트를 식별하고 ACL 차단 정책을 화면상에서 제안하는 수준. 실제 네트워크 장비(방화벽/스위치) 연동 로직 미구현.
5. **드리프트(Concept Drift) 대응 미비**: 트래픽 패턴 변화 시 임계값 자동 재조정 및 증분 학습(Incremental Learning) 메커니즘 부재.
6. **보안 감사 추적 미흡**: 탐지 및 차단 이력이 메모리 세션에만 유지되어 재시작 시 소멸. 산업 보안 규정(IEC 62443, NERC CIP) 기준 영속 Audit Trail 미구현.

---

## 7. 추후 연구 방향 (Future Work)

1. **OT 프로토콜 특화 피처 엔지니어링**: Modbus, DNP3, EtherNet/IP 패킷 구조에서 OT 전용 피처(레지스터 주소 접근 빈도, Function Code 분포 등) 추출 및 IT/OT 융합 탐지 모델로 확장.
2. **Transformer 기반 이상 탐지**: Self-Attention 메커니즘 기반 Anomaly Transformer 아키텍처 적용 연구.
3. **연합 학습 (Federated Learning)**: 여러 공장/공항 현장의 민감 트래픽 데이터를 중앙 서버로 수집하지 않고 분산 학습하여 프라이버시 보호 및 도메인 적응 달성.
4. **온프레미스 Edge 배포**: NVIDIA Jetson 또는 산업용 IPC에 ONNX 변환 모델 배포를 통한 폐쇄망 실시간 추론 속도 벤치마크.
5. **Concept Drift 적응형 재학습**: ADWIN, PHT 등 드리프트 감지 알고리즘 통합 및 정기적 자동 재학습 파이프라인 구축.
6. **Human-in-the-Loop 검토 체계**: AI가 탐지한 이상 이벤트를 보안 분석가가 승인 후 ACL 배포하는 SOAR 연동 워크플로 구축 (IEC 62443 준수).
7. **다중 데이터셋 교차 검증**: UNSW-NB15, KDD Cup 99, SCADA 전용 데이터셋으로 일반화 성능 측정 및 도메인 적응 기법 적용.
8. **설명 가능 AI (XAI) 통합**: SHAP 기법을 활용한 XGBoost 분류 근거 시각화 및 의사결정 지원.

---

## 8. 시스템 스펙 요약 (System Specifications)

| 구분 | 명세 (Specifications) |
| :--- | :--- |
| **이상 탐지 모델** | Recurrent (LSTM/GRU) Autoencoder |
| **시퀀스 길이** | 5 Timesteps |
| **은닉 차원** | 16 Hidden Units |
| **다중 분류 모델** | XGBoost (Fallback: RandomForest) |
| **전처리 스케일러** | RobustScaler & Log1p Transformation |
| **평가 데이터** | 20% Hold-out Test Set |
| **임계값 설정** | 복원 오차(MSE) Z-score 기반 다이나믹 임계값 |
