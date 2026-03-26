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
                      CLI tooling            Stack (infra)
```

## Navigating the repos

Start with whatever matches what you're working on:

### [`meanscoop-payload`](https://github.com/meanscoop/meanscoop-payload) — Backend API + CMS

The core of the platform. Built on **Payload 3 + Next.js**. Defines all data models (businesses, events, deals, reviews, posts, users), handles auth, serves the REST/GraphQL API, and provides the admin panel. If you're adding a feature that touches data or business logic, start here.

**Key areas:** Collections (data models), hooks, access control, email/SMS queues, content moderation

---

### [`ai-social-scrapers`](https://github.com/meanscoop/ai-social-scrapers) — Content ingestion pipeline

**Python.** Discovers content from Instagram, Reddit, TikTok, and web sources, then runs it through a multi-stage pipeline: LLM extraction (Ollama/Llama 3.2), embedding generation, geocoding, and quality scoring. Has a control plane for job routing and dead-letter handling, and a publisher service that deduplicates and pushes enriched content to the platform via Kafka.

**Key areas:** Platform-specific agents (`instagram-agent`, `reddit-activity-agent`, `tiktok-agent`), workers (embedder, geocoder, LLM extractor, quality scorer), publisher, control plane

---

### [`meanscoop-app`](https://github.com/meanscoop/meanscoop-app) — Mobile app

**React Native (Expo)** for iOS and Android. Consumes the Payload API. This is what users interact with — browsing local events, businesses, deals, and community content.

**Key areas:** Screens, API client, theme, providers

---

### [`meanscoop-cli`](https://github.com/meanscoop/meanscoop-cli) — CLI tooling

**Go.** Command-line tool for managing the platform — interacting with agents, running tasks, managing Docker services, and orchestrating Flyte workflows.

**Key areas:** `internal/agents`, `internal/tasks`, `internal/flyte`, `internal/docker`

---

### [`stack`](https://github.com/meanscoop/stack) — Infrastructure

Docker Compose configs and Helm charts for running the full platform locally or deploying it. Separate compose files for each concern: Payload, Kafka, agents, datalake, Langfuse (LLM observability), and Flyte (workflow orchestration).

**Key areas:** `docker/docker-compose.*.yml`, `skaffold.yaml`, deployment scripts

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
| Orchestration | Flyte |
