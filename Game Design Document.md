# Game Design Document (GDD)

# Project: Nusantara AI Nation Simulator

## High Concept

“AI-powered real-time nation simulator where the player becomes President of Nusantara and manages economy, politics, society, and global crises synchronized with real-world events.”

Game ini adalah:

* AI simulation sandbox
* geopolitical simulator
* economy simulator
* livestream content engine
* AI multi-agent experiment

Target utama:

* livestream entertainment
* emergent AI drama
* political strategy gameplay
* real-world synchronized simulation

---

# 1. Vision

Membuat simulasi negara hidup dimana:

* semua NPC penting adalah AI agents
* dunia berubah berdasarkan berita nyata
* ekonomi mengikuti kondisi global
* kabinet dapat berdebat otomatis
* rakyat punya opini dinamis
* pemain menjadi presiden yang harus menjaga stabilitas negara

---

# 2. Core Fantasy

Pemain merasakan:

* tekanan sebagai presiden
* menghadapi krisis ekonomi
* mengelola kabinet AI
* menangani media dan opini publik
* mengambil keputusan sulit
* menyelamatkan negara dari chaos

---

# 3. Genre

Primary:

* Political Simulation
* AI Sandbox
* Strategy Simulation

Secondary:

* Real-time Management
* Emergent Narrative
* AI Society Simulation

---

# 4. Platform

Initial:

* Web Application

Future:

* Desktop App
* Steam Release

---

# 5. Target Audience

## Primary

* penonton livestream
* penggemar simulasi politik
* AI enthusiasts
* geopolitics fans
* economy simulation fans

## Secondary

* edukasi ekonomi/politik
* konten kreator
* AI experimentation community

---

# 6. Unique Selling Point (USP)

## 1. AI Cabinet Agents

Semua menteri adalah AI hidup.

## 2. Real-World Synchronization

Berita dan ekonomi dunia nyata mempengaruhi simulasi.

## 3. Emergent Drama

AI dapat:

* berbohong
* manipulatif
* korup
* konflik internal
* membentuk aliansi

## 4. Livestream Ready

Dirancang untuk konten live streaming.

---

# 7. Game Pillars

## A. Dynamic AI Politics

AI agent punya agenda dan opini sendiri.

## B. Real-Time Crisis Management

Krisis ekonomi/politik berjalan real-time.

## C. Public Sentiment Warfare

Media dan opini publik sangat penting.

## D. Emergent Storytelling

Cerita muncul alami dari simulasi.

---

# 8. Core Gameplay Loop

```text id="gddloop1"
Receive Crisis/Event
        ↓
Cabinet AI Discussion
        ↓
Player Decision
        ↓
Simulation Updates
        ↓
Public Reaction
        ↓
Political Consequences
        ↓
New Crisis/Event
```

---

# 9. Main Systems

# 9.1 Economic System

## Variables

### Currency

* USD/RP exchange rate

### Inflation

* food inflation
* fuel inflation

### Employment

* unemployment rate

### Trade

* imports
* exports

### National Finance

* APBN
* debt
* reserves

### Investment

* investor confidence

---

## Economic Events

* FED interest rate hikes
* oil price spikes
* supply chain collapse
* sanctions
* recession
* crypto crash

---

# 9.2 Political System

## Variables

* public approval
* cabinet loyalty
* military loyalty
* corruption
* opposition power
* regional stability

---

## Political Events

* protests
* impeachment
* corruption scandal
* separatism
* riots

---

# 9.3 Public Opinion System

Rakyat dibagi menjadi:

* lower class
* middle class
* upper class
* students
* business owners
* nationalists
* activists

Setiap kelompok:

* punya kebutuhan
* punya ideology
* punya anger level

---

# 9.4 Media System

## Media Types

* government media
* opposition media
* independent media
* social media influencers

---

## Media Effects

* narrative shaping
* propaganda
* panic creation
* public trust shifts

---

# 9.5 AI Cabinet System

## Initial AI Agents

### Core Cabinet

* Minister of Finance
* Minister of Defense
* Minister of Social Affairs
* Minister of Economy
* Central Bank Governor
* Intelligence Chief

---

## AI Agent Attributes

### Personality

* aggressive
* populist
* technocrat
* nationalist
* corrupt

### Internal Stats

* loyalty
* ambition
* stress
* trust
* influence

### Hidden Traits

* foreign ties
* corruption risk
* political agenda

---

# 10. AI Architecture

# 10.1 AI Stack

## Local AI

Initial:

* Ollama

Models:

* Llama 3
* Qwen
* DeepSeek
* Gemma

---

## Cloud AI

Future:

* OpenRouter

Possible Models:

* Claude
* GPT
* DeepSeek
* Gemini

---

# 10.2 Agent Framework

Recommended:

* LangGraph

Why:

* stateful agents
* multi-agent orchestration
* memory graph support

Alternative:

* CrewAI
* AutoGen

---

# 10.3 Memory System

## Short-term Memory

Current events.

## Long-term Memory

Political history.

## Relationship Memory

Agent-to-agent relationships.

---

# 11. World Event Engine

## Real Data Sources

### Economy APIs

* USD exchange
* oil prices
* gold prices
* stock market

### News APIs

* RSS feeds
* geopolitical news
* economic news

### Social Trends

* X/Twitter trends
* YouTube trends

---

## Event Pipeline

```text id="eventpipe1"
Real World Data
      ↓
Event Parser
      ↓
Classification Engine
      ↓
Simulation Impact Engine
      ↓
AI Reaction System
```

---

# 12. Livestream System

# 12.1 Stream Overlay

Widgets:

* approval rating
* USD/RP
* inflation
* protests
* breaking news

---

# 12.2 Viewer Interaction

Viewers can:

* vote policy
* support opposition
* trigger events
* donate influence

---

# 13. UI/UX Direction

# Frontend Framework

## Astro

Website-first architecture.

Reason:

* fast
* content oriented
* lightweight
* SEO friendly

---

# Design Direction

Reference:
Astro
and
Blueprint

---

# Visual Style

## Aesthetic

* premium intelligence dashboard
* modern geopolitical war-room
* minimalist cyber-government
* Bloomberg + military command center

---

## Typography

Recommended:

* Inter
* Space Grotesk
* Satoshi

---

## Color Palette

### Primary

* deep navy
* dark graphite

### Accent

* electric blue
* emerald
* alert red

---

## Layout

### Presidential Dashboard

Sections:

* national status
* crisis alerts
* cabinet AI feed
* economic graphs
* public opinion
* world map

---

# 14. Technical Architecture

# Frontend

## Stack

* Astro
* TailwindCSS
* TypeScript

## UI

* glassmorphism
* animated dashboards
* realtime widgets

---

# Backend

## Stack

* Python
* FastAPI

---

## Services

### Simulation Service

Runs national simulation.

### AI Agent Service

Handles AI cabinet.

### Event Service

Processes real-world data.

### WebSocket Gateway

Realtime frontend sync.

---

# Database

## PostgreSQL

Persistent simulation state.

## Redis

Realtime cache/events.

## Vector DB

AI memory storage.

Recommended:

* Qdrant

---

# 15. Suggested Folder Structure

```text id="folderstruct1"
frontend/
 ├── astro-app/
 ├── components/
 ├── layouts/
 ├── pages/
 └── websocket/

backend/
 ├── api/
 ├── simulation/
 ├── ai_agents/
 ├── economy/
 ├── events/
 ├── memory/
 ├── websocket/
 └── database/
```

---

# 16. MVP Scope (IMPORTANT)

Jangan mulai terlalu besar.

---

# MVP FEATURES

## Must Have

### Economy

* USD/RP
* inflation
* approval rating

### AI Cabinet

* 5 ministers

### Events

* manual + RSS news

### Dashboard

* realtime graphs

### AI Discussion

* cabinet chat

---

## NOT YET

Jangan dulu:

* war system
* elections
* 3D graphics
* full city simulation
* advanced military combat

---

# 17. Development Phases

# Phase 1 — Prototype

Goal:
AI cabinet + economy simulation.

---

# Phase 2 — Playable Sandbox

Goal:
livestream-ready dashboard.

---

# Phase 3 — Real-World Sync

Goal:
news and economy integration.

---

# Phase 4 — Advanced AI Politics

Goal:
betrayal, corruption, deep memory.

---

# Phase 5 — Massive Simulation

Goal:
fully autonomous nation AI.

---

# 18. Recommended Initial AI Roles

Start with ONLY:

1. President (Player)
2. Finance Minister
3. Social Minister
4. Central Bank Governor
5. Intelligence Chief
6. Media AI

Sudah cukup untuk gameplay awal.

---

# 19. Success Metric

## Technical

* stable realtime simulation
* believable AI interaction

## Gameplay

* emergent drama
* meaningful decisions

## Content

* entertaining livestream moments

---

# 20. Long-Term Vision

Menjadi:

* AI civilization simulator
* political sandbox platform
* interactive livestream nation
* autonomous AI governance experiment

---

# Recommended Immediate Next Step

Kerjakan urutan ini:

## STEP 1

Astro dashboard UI

## STEP 2

Python FastAPI backend

## STEP 3

WebSocket realtime

## STEP 4

Economic simulation

## STEP 5

Ollama AI cabinet

## STEP 6

Cabinet live discussion

## STEP 7

Real-world news integration
