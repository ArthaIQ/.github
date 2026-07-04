# ArthaIQ

> **AI-Powered Credit Decision Support Platform for India's MSMEs**\
> Built for **IDBI Innovate 2026 · Problem Statement 3 — Financial Health Score**

<div align="center">

![ArthaIQ Banner](screenshots/banner_placeholder.png)

[![Spring Boot](https://img.shields.io/badge/Spring%20Boot-3.2-6DB33F?style=flat&logo=springboot&logoColor=white)](https://spring.io/projects/spring-boot)
[![React](https://img.shields.io/badge/React-18-61DAFB?style=flat&logo=react&logoColor=black)](https://react.dev)
[![Python](https://img.shields.io/badge/Python-3.11-3776AB?style=flat&logo=python&logoColor=white)](https://python.org)
[![XGBoost](https://img.shields.io/badge/XGBoost-ML-FF6600?style=flat)](https://xgboost.readthedocs.io)
[![Docker](https://img.shields.io/badge/Docker-Compose-2496ED?style=flat&logo=docker&logoColor=white)](https://docker.com)
[![PostgreSQL](https://img.shields.io/badge/PostgreSQL-15-4169E1?style=flat&logo=postgresql&logoColor=white)](https://postgresql.org)

</div>

---

## Table of Contents

- [Overview](#overview)
- [Problem Statement](#problem-statement)
- [Solution](#solution)
- [Innovation Highlights](#innovation-highlights)
- [Architecture](#architecture)
  - [High-Level Architecture](#high-level-architecture)
  - [AI and ML Pipeline](#ai-and-ml-pipeline)
  - [API Flow](#api-flow)
  - [Database Schema](#database-schema)
  - [Deployment Architecture](#deployment-architecture)
  - [LLM Provider Architecture](#llm-provider-architecture)
- [Backend API Reference](#backend-api-reference)
- [Screenshots](#screenshots)
- [Tech Stack](#tech-stack)
- [Repository Structure](#repository-structure)
- [Local Setup](#local-setup)
- [Environment Variables](#environment-variables)
- [Team](#team)

---

## Overview

ArthaIQ transforms alternate financial data (GST, UPI, bank statements, EPFO) into a complete, explainable **Credit Decision Support Package** — giving loan officers everything they need to assess an MSME in under 60 seconds.

Unlike a scoring dashboard, ArthaIQ produces a **decision-ready brief**:

| Output | Description |
|--------|-------------|
| 🎯 **Composite Health Score** | 0–100 score derived from XGBoost ML model |
| 📊 **7 Financial Dimensions** | Liquidity · Growth · Compliance · Cash Flow · Creditworthiness · Digital Adoption · Operational Stability |
| 🔍 **SHAP Explainability** | Per-feature contribution — every score point is traceable |
| 🤖 **AI Credit Narrative** | LLM-generated plain-English credit summary grounded in SHAP values |
| 💬 **Credit Copilot** | Conversational AI answering loan officer questions about the specific business |
| 📋 **Loan Recommendation** | Product type + eligible amount range based on score and cash flow |
| 🗺️ **Improvement Roadmap** | Top 3 actions with estimated score impact and timeframe |

---

## Problem Statement

India has **63 million MSMEs** contributing 30% of GDP. Yet fewer than 20% have access to formal credit.

```
Traditional credit assessment requires:          MSMEs typically have:
─────────────────────────────────────            ──────────────────────────────
✗ 2–3 years of CA-audited financials     →       ✓ GST filing history
✗ CIBIL / bureau credit history          →       ✓ UPI transaction records
✗ Collateral documentation               →       ✓ Bank statement cash flows
✗ Established banking relationship       →       ✓ EPFO compliance records

The data exists. Banks cannot read it intelligently.
```

**The gap is not risk — it is visibility.**

Existing solutions fail because they either:
- Require prior credit history (CIBIL) — NTC businesses score zero by definition
- Use a single alternate data source — misses multi-signal patterns
- Produce a score without a decision-ready explanation — loan officer still guesses
- Are black-box — not defensible to regulators under RBI's explainable AI guidelines

---

## Solution

ArthaIQ builds a **Digital Financial Twin** of every MSME — a continuously-updating, multi-dimensional model of business financial health derived from alternate data, interpreted by AI, and presented as a loan officer's 60-second decision brief.

```
Loan Officer opens ArthaIQ
         │
         ▼
Enters MSME GSTIN
         │
         ▼
ArthaIQ fetches + processes alternate data   ←── GST + UPI + Bank + EPFO
         │
         ▼
XGBoost model scores the business (0–100)
SHAP values computed per feature
         │
         ▼
LLM converts SHAP + metrics → credit narrative + recommendations
         │
         ▼
Financial Health Card rendered in < 15 seconds
         │
         ▼
Loan officer reads brief, asks Credit Copilot questions
         │
         ▼
Makes faster, better-documented credit decision
```

---

## Innovation Highlights

| # | Innovation | Why It Matters |
|---|------------|----------------|
| ① | **Decision support, not just a score** | Output is a decision brief — score + explanation + narrative + recommendations + next steps |
| ② | **SHAP-backed regulatory explainability** | Every score component traceable to a data point. Addresses RBI's explainable AI requirements directly |
| ③ | **Credit Copilot** | Conversational AI grounded in the specific business's data — not generic financial advice |
| ④ | **Multi-source alternate data fusion** | GST + UPI + Bank + EPFO combined reveals signals no single source can |
| ⑤ | **Financial inclusion by design** | NTC/NTB businesses are the primary target — scored even with zero bureau history |
| ⑥ | **Provider-agnostic LLM (Strategy Pattern)** | Free MVP on Ollama → one config change to Amazon Bedrock for the final round |
| ⑦ | **Banking-native architecture** | Spring Boot + PostgreSQL + Docker — ready to integrate with existing bank LOS |

---

## Architecture

### High-Level Architecture

```
┌──────────────────────────────────────────────────────────────────────────┐
│                             CLIENT LAYER                                  │
│                                                                           │
│  ┌──────────────────┐   ┌──────────────────┐   ┌──────────────────────┐  │
│  │   Loan Officer   │   │   MSME Owner     │   │    Risk Manager      │  │
│  │   React Web App  │   │   Self-Service   │   │    Admin View        │  │
│  └────────┬─────────┘   └────────┬─────────┘   └──────────┬───────────┘  │
└───────────┼─────────────────────┼──────────────────────────┼─────────────┘
            └─────────────────────┼──────────────────────────┘
                                  │  HTTPS / REST
┌─────────────────────────────────┼────────────────────────────────────────┐
│                          API GATEWAY LAYER                                │
│                                 │                                         │
│               ┌─────────────────▼──────────────────┐                     │
│               │        Spring Boot API Gateway      │                     │
│               │  JWT Auth · Rate Limiting · Routing │                     │
│               └──────────┬───────────┬─────────────┘                     │
└──────────────────────────┼───────────┼─────────────────────────────────┘
                           │           │
             ┌─────────────┼───────────┼──────────────┐
             │             │           │              │
┌────────────┼─────────────┼───────────┼──────────────┼───────────────────┐
│            ▼    SERVICE LAYER        ▼              ▼                    │
│  ┌──────────────────┐  ┌──────────────────┐  ┌──────────────────────┐   │
│  │  Profile Service │  │ Scoring Service  │  │  Advisory Service    │   │
│  │  MSME onboarding │  │ Orchestration    │  │  Credit Copilot      │   │
│  │  Data ingestion  │  │ Result caching   │  │  Recommendations     │   │
│  │  History track   │  │ Audit logging    │  │  Loan matching       │   │
│  └────────┬─────────┘  └───────┬──────────┘  └──────────┬───────────┘   │
└───────────┼────────────────────┼────────────────────────┼───────────────┘
            └────────────────────┼────────────────────────┘
                                 │
┌────────────────────────────────┼────────────────────────────────────────┐
│                          AI / ML LAYER                                    │
│                                │                                          │
│  ┌─────────────────────────────▼──────┐  ┌──────────────────────────┐   │
│  │        ML Scoring Engine           │  │   LLM Intelligence Layer │   │
│  │  FastAPI · XGBoost · SHAP          │  │   Provider-agnostic      │   │
│  │  Feature engineering (30 features) │  │   Narrative · Copilot    │   │
│  │  7 dimension sub-scores            │  │   Recommendations        │   │
│  └──────────────┬─────────────────────┘  └─────────────┬────────────┘   │
└─────────────────┼──────────────────────────────────────┼────────────────┘
                  └────────────────────┬─────────────────┘
                                       │
┌──────────────────────────────────────┼────────────────────────────────────┐
│                            DATA LAYER│                                     │
│                                      │                                     │
│  ┌───────────────────┐  ┌────────────▼──────┐  ┌──────────────────────┐  │
│  │   PostgreSQL 15   │  │     Redis 7        │  │   Synthetic Data     │  │
│  │   Primary database│  │   Score cache      │  │   MVP demo profiles  │  │
│  │   6 core tables   │  │   TTL 24 hours     │  │   6 MSME profiles    │  │
│  └───────────────────┘  └───────────────────┘  └──────────────────────┘  │
└────────────────────────────────────────────────────────────────────────────┘
```

---

### AI and ML Pipeline

```
DATA SOURCES       FEATURE ENGINEERING      ML MODEL        EXPLAINABILITY
────────────       ───────────────────      ────────        ──────────────

GST Returns  ──┐                            XGBoost   ───► SHAP Values
               │  ┌──────────────────────┐  ─────────       per feature
UPI Trans    ──┼─►│   30 Derived Features│  Score 0-100
               │  │                      │             ──► LLM Prompt
Bank Stmts   ──┤  │ • gst_filing_rate    │  7 Dims          (SHAP+metrics)
               │  │ • revenue_trend      │
EPFO Rec     ──┘  │ • upi_frequency      │  Risk Flags
                  │ • cash_digital_ratio │
                  │ • epfo_compliance    │  CR Tier
                  │ • avg_balance        │
                  │ • inflow_outflow     │  Loan Rec
                  └──────────────────────┘
                                              LLM OUTPUTS
                                              ┌──────────────────────────┐
                                              │ Credit Narrative (120 w) │
                                              │ Recommendations (JSON×3) │
                                              │ Credit Copilot (Q&A)     │
                                              └──────────────────────────┘
```

**7 Financial Health Dimensions:**

| Dimension | Features Used |
|-----------|--------------|
| **Liquidity** | avg_monthly_balance · balance_volatility_cv · cash_buffer_ratio |
| **Growth Trajectory** | gst_revenue_6m_trend_slope · revenue_growth_rate_yoy · monthly_inflow_growth_3m |
| **GST / Tax Compliance** | gst_filing_rate_12m · avg_gst_filing_delay_days · itc_claim_ratio |
| **Cash Flow Stability** | inflow_outflow_ratio · monthly_inflow_variance_cv · negative_balance_days_6m |
| **Creditworthiness** | cheque_bounce_count_6m · overdraft_utilization_rate · emi_obligation_ratio |
| **Digital Adoption** | upi_transaction_share · upi_counterparty_count_unique · digital_payment_growth_rate |
| **Operational Stability** | epfo_contribution_rate · employee_count_trend · business_vintage_months |

---

### API Flow

**Score Generation Flow** (synchronous, ~10–15 seconds):

```
React Frontend        Spring Boot           FastAPI ML          LLM Provider
──────────────        ───────────           ──────────          ────────────
      │                    │                    │                    │
      ├─POST /api/score───►│                    │                    │
      │   {msmeId}         │                    │                    │
      │                    ├─POST /ml/score────►│                    │
      │                    │   {feature vector} │                    │
      │                    │                    ├─XGBoost + SHAP     │
      │                    │                    │   compute          │
      │                    │◄──score + SHAP─────┤                    │
      │                    │   + dimensions     │                    │
      │                    │                    │                    │
      │                    ├─structured prompt──────────────────────►│
      │                    │   (SHAP + metrics)                      │
      │                    │◄──narrative + recommendations───────────┤
      │                    │                                         │
      │                    ├─cache to Redis (TTL 24h)                │
      │                    ├─persist to PostgreSQL                   │
      │                    │                                         │
      │◄──credit package───┤                                         │
      │   score · dims     │                                         │
      │   SHAP · narrative │                                         │
      │   recs · loan match│                                         │
```

**Credit Copilot Flow** (stateless — full context injected every request):

```
React Frontend        Spring Boot           LLM Provider
──────────────        ───────────           ────────────
      │                    │                    │
      ├─POST /api/copilot─►│                    │
      │   {msmeId,         │                    │
      │    message,        ├─load MSME profile  │
      │    history[5]}     │  + latest score    │
      │                    ├─build system prompt│
      │                    │  (full MSME context│
      │                    ├─system+history+───►│
      │                    │   user message     │
      │                    │◄──grounded reply───┤
      │◄──AI response──────┤   (max 80 words,   │
      │                    │    data-grounded)  │
```

---

### Database Schema

```
┌────────────────────────────────────────────────────────────────────────┐
│                         MSME_PROFILES                                   │
│  id UUID PK · gstin · business_name · sector · city                    │
│  owner_name · contact_email · created_at                               │
└──────────────────┬─────────────────────────────────────────────────────┘
                   │ 1:N
       ┌───────────┴──────────────────────────┐
       │                                      │
       ▼ N                                    ▼ N
┌──────────────────────────┐   ┌─────────────────────────────────────────┐
│  FINANCIAL_DATA_SNAPSHOTS│   │            HEALTH_SCORES                │
│  id UUID PK              │   │  id UUID PK                             │
│  msme_id FK              │   │  msme_id FK                             │
│  data_source VARCHAR      │   │  composite_score INT                    │
│  raw_data JSONB           │   │  credit_readiness_tier VARCHAR          │
│  snapshot_date TIMESTAMP  │   │  shap_values JSONB  ◄── 30 features    │
│  status VARCHAR           │   │  ai_narrative TEXT                      │
└──────────────────────────┘   │  loan_recommendation TEXT               │
                               │  scored_at TIMESTAMP                    │
                               └──────────────┬──────────────────────────┘
                                              │ 1:N
                                 ┌────────────┴──────────────┐
                                 │                           │
                                 ▼ N                         ▼ N
                  ┌──────────────────────────┐  ┌───────────────────────┐
                  │     SCORE_DIMENSIONS     │  │    RECOMMENDATIONS    │
                  │  id UUID PK              │  │  id UUID PK           │
                  │  score_id FK             │  │  score_id FK          │
                  │  dimension_name VARCHAR  │  │  rank INT (1/2/3)     │
                  │  dimension_score INT     │  │  action_text TEXT     │
                  │  risk_level VARCHAR      │  │  estimated_impact INT │
                  └──────────────────────────┘  │  timeframe VARCHAR    │
                                                └───────────────────────┘

┌────────────────────────────────────────────────────────────────────────┐
│                           CHAT_HISTORY                                  │
│  id UUID PK · msme_id FK · session_id UUID                             │
│  role VARCHAR · message TEXT · sent_at TIMESTAMP                       │
└────────────────────────────────────────────────────────────────────────┘
```

> **JSONB design decision:** `shap_values` and `raw_data` use PostgreSQL JSONB — avoids 30 rows per score run and allows schema-flexible ingestion as new data sources are added without migrations.

---

### Deployment Architecture

```
                     ┌───────────────────────────┐
                     │     Internet / Browser     │
                     │  Loan Officer · MSME Owner │
                     └─────────────┬─────────────┘
                                   │ HTTPS
                     ┌─────────────▼─────────────┐
                     │   Render / AWS EC2         │
                     │   (single instance, MVP)   │
                     │                            │
                     │  ┌──────────────────────┐  │
                     │  │   Docker Compose      │  │
                     │  │   arthaiq-net         │  │
                     │  │                       │  │
                     │  │  ┌─────────────────┐  │  │
                     │  │  │arthaiq-frontend │  │  │
                     │  │  │React + Nginx    │  │  │
                     │  │  │Port 3000        │  │  │
                     │  │  └────────┬────────┘  │  │
                     │  │           │            │  │
                     │  │  ┌────────▼────────┐  │  │
                     │  │  │ arthaiq-api     │  │  │
                     │  │  │ Spring Boot     │  │  │
                     │  │  │ Port 8080       │  │  │
                     │  │  └───┬─────────┬───┘  │  │
                     │  │      │         │       │  │
                     │  │  ┌───▼───┐ ┌───▼────┐  │  │
                     │  │  │arthaiq│ │arthaiq │  │  │
                     │  │  │ -ml   │ │ -db    │  │  │
                     │  │  │FastAPI│ │Postgres│  │  │
                     │  │  │:8000  │ │:5432   │  │  │
                     │  │  └───┬───┘ └────────┘  │  │
                     │  │      │   ┌──────────┐   │  │
                     │  │      └──►│arthaiq   │   │  │
                     │  │         │-cache     │   │  │
                     │  │         │Redis:6379 │   │  │
                     │  │         └──────────┘   │  │
                     │  │  ┌──────────────────┐  │  │
                     │  │  │ollama (optional) │  │  │
                     │  │  │qwen2.5:7b        │  │  │
                     │  │  │Port 11434        │  │  │
                     │  │  └──────────────────┘  │  │
                     │  └──────────────────────┘  │  │
                     └────────────────┬───────────┘
                                      │ HTTPS
                     ┌────────────────▼───────────┐
                     │  OpenAI-compat / Bedrock    │
                     │  External LLM (optional)    │
                     └────────────────────────────┘
```

**One-command startup:**

```bash
# With local Ollama LLM (recommended for MVP)
docker-compose --profile ollama up --build

# Template fallback — no LLM, works fully offline
docker-compose up --build

# Final round on AWS with Bedrock
LLM_PROVIDER=bedrock docker-compose up --build
```

---

### LLM Provider Architecture

ArthaIQ uses the **Strategy Pattern** — the `LlmProvider` interface decouples all business logic from the underlying model. Prompt templates, API contracts, and service code are identical across all environments.

```
          Spring Boot
     (LlmService / CopilotService)
                │
                ▼
   ┌────────────────────────────┐
   │      LlmProvider           │
   │      (interface)           │
   │  generateNarrative(prompt) │
   │  chat(system,history,msg)  │
   └────┬──────────┬────────────┘
        │          │            │
        ▼          ▼            ▼
┌────────────┐ ┌──────────┐ ┌──────────────────┐
│  Ollama    │ │ Template │ │ Amazon Bedrock    │
│  Provider  │ │ Fallback │ │ Provider         │
│            │ │          │ │                  │
│ Local      │ │ Offline  │ │ AWS managed      │
│ Free       │ │ Zero deps│ │ IAM auth (no key)│
│ qwen2.5:7b │ │ Handlebars│ │ Claude / Titan   │
│ llama3.2   │ │ templates│ │ Mistral / Nova   │
└────────────┘ └──────────┘ └──────────────────┘
 MVP default   Demo safety   Final round (AWS)
 (free tier)   net           no code changes
```

**What changes between phases — what stays the same:**

| Component | MVP (Ollama) | Offline | Final Round (Bedrock) |
|-----------|-------------|---------|----------------------|
| `LlmProvider` interface | ✅ Unchanged | ✅ Unchanged | ✅ Unchanged |
| Prompt templates | ✅ Unchanged | ✅ Unchanged | ✅ Unchanged |
| Business logic | ✅ Unchanged | ✅ Unchanged | ✅ Unchanged |
| Frontend | ✅ Unchanged | ✅ Unchanged | ✅ Unchanged |
| Provider class | `OllamaProvider` | `TemplateFallback` | `BedrockProvider` |
| `LLM_PROVIDER` | `ollama` | `template` | `bedrock` |
| `LLM_MODEL` | `qwen2.5:7b` | *(ignored)* | `amazon.titan-text-express-v1` |
| Auth | Not needed | Not needed | IAM role on instance |

---

## Backend API Reference

**Base URL:** `http://localhost:8080`

All responses use the envelope format:

```json
{
  "success": true,
  "data": {},
  "error": null,
  "timestamp": "2026-01-15T10:30:00Z"
}
```

Error format:

```json
{
  "success": false,
  "data": null,
  "error": {
    "code": "SCORE_GENERATION_FAILED",
    "message": "ML service unavailable. Please try again.",
    "retryable": true
  }
}
```

---

### `POST /api/auth/login`

Authenticate user and receive JWT token.

```json
// Request
{ "username": "officer.demo", "password": "demo123" }

// Response
{
  "data": {
    "token": "eyJhbGci...",
    "role": "LOAN_OFFICER",
    "name": "Rakesh Iyer",
    "expiresAt": "2026-01-16T10:30:00Z"
  }
}
```

> All subsequent requests require `Authorization: Bearer <token>`

---

### `GET /api/msme`

List MSME profiles. Query params: `page`, `size`, `sector`, `city`.

### `GET /api/msme/{id}`

Get single MSME profile with data source summary.

### `POST /api/msme`

Create new MSME profile.

```json
{
  "gstin": "24AABCS1429B1Z1",
  "businessName": "Sabir Textiles",
  "sector": "Textile",
  "city": "Surat",
  "ownerName": "Sabir Rashid",
  "contactEmail": "rashid@Sabirtextiles.com"
}
```

### `GET /api/msme/demo`

Return all 6 pre-loaded synthetic demo profiles with latest scores. No auth required when `DEMO_MODE=true`.

---

### `POST /api/score/{msmeId}`

Trigger full score generation pipeline. Checks Redis cache first (TTL 24h).

```json
// Response data
{
  "scoreId": "7c9e6679-...",
  "compositeScore": 62,
  "creditReadinessTier": "Developing",
  "dimensions": {
    "liquidity": 58,
    "growthTrajectory": 71,
    "gstCompliance": 65,
    "cashFlowStability": 55,
    "creditworthiness": 60,
    "digitalAdoption": 48,
    "operationalStability": 72
  },
  "shapValues": {
    "gst_filing_rate_12m": 8.2,
    "revenue_growth_rate_yoy": 6.1,
    "upi_transaction_share": -9.3,
    "cheque_bounce_count_6m": -5.8,
    "epfo_contribution_rate": -3.2,
    "avg_monthly_balance": 4.7,
    "inflow_outflow_ratio": 3.1
  },
  "riskFlags": [
    { "flag": "High cash dependency — 65% revenue via cash", "severity": "high" },
    { "flag": "Irregular EPFO contributions (3 months missed)", "severity": "medium" },
    { "flag": "No existing credit history", "severity": "medium" }
  ],
  "aiNarrative": "Sabir Textiles shows consistent GST compliance over 9 of the last 12 months and a positive revenue trend of 15% year-on-year, indicating a growing business. Key strengths include strong inflow-to-outflow ratios and a stable employee base of 8 registered workers. Primary concerns are high cash dependency (65% of revenue outside digital channels) and irregular EPFO contributions. The loan officer should probe cash handling practices and seasonal workforce patterns.",
  "recommendations": [
    {
      "rank": 1,
      "action": "Increase digital payment share from 35% to at least 50% of monthly revenue",
      "rationale": "Digital adoption is the single largest negative SHAP driver",
      "estimatedImpact": "6–8 points",
      "timeframe": "60 days"
    },
    {
      "rank": 2,
      "action": "File GST returns on time for 3 consecutive months",
      "rationale": "Eliminates the compliance gap, improves GST dimension score",
      "estimatedImpact": "4–6 points",
      "timeframe": "90 days"
    },
    {
      "rank": 3,
      "action": "Regularise EPFO contributions for all enrolled employees",
      "rationale": "Consistent EPFO signals operational stability to the model",
      "estimatedImpact": "3–4 points",
      "timeframe": "60 days"
    }
  ],
  "loanRecommendation": {
    "product": "Working Capital Loan",
    "minAmount": 800000,
    "maxAmount": 1500000,
    "currency": "INR",
    "rationale": "Based on avg monthly inflow ₹6.8L and current score tier"
  },
  "cachedResult": false,
  "processingTimeMs": 12400
}
```

### `GET /api/score/{msmeId}/latest`

Return the most recent score without triggering a new computation.

### `GET /api/score/{msmeId}/history`

Return historical score runs for trend chart.

```json
{
  "data": [
    { "scoredAt": "2026-01-15", "compositeScore": 62, "tier": "Developing" },
    { "scoredAt": "2025-12-15", "compositeScore": 57, "tier": "Developing" },
    { "scoredAt": "2025-11-15", "compositeScore": 54, "tier": "Not Ready" }
  ]
}
```

---

### `POST /api/copilot/chat`

Send a Credit Copilot message. Stateless — include conversation history with each request.

```json
// Request
{
  "msmeId": "3fa85f64-...",
  "message": "What is the biggest repayment risk for this business?",
  "history": [
    { "role": "user", "content": "Summarise this business for me" },
    { "role": "assistant", "content": "Sabir Textiles is a growing textile trader..." }
  ]
}

// Response
{
  "data": {
    "response": "The primary repayment risk is cash flow irregularity — 65% of revenue flows through cash channels, making monthly inflows harder to verify and more volatile. Three missed EPFO contributions also suggest periodic liquidity stress. The loan officer should request 6 months of bank statements and probe cash handling practices before approval.",
    "timestamp": "2026-01-15T10:35:22Z"
  }
}
```

> The Copilot is **data-grounded** — it will not speculate beyond the specific MSME's data and explicitly deflects approval/rejection recommendations.

---

### `POST /ml/score` *(Internal — FastAPI only)*

> Called by Spring Boot only. Not exposed to frontend.

```json
// Request
{
  "msme_id": "3fa85f64-...",
  "features": {
    "avg_monthly_balance": 280000,
    "balance_volatility_cv": 0.34,
    "gst_filing_rate_12m": 0.75,
    "upi_transaction_share": 0.35,
    "revenue_growth_rate_yoy": 0.15,
    "cheque_bounce_count_6m": 2,
    "epfo_contribution_rate": 0.75,
    "business_vintage_months": 48
  }
}

// Response
{
  "composite_score": 62,
  "dimensions": {
    "liquidity": 58, "growth_trajectory": 71, "gst_compliance": 65,
    "cash_flow_stability": 55, "creditworthiness": 60,
    "digital_adoption": 48, "operational_stability": 72
  },
  "shap_values": { "gst_filing_rate_12m": 8.2, "upi_transaction_share": -9.3 },
  "risk_flags": [{ "flag": "High cash dependency", "severity": "high" }],
  "credit_readiness_tier": "Developing",
  "loan_recommendation": { "product": "Working Capital Loan", "min_amount": 800000, "max_amount": 1500000 }
}
```

---

## Screenshots

> 📸 Replace each placeholder with an actual screenshot after building the UI. Recommended tool: browser fullscreen → `Cmd+Shift+4` (macOS) or Snipping Tool (Windows). Save to `screenshots/` in this repo.

### 01 — Login Page

![Login Page](screenshots/01_login.png)

*ArthaIQ login screen. Navy branding. Branch credentials. Redirects to dashboard on success.*

---

### 02 — Dashboard — MSME List

![Dashboard](screenshots/02_dashboard.png)

*Paginated list of MSME profiles with sector badge, city, composite score, and credit readiness tier. Searchable and filterable.*

---

### 03 — Financial Health Card — Score and Radar

![Health Card Score](screenshots/03_health_card_score.png)

*Score gauge (arc, color-coded: red / amber / teal / green). Credit Readiness badge. 7-dimension radar chart. Above-the-fold view.*

---

### 04 — Financial Health Card — SHAP Waterfall

![SHAP Waterfall](screenshots/04_shap_waterfall.png)

*Horizontal bar chart. Green bars = positive contributors (push score up). Red bars = negative contributors (push score down). Sorted by absolute SHAP value. Regulatory explainability layer.*

---

### 05 — Financial Health Card — AI Narrative and Risk Flags

![AI Narrative](screenshots/05_ai_narrative.png)

*LLM-generated credit narrative (3 paragraphs, 120 words max, grounded in SHAP values). Risk flags with color-coded severity indicators.*

---

### 06 — Financial Health Card — Improvement Roadmap

![Recommendations](screenshots/06_recommendations.png)

*Three improvement recommendation cards. Each: specific action + estimated score impact badge + timeframe pill. Rendered directly from structured LLM JSON output.*

---

### 07 — Credit Copilot

![Credit Copilot](screenshots/07_credit_copilot.png)

*Conversational AI chat. User messages right-aligned (navy). AI responses left-aligned (gray) with ArthaIQ avatar. Typing indicator while waiting. Answers grounded in specific MSME data.*

---

### 08 — Loan Recommendation Panel

![Loan Recommendation](screenshots/08_loan_recommendation.png)

*Recommended loan product (e.g. Working Capital Loan). Eligible amount range (e.g. ₹8L – ₹15L). Rationale text derived from score tier and cash flow analysis.*

---

### 09 — Contrast View — High-Score Profile (Priya Foods)

![High Score Profile](screenshots/09_high_score_profile.png)

*Priya Foods Pvt Ltd, Pune — score 81/100, Strong Profile tier. Demonstrates the platform's range across credit quality tiers.*

---

## Tech Stack

| Layer | Technology | Version | Purpose |
|-------|-----------|---------|---------|
| **Frontend** | React | 18 | UI framework |
| | Vite | 5 | Build tooling |
| | Tailwind CSS | 3 | Utility-first styling |
| | Chart.js + react-chartjs-2 | 4 | Radar + waterfall charts |
| | Zustand | 4 | Global state (msmeProfile, scoreResult slices) |
| | Axios | 1.6 | HTTP client with JWT interceptor |
| **Backend** | Spring Boot | 3.2 | API gateway + orchestrator |
| | Java | 21 | Runtime |
| | Spring Security + JJWT | — | JWT authentication + RBAC |
| | Spring Data JPA | — | ORM + repositories |
| | Flyway | — | Database schema migrations |
| **AI / ML** | FastAPI | 0.110 | ML service API |
| | Python | 3.11 | ML runtime |
| | XGBoost | 2.0 | Credit scoring model |
| | SHAP | 0.44 | Explainability (TreeExplainer — 10× faster than generic) |
| | Pandas + NumPy | — | Feature engineering |
| | Pydantic | 2 | Request/response validation |
| **LLM** | Ollama + qwen2.5:7b | — | MVP — local, free, no key |
| | TemplateFallbackProvider | — | Offline safety net |
| | Amazon Bedrock | — | Final round (AWS infra, IAM auth) |
| **Data** | PostgreSQL | 15 | Primary database (ACID, JSONB) |
| | Redis | 7 | Score result cache (TTL 24h) |
| **DevOps** | Docker | 24+ | Containerisation |
| | Docker Compose | 2.x | Local + cloud orchestration |
| | Render / AWS EC2 | — | Cloud deployment target |
| | Nginx | — | Static frontend serving |

---

## Repository Structure


```
ArthaIQ/
│
├── arthaiq-backend/              ← Member 1 — Spring Boot
│   ├── Dockerfile
│   └── src/main/java/com/arthaiq/
│       ├── controller/
│       │   ├── AuthController.java
│       │   ├── MsmeController.java
│       │   ├── ScoreController.java
│       │   └── CopilotController.java
│       ├── service/
│       │   ├── MsmeService.java
│       │   ├── ScoringService.java       ← calls FastAPI /ml/score
│       │   ├── LlmService.java           ← calls LlmProvider interface
│       │   └── CopilotService.java
│       ├── llm/
│       │   ├── LlmProvider.java          ← interface (never changes)
│       │   ├── OllamaProvider.java       ← MVP default (free)
│       │   ├── TemplateFallbackProvider.java  ← offline safety net
│       │   └── BedrockProvider.java      ← final round (AWS)
│       ├── repository/
│       ├── model/                        ← JPA entities (6 tables)
│       ├── dto/                          ← request/response objects
│       ├── security/                     ← JWT filter + config
│       └── config/                       ← CORS, OpenAPI docs
│
├── arthaiq-frontend/             ← Member 2 — React + Tailwind
│   ├── Dockerfile
│   └── src/
│       ├── pages/
│       │   ├── LoginPage.jsx
│       │   ├── DashboardPage.jsx
│       │   ├── HealthCardPage.jsx        ← demo centrepiece
│       │   └── CopilotPage.jsx
│       ├── components/
│       │   ├── ScoreGauge.jsx            ← arc gauge 0–100, color-coded
│       │   ├── DimensionRadar.jsx        ← Chart.js radar, 7 axes
│       │   ├── ShapWaterfall.jsx         ← horizontal bar, green/red
│       │   ├── CreditReadinessBadge.jsx  ← 4-tier colored pill
│       │   ├── RiskFlagList.jsx          ← severity-coded flags
│       │   ├── RecommendationCards.jsx   ← 3 cards, impact + timeframe
│       │   ├── LoanRecommendation.jsx    ← product + amount + rationale
│       │   └── CopilotChat.jsx           ← message bubbles + input
│       ├── api/                          ← Axios API client functions
│       ├── hooks/                        ← useScore, useCopilot
│       └── store/                        ← Zustand slices
│
├── arthaiq-ml/                   ← Member 3 — Python FastAPI
│   ├── Dockerfile
│   ├── main.py                   ← FastAPI app entry point
│   ├── routers/scoring.py        ← POST /ml/score endpoint
│   ├── ml/
│   │   ├── feature_engineering.py   ← 30 derived features
│   │   ├── model.py                 ← XGBoost train + predict + save
│   │   ├── shap_explainer.py        ← TreeExplainer + top-N extraction
│   │   └── scoring_dimensions.py    ← 7 weighted dimension calculators
│   ├── data/
│   │   ├── generate_synthetic.py    ← 500-profile synthetic generator
│   │   └── synthetic_profiles.json  ← 6 demo profiles (pre-built)
│   ├── prompts/
│   │   ├── narrative_prompt.txt     ← score narrative template
│   │   └── copilot_system_prompt.txt← Copilot grounding template
│   └── requirements.txt
│
└── arthaiq-infra/                ← DevOps — shared
    ├── docker-compose.yml
    ├── docker-compose.prod.yml
    ├── .env.example
    └── nginx/nginx.conf
```

## Repositories
| Repository | Purpose | Link |
|------------|---------|------|
| **arthaiq-backend** | Spring Boot APIs | https://github.com/ArthaIQ/arthaiq-backend |
| **arthaiq-frontend** | React UI | https://github.com/ArthaIQ/arthaiq-frontend |
| **arthaiq-ml** | FastAPI ML & XGBoost | https://github.com/ArthaIQ/arthaiq-ml |
| **arthaiq-infra** | Docker & Deployment | https://github.com/ArthaIQ/arthaiq-infra |

---

## Local Setup

### Prerequisites

```bash
docker --version        # 24+
docker compose version  # 2.x
ollama --version        # 0.2+ (optional — for local LLM)
```

### Step 1 — Clone all repositories

```bash
mkdir arthaiq && cd arthaiq
git clone https://github.com/ArthaIQ/arthaiq-backend
git clone https://github.com/ArthaIQ/arthaiq-frontend
git clone https://github.com/ArthaIQ/arthaiq-ml
git clone https://github.com/ArthaIQ/arthaiq-infra
```

### Step 2 — Configure environment

```bash
cd arthaiq-infra
cp .env.example .env
# Edit .env — set JWT_SECRET and DB_PASSWORD at minimum
```

### Step 3 — Pull Ollama model (first time only, ~5 GB)

```bash
ollama pull qwen2.5:7b

# Skip this step if using LLM_PROVIDER=template (offline mode)
```

### Step 4 — Start all services

```bash
# With local Ollama LLM (recommended)
docker-compose --profile ollama up --build

# Offline / no LLM dependency
docker-compose up --build
```

**Expected startup output:**

```
arthaiq-db       | database system is ready to accept connections
arthaiq-cache    | Ready to accept connections
arthaiq-ml       | Uvicorn running on http://0.0.0.0:8000
arthaiq-api      | Started ArthaIqApplication in 4.2 seconds
arthaiq-frontend | nginx started
```

### Step 5 — Verify

```bash
curl http://localhost:8080/actuator/health   # → {"status":"UP"}
curl http://localhost:8000/health            # → {"status":"ok"}
open http://localhost:3000                   # React UI
```

### Demo credentials

| Username | Password | Role |
|----------|----------|------|
| `officer.demo` | `demo123` | `LOAN_OFFICER` |
| `manager.demo` | `demo123` | `RISK_MANAGER` |
| `admin.demo` | `demo123` | `ADMIN` |

### Demo MSME profiles

| Profile | Sector | City | Score | Tier |
|---------|--------|------|-------|------|
| Sabir Textiles | Textile | Surat | ~62 | Developing |
| Priya Foods Pvt Ltd | Food Processing | Pune | ~81 | Strong |
| Rajesh Pharma Dist | Pharma | Mumbai | ~71 | Credit Ready |
| Surat Retail Co | Retail | Surat | ~40 | Not Ready |
| Chennai Mfg Works | Manufacturing | Chennai | ~74 | Credit Ready |
| Kavita Textiles | Textile | Ahmedabad | ~54 | Developing |

---

## Environment Variables

```bash
# ── LLM Provider (only section that changes between phases) ───────────────
LLM_PROVIDER=ollama           # ollama | template | bedrock
LLM_BASE_URL=http://ollama:11434/v1
LLM_MODEL=qwen2.5:7b
LLM_API_KEY=                  # blank for Ollama and Bedrock
LLM_MAX_TOKENS=1000
LLM_TEMPERATURE=0.3           # low = consistent, factual output

# Phase reference:
# MVP:          LLM_PROVIDER=ollama    LLM_MODEL=qwen2.5:7b
# Offline:      LLM_PROVIDER=template
# Final/AWS:    LLM_PROVIDER=bedrock   LLM_MODEL=amazon.titan-text-express-v1

# ── Database ──────────────────────────────────────────────────────────────
DB_HOST=db
DB_PORT=5432
DB_NAME=arthaiq
DB_USER=arthaiq
DB_PASSWORD=changeme_in_production

# ── Cache ─────────────────────────────────────────────────────────────────
REDIS_HOST=cache
REDIS_PORT=6379
REDIS_TTL_SECONDS=86400       # 24 hours

# ── Spring Boot ───────────────────────────────────────────────────────────
JWT_SECRET=your-256-bit-secret-here
JWT_EXPIRY_HOURS=24
ML_SERVICE_URL=http://ml:8000
CORS_ALLOWED_ORIGINS=http://localhost:3000

# ── ML Service ────────────────────────────────────────────────────────────
MODEL_PATH=/app/models/arthaiq_xgb.pkl
SYNTHETIC_DATA_PATH=/app/data/synthetic_profiles.json

# ── Demo ──────────────────────────────────────────────────────────────────
DEMO_MODE=true
DEMO_PROFILES_PRELOAD=true
```

---

## Team

| Member | Role | Responsibilities |
|--------|------|-----------------|
| **Member 1** | Backend Engineer | Spring Boot · REST APIs · JWT · PostgreSQL · Redis · Docker · Cloud deployment |
| **Member 2** | Frontend Engineer | React · Tailwind CSS · Health Card · Credit Copilot UI · Chart.js visualisations |
| **Member 3** | ML / AI Engineer | XGBoost · SHAP · FastAPI · Synthetic data generation · Prompt engineering · LLM integration |

---

<div align="center">

**ArthaIQ** · IDBI Innovate 2026 · Problem Statement 3

*63 million MSMEs. The data is there. The intelligence is here.*

</div>
