# ArthaIQ

> **AI-Powered Credit Decision Support Platform for India's MSMEs**\
> Built for **IDBI Innovate 2026**

## Overview

ArthaIQ transforms alternate financial data into an explainable credit
decision package for MSMEs. Instead of only generating a score, it
provides: - Composite Credit Readiness Score - 7 Financial Health
Dimensions - SHAP Explainability - AI Credit Narrative - Credit
Copilot - Actionable Improvement Roadmap

## Problem

Millions of MSMEs lack formal credit history despite generating rich
operational data (GST, bank transactions, UPI, EPFO). ArthaIQ helps
banks assess such businesses using explainable AI.

## Architecture

``` text
React Frontend
      │
Spring Boot Backend
      │
 ├── FastAPI ML Service
 ├── PostgreSQL
 ├── Redis
 └── LLM Provider
```

## Repositories


| Repository | Purpose | Link |
|------------|---------|------|
| **arthaiq-backend** | Spring Boot APIs | https://github.com/ArthaIQ/arthaiq-backend |
| **arthaiq-frontend** | React UI | https://github.com/ArthaIQ/arthaiq-frontend |
| **arthaiq-ml** | FastAPI ML & XGBoost | https://github.com/ArthaIQ/arthaiq-ml |
| **arthaiq-infra** | Docker & Deployment | https://github.com/ArthaIQ/arthaiq-infra |

## Tech Stack

-   Frontend: React, Vite, Tailwind CSS, Chart.js
-   Backend: Spring Boot 3, Java 21, PostgreSQL, Redis
-   AI/ML: FastAPI, XGBoost, SHAP, Pandas
-   DevOps: Docker, Docker Compose

## Key Features

-   Digital Financial Twin
-   Explainable AI with SHAP
-   AI-generated credit narrative
-   Credit Copilot
-   Loan recommendations
-   Provider-agnostic LLM integration

## Repository Structure

``` text
ArthaIQ
├── arthaiq-backend
├── arthaiq-frontend
├── arthaiq-ml
└── arthaiq-infra
```

## Local Setup

Clone each repository and follow its README. Start services using Docker
Compose from the infrastructure repository.

## Team

-   Backend & Integration
-   Frontend
-   ML/AI
