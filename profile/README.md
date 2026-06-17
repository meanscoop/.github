# MeanScoop

MeanScoop discovers what's happening in a city by continuously monitoring Instagram, TikTok, Reddit,
and other local sources, and converts that local demand signal into structured **Events,
Activities, Deals, and Businesses** published to a Next.js/Payload site.

MeanScoop is **demand-routing infrastructure** — an empty feed is a signal to fix the scrapers, never
a reason to seed data.

The repo is a stack of two submodules plus shared infra: **`meanscoop-payload/`** (Next.js 15 +
Payload CMS, the app + ingest worker), **`ai-social-scrapers/`** (python agents + control-plane +
url-extract sidecar), and **`docs/`**.

## Who are you?

Find your role and go straight to your docs.

### I'm on the Revenue Team

You help local businesses get discovered and onboarded — sales, activation, partnerships.

→ **[docs/revenue/](https://github.com/meanscoop/stack/blob/main/docs/revenue/README.md)** — what we sell, pricing & packaging, the sales
playbook, onboarding, and compensation.

### I'm a Developer

You build and maintain the platform.

→ **[docs/engineering/](https://github.com/meanscoop/stack/blob/main/docs/engineering/README.md)** — getting started, architecture, operations,
runbooks, and the runtime/queue contracts. Quick-start: [getting started](https://github.com/meanscoop/stack/blob/main/docs/engineering/getting-started.md).

### I'm a Market Operator

You grow a city by finding sources and feeding the system.

→ **[docs/market-ops/](https://github.com/meanscoop/stack/blob/main/docs/market-ops/README.md)** — source discovery, manifests, the ingestion
guide, coverage/quality, and nightly runs.

Not sure? The full index is [`docs/`](https://github.com/meanscoop/stack/blob/main/docs/README.md).

## The one job

```
Discover → Extract → Score → Review → Promote
```

Everything exists to improve one of those stages. Never seed fake content — an empty feed means fix
the scrapers.

## Run it now

Day-to-day dev runs the whole compose stack from the canonical `docker-compose.dev.yml`:

```bash
./run.sh start      # start everything (Payload :3011, control-plane :8000, Kafka UI :8086)
./run.sh status     # show running services + ports
./run.sh logs payload   # tail one service
```

App-only (no Docker): `cd meanscoop-payload && pnpm dev` → <http://localhost:3011>. Full setup,
per-stack control, and runtime verification live in
[Getting Started](https://github.com/meanscoop/stack/blob/main/docs/engineering/getting-started.md).

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
