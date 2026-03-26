# MeanScoop

> Local discovery platform — events, businesses, deals, and community content powered by AI ingestion

## How the platform works

MeanScoop discovers and enriches local content from social media and public sources, runs it through AI extraction and quality scoring, and surfaces it to users through a mobile app. Businesses get tools for promotions, loyalty programs, and analytics.

```
  Social sources          AI pipeline            Platform             Users
┌──────────────┐    ┌──────────────────┐    ┌──────────────┐    ┌───────────┐
│  Instagram   │    │  Discovery       │    │  Payload CMS │    │  Mobile   │
│  Reddit      │───▶│  LLM Extraction  │───▶│  API + Admin │───▶│  App      │
│  TikTok      │    │  Embeddings      │    │  Kafka pub   │    │  (Expo)   │
│  Web         │    │  Quality scoring │    │              │    │           │
└──────────────┘    └──────────────────┘    └──────────────┘    └───────────┘
                           ▲                       ▲
                           │                       │
                      CLI tooling         Shared infra + docs
```

## Navigating the repos

Start with whatever matches what you're working on:

### [`meanscoop-payload`](https://github.com/meanscoop/meanscoop-payload) — Backend API + CMS

The core of the platform. Built on **Payload 3 + Next.js**. Defines all data models (businesses, events, deals, reviews, posts, users), handles auth, serves the REST/GraphQL API, and provides the admin panel. Includes its own Docker Compose setup for local development. If you're adding a feature that touches data or business logic, start here.

**Key areas:** Collections (data models), hooks, access control, email/SMS queues, content moderation, local Docker infra

---

### [`ai-social-scrapers`](https://github.com/meanscoop/ai-social-scrapers) — Content ingestion pipeline

**Python.** Discovers content from Instagram, Reddit, TikTok, and web sources, then runs it through a multi-stage pipeline: LLM extraction (Ollama/Llama 3.2), embedding generation, geocoding, and quality scoring. Has a control plane for job routing and dead-letter handling, and a publisher service that deduplicates and pushes enriched content to the platform via Kafka. Includes its own Docker Compose setup for running agents and workers locally.

**Key areas:** Platform-specific agents (`instagram-agent`, `reddit-activity-agent`, `tiktok-agent`), workers (embedder, geocoder, LLM extractor, quality scorer), publisher, control plane, local Docker infra

---

### [`meanscoop-app`](https://github.com/meanscoop/meanscoop-app) — Mobile app

**React Native (Expo)** for iOS and Android. Consumes the Payload API. This is what users interact with — browsing local events, businesses, deals, and community content.

**Key areas:** Screens, API client, theme, providers

---

### [`meanscoop-cli`](https://github.com/meanscoop/meanscoop-cli) — CLI tooling

**Go.** Command-line tool for managing the platform — interacting with agents, running tasks, and managing Docker services.

**Key areas:** `internal/agents`, `internal/tasks`, `internal/docker`

---

### [`stack`](https://github.com/meanscoop/stack) — Shared infrastructure + docs

Shared deployment configs, Helm charts, Skaffold profiles, and cross-repo documentation. This is **not** where you run services locally — each service repo (`meanscoop-payload`, `ai-social-scrapers`) has its own Docker Compose for local dev. Stack is for shared infra concerns like Kafka cluster config, Langfuse observability, and production deployment.

**Key areas:** Helm charts, Skaffold profiles, shared Docker Compose (Kafka, Langfuse, datalake), deployment scripts, cross-repo docs

## Tech stack

| Layer | Tech |
|---|---|
| Mobile | React Native, Expo |
| Backend / CMS | Payload 3, Next.js |
| Ingestion | Python, Ollama, Kafka |
| CLI | Go |
| Infra | Docker Compose, Skaffold, Helm |
| Data | PostgreSQL, Redis, Kafka |
| LLM | Llama 3.2 (local via Ollama) |
| Observability | Langfuse |
| Orchestration | App-owned queue/workers, DB-backed state |
