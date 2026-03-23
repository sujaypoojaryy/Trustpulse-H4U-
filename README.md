# TrustPulse 🔐
### Agentic Continuous Identity Verification for Zero Trust Environments

> **HackToFuture 4.0** · T2PS1 · Cybersecurity Theme  
> St Joseph Engineering College, Mangaluru · April 15–17, 2026

---

## The Problem

Traditional authentication (passwords, OTPs, SSO) only verifies identity **once — at login**. After that, every request is trusted until the session expires.

A stolen JWT token grants an attacker **unrestricted lateral movement** across all microservices — often for hours — with zero detection. Existing systems cannot detect:
- Impossible travel (login from India, API call from Germany 10 min later)
- Unusual API access patterns mid-session
- Device fingerprint changes after authentication

## The Solution

TrustPulse is an **agentic identity verification platform** that continuously scores every active session using behavioural AI and an LLM reasoning agent — enforcing Zero Trust throughout the session lifecycle, not just at login.

**Live Demo Scenario:**
1. Normal user session runs (risk score: 12 ✅)
2. Attacker replays stolen token from different country
3. Agent detects impossible travel within **< 2 seconds**
4. Automatically triggers step-up MFA → session freeze → admin alert
5. Everything plays out live on the React dashboard

---

## Architecture

```
[ Keystroke Dynamics ] [ Mouse Patterns ] [ API Call Graph ] [ Device Fingerprint ] [ Geo/IP ]
                                              |
                         +--------------------------------------------+
                         |   BEHAVIOUR ENGINE (Isolation Forest+LSTM) |
                         |   Continuously scores session risk 0-100   |
                         +--------------------------------------------+
                                              | risk score
                         +--------------------------------------------+
                         |   AGENTIC LAYER  (LLM Reasoning Agent)     |
                         |   Interprets anomalies with full context    |
                         +--------------------------------------------+
              |                   |                  |                 |
      [Silent Monitor]     [Step-up MFA]     [Session Freeze]   [Admin Alert]
                                              |
                         +--------------------------------------------+
                         |   REACT DASHBOARD (Human-in-the-Loop)      |
                         |   Live risk feed · Audit trail · Override   |
                         +--------------------------------------------+
```

---

## Tech Stack

| Layer | Technologies |
|---|---|
| **ML / AI** | Python 3.11, scikit-learn (Isolation Forest), PyTorch (LSTM), Claude API |
| **Backend** | FastAPI, WebSockets, JWT, PostgreSQL |
| **Frontend** | React + TypeScript, Recharts, TailwindCSS, shadcn/ui |
| **Infra** | Docker Compose, Kubernetes (simulated), GitHub Actions, Prometheus + Grafana |

---

## Project Structure

```
trustpulse/
├── ml/
│   ├── data_simulator.py        # Generate labelled session data
│   ├── behaviour_engine.py      # Isolation Forest + LSTM model
│   ├── train.py                 # Model training pipeline
│   └── models/                  # Saved model artifacts
├── backend/
│   ├── main.py                  # FastAPI entry point
│   ├── session_manager.py       # JWT session handling
│   ├── risk_scorer.py           # Real-time risk scoring
│   ├── agent.py                 # LLM agentic decision layer
│   ├── dispatcher.py            # Action dispatcher (MFA/freeze/alert)
│   └── websocket_stream.py      # Real-time event stream
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── RiskDashboard.tsx     # Live session risk feed
│   │   │   ├── SessionMap.tsx        # Active sessions map
│   │   │   ├── AgentActionLog.tsx    # Agent decision audit trail
│   │   │   └── OverridePanel.tsx     # Human-in-the-loop controls
│   │   └── App.tsx
├── infra/
│   ├── docker-compose.yml
│   ├── prometheus.yml
│   └── k8s/                     # Kubernetes simulation manifests
├── demo/
│   ├── normal_session.py        # Demo: normal user flow
│   └── attacker_session.py      # Demo: stolen token attack flow
├── .github/
│   └── workflows/
│       └── ci.yml               # GitHub Actions CI pipeline
├── docker-compose.yml
├── requirements.txt
└── README.md
```

---

## Getting Started

### Prerequisites

- Python 3.11+
- Node.js 18+
- Docker + Docker Compose

### Quick Start

```bash
# Clone the repo
git clone https://github.com/YOUR_TEAM/trustpulse.git
cd trustpulse

# Start all services
docker-compose up --build

# Frontend runs at    http://localhost:3000
# Backend API at      http://localhost:8000
# API docs at         http://localhost:8000/docs
```

### Manual Setup (without Docker)

```bash
# Backend
cd backend
pip install -r requirements.txt
uvicorn main:app --reload

# ML training
cd ml
python train.py

# Frontend
cd frontend
npm install
npm run dev
```

### Run the Demo

```bash
# Terminal 1 — start the stack
docker-compose up

# Terminal 2 — simulate normal user
python demo/normal_session.py

# Terminal 3 — simulate attacker with stolen token
python demo/attacker_session.py
```

Watch the React dashboard at `http://localhost:3000` — you will see the risk score spike and the agent trigger a session freeze in real time.

---

## How the Agent Works

The agentic decision layer uses the Claude API to reason over session anomalies with full context:

```python
# Simplified agent decision loop
def evaluate_session(session_data, risk_score, context):
    prompt = f"""
    Session risk score: {risk_score}/100
    Anomaly signals: {context['anomalies']}
    User baseline: {context['baseline']}
    Recent actions: {context['recent_actions']}

    Decide the appropriate response:
    - MONITOR  : risk < 40, anomaly explainable
    - CHALLENGE: risk 40-70, request step-up MFA
    - FREEZE   : risk > 70, impossible travel or device change
    - ALERT    : critical threat, notify admin immediately
    """
    decision = claude_api.reason(prompt)
    return dispatcher.execute(decision)
```

The agent always logs its reasoning to the audit trail — every decision is explainable and can be overridden by an admin (**human-in-the-loop**).

---

## 36-Hour Build Plan

| Phase | Hours | Deliverable |
|---|---|---|
| **Phase 1** | 0–8 | Data simulator + ML model trained + FastAPI backend |
| **Phase 2** | 8–18 | Claude API agent + action dispatcher + WebSocket stream |
| **Phase 3** | 18–28 | React dashboard + full stack integration + Docker Compose |
| **Phase 4** | 28–35 | Demo polish + QA + final submission |

> **Hackathon scope:** ML model runs on simulated session data. Production-grade microservice integration and real user telemetry collection are post-hackathon work.

---

## Why TrustPulse is Different

Unlike existing tools (Azure AD Conditional Access, Okta ThreatInsight) that use **static, rule-based policies**, TrustPulse uses:

- A **continuously learning ML model** that adapts to per-user behavioural baselines
- An **LLM reasoning agent** that interprets anomalies with full session context — not just thresholds
- **Token-level enforcement** across microservices, not just session-level
- A **human-in-the-loop** override layer so admins are always in control

---

## Team

| Name | Role |
|---|---|
| Member 1 (Team Leader) | ML + Agentic Layer |
| Member 2 | Backend + WebSockets |
| Member 3 | Frontend + Dashboard |
| Member 4 | Infra + Integration |

---

## Judging Criteria Alignment

| Criterion | Our Approach |
|---|---|
| **Innovation & Creativity** | Agentic LLM reasoning over live identity — no open-source tool does this |
| **Technical Implementation** | ML model + LLM agent + WebSocket dashboard + JWT enforcement in one stack |
| **Functionality & Feasibility** | Fully demoable with simulated sessions — no live cloud infra required |
| **Impact & Relevance** | Addresses credential theft — the #1 attack vector (94% of breaches, $4.45M avg cost) |
| **Presentation & Demo** | Live risk-score dashboard reacting in real time to a stolen-token attack |

---

## License

MIT License — built for HackToFuture 4.0.
