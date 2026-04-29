# MVP PRD — Nusantara AI Nation Simulator
**Document Type:** Product Requirements Document (MVP Scope)  
**Version:** 1.1 (JSON Storage — no DB engine)  
**Target Reader:** AI Agent (Antigravity)  
**Source:** Game Design Document (GDD) v1  
**Status:** Ready for Implementation

---

## 0. Agent Instruction Primer

Dokumen ini adalah **machine-readable PRD** untuk AI agent Antigravity. Setiap section menggunakan format terstruktur: `WHAT → WHY → ACCEPTANCE CRITERIA`. Prioritas ditulis eksplisit. Tidak ada ambiguitas yang disengaja.

Gunakan dokumen ini sebagai **ground truth** untuk semua keputusan build MVP.

---

## 1. Product Overview

| Field | Value |
|---|---|
| Product Name | Nusantara AI Nation Simulator |
| MVP Codename | `nusantara-mvp-v1` |
| Platform | Web Application |
| Primary Use Case | AI-powered geopolitical simulation + livestream content |
| Core Loop | Player (President) menerima krisis → Kabinet AI berdebat → Player memutuskan → Simulasi update → Reaksi publik |

---

## 2. MVP Goal Statement

**Build the minimum system that can:**
1. Menjalankan simulasi ekonomi dasar (USD/RP, inflasi, approval rating) secara real-time
2. Menampilkan 5 AI cabinet ministers yang bisa berdiskusi secara otomatis
3. Menerima manual event input dan RSS news sebagai trigger
4. Menampilkan dashboard real-time yang layak di-livestream

**NOT a goal for MVP:**
- War system
- Election system
- 3D graphics
- Full city simulation
- Advanced military combat
- Full real-world data sync (ini Phase 3)

---

## 3. User Stories (MVP Scope Only)

### US-01 — Presidential Dashboard
```
AS a player (President),
I WANT to see a real-time dashboard with national stats,
SO THAT I can monitor the state of the nation at a glance.
```
**Acceptance Criteria:**
- Dashboard menampilkan: USD/RP rate, inflation %, approval rating %, active crisis alerts
- Data refresh interval: ≤ 5 detik via WebSocket
- Dashboard visible saat livestream (OBS-friendly layout)

---

### US-02 — Economic Simulation
```
AS a player,
I WANT the economy to simulate dynamically,
SO THAT my decisions have measurable consequences.
```
**Acceptance Criteria:**
- Variabel aktif: `usd_rp_rate`, `food_inflation`, `fuel_inflation`, `unemployment_rate`, `approval_rating`
- Setiap keputusan player mengubah minimal 1 variabel ekonomi
- Variabel tidak boleh freeze; harus ada decay/growth logic saat idle
- Nilai variabel tersimpan persisten di database

---

### US-03 — AI Cabinet Discussion
```
AS a player,
I WANT my cabinet ministers (AI agents) to automatically discuss a crisis,
SO THAT I get multiple perspectives before making a decision.
```
**Acceptance Criteria:**
- 5 AI ministers aktif: Finance Minister, Social Minister, Central Bank Governor, Intelligence Chief, Media AI
- Saat event/crisis trigger, semua menteri menghasilkan response dalam ≤ 30 detik
- Setiap menteri memiliki persona konsisten (lihat section 6)
- Dialog menteri tampil sebagai live feed di dashboard
- Menteri bisa tidak setuju satu sama lain (conflict logic required)

---

### US-04 — Crisis / Event System
```
AS a player,
I WANT to receive crisis events (manual or from RSS),
SO THAT there is always something to react to.
```
**Acceptance Criteria:**
- Admin/player dapat trigger manual event via UI
- Sistem dapat parse 1+ RSS feed dan mengklasifikasikan berita sebagai event
- Event memiliki tipe: `economic`, `political`, `social`, `international`
- Event ditampilkan sebagai "Breaking News" di dashboard overlay

---

### US-05 — Player Decision
```
AS a player,
I WANT to respond to a crisis with a decision,
SO THAT I feel agency over the simulation.
```
**Acceptance Criteria:**
- Setiap crisis event menghasilkan ≥ 2 pilihan keputusan
- Keputusan memiliki deskripsi dampak singkat sebelum dikonfirmasi
- Setelah keputusan, simulasi update dalam ≤ 10 detik
- Keputusan tercatat di history log

---

### US-06 — Public Reaction
```
AS a player,
I WANT to see how the public reacts to my decisions,
SO THAT I feel the political weight of my choices.
```
**Acceptance Criteria:**
- Approval rating berubah setelah setiap keputusan
- Minimal 3 segmen publik aktif: `lower_class`, `middle_class`, `students`
- Setiap segmen menampilkan anger level (0–100)
- Reaction tampil sebagai notifikasi/feed di dashboard

---

## 4. System Architecture (MVP)

### 4.1 High-Level Architecture

```
┌─────────────────────────────────────────────┐
│                  FRONTEND                   │
│         Astro + TailwindCSS + TypeScript     │
│         Presidential Dashboard UI           │
│         WebSocket Client                    │
└─────────────────┬───────────────────────────┘
                  │ WebSocket / REST
┌─────────────────▼───────────────────────────┐
│                  BACKEND                    │
│              Python + FastAPI               │
│                                             │
│  ┌───────────┐  ┌──────────┐  ┌──────────┐  │
│  │ Simulation│  │ AI Agent │  │  Event   │  │
│  │  Service  │  │ Service  │  │ Service  │  │
│  └───────────┘  └──────────┘  └──────────┘  │
│                                             │
│         WebSocket Gateway (realtime)        │
└──────────────┬──────────────────────────────┘
               │
┌──────────────▼──────────────────────────────┐
│                  DATA LAYER                 │
│         JSON File-based Storage             │
│  state.json │ events.json │ agents.json     │
│  history.json │ memory/ (per-agent)         │
└─────────────────────────────────────────────┘
               │
┌──────────────▼──────────────────────────────┐
│                  AI LAYER                   │
│         Ollama (local inference)            │
│  Models: Llama 3 / Qwen / DeepSeek / Gemma  │
└─────────────────────────────────────────────┘
```

---

### 4.2 Folder Structure

```
nusantara/
├── frontend/
│   ├── astro-app/
│   │   ├── src/
│   │   │   ├── components/        # Dashboard widgets
│   │   │   ├── layouts/           # Base page layouts
│   │   │   ├── pages/             # Astro pages (index, dashboard)
│   │   │   └── lib/
│   │   │       └── websocket.ts   # WS client logic
│   │   └── astro.config.mjs
│
└── backend/
    ├── api/                       # FastAPI route definitions
    ├── simulation/                # Economic simulation engine
    ├── ai_agents/                 # Cabinet AI agent definitions + runner
    ├── economy/                   # Economic variable models
    ├── events/                    # Event parser (manual + RSS)
    ├── memory/                    # Agent memory (short-term MVP)
    ├── websocket/                 # WS gateway + broadcaster
    └── database/                  # DB models + migrations
```

---

## 5. Economic Variables (MVP)

| Variable | Type | Range | Default | Notes |
|---|---|---|---|---|
| `usd_rp_rate` | float | 14000–20000 | 15800 | Simulasi; dapat di-sync real API di Phase 3 |
| `food_inflation` | float (%) | 0–30 | 4.5 | Berubah berdasarkan event + keputusan |
| `fuel_inflation` | float (%) | 0–50 | 5.0 | Berubah berdasarkan event + keputusan |
| `unemployment_rate` | float (%) | 0–40 | 5.8 | Berubah lambat (lagging indicator) |
| `approval_rating` | float (%) | 0–100 | 55.0 | Core KPI player |
| `national_debt_index` | float | 0–200 | 60.0 | Simplified debt metric |
| `investor_confidence` | float (%) | 0–100 | 65.0 | Pengaruhi economic growth |

**Economic Tick Logic:**
- Simulation tick setiap **60 detik** (configurable)
- Setiap tick: variabel di-decay/grow berdasarkan formula sederhana
- Event modifier stacked on top of base formula
- Keputusan player menghasilkan immediate modifier + delayed effect

---

## 6. AI Cabinet Agents (MVP)

### 6.1 Agent Roster

| ID | Role | Persona | Agenda |
|---|---|---|---|
| `finance_minister` | Minister of Finance | Technocrat, data-driven, risk-averse | Jaga fiscal stability, anti-populism |
| `social_minister` | Minister of Social Affairs | Populist, empathetic, pro-rakyat | Prioritas welfare, sering konflik dengan finance |
| `central_bank_gov` | Central Bank Governor | Independent, hawkish, formal | Jaga inflasi, tolak political pressure |
| `intel_chief` | Intelligence Chief | Paranoid, secretive, suspicious | Threat detection, selalu curiga pada pihak asing |
| `media_ai` | Media Advisor | Pragmatic, spin doctor | Kelola narasi publik, approval rating |

### 6.2 Agent Attributes (Per Agent)

```json
{
  "agent_id": "finance_minister",
  "personality": "technocrat",
  "stats": {
    "loyalty": 70,
    "ambition": 40,
    "stress": 30,
    "trust": 65,
    "influence": 75
  },
  "hidden_traits": {
    "corruption_risk": 10,
    "foreign_ties": false,
    "political_agenda": "fiscal_conservatism"
  },
  "memory": {
    "short_term": [],
    "relationship_map": {}
  }
}
```

### 6.3 Agent Discussion Protocol

Saat event di-trigger:
1. **Event di-broadcast ke semua agents** via internal message queue
2. **Setiap agent generate response** berdasarkan persona + event context
3. **Conflict detection**: jika 2+ agent memiliki respons berlawanan → flag sebagai cabinet conflict
4. **Response tampil di cabinet feed** secara sequential (bukan simultan)
5. **Player melihat semua responses** sebelum membuat keputusan

**Agent Prompt Template (Ollama):**
```
You are {agent_role} of Nusantara. Your personality: {personality}. Your agenda: {agenda}.

Current national situation:
- USD/RP: {usd_rp_rate}
- Approval Rating: {approval_rating}%
- Active Crisis: {crisis_description}

Respond in character. Be direct. Max 3 sentences. Mention specific policy recommendation.
Language: Bahasa Indonesia (mix English terms for formal concepts).
```

---

## 7. Event System (MVP)

### 7.1 Event Types

| Type | Trigger Method | Example |
|---|---|---|
| `manual_event` | Admin UI input | "Gempa bumi di Sulawesi" |
| `rss_event` | RSS feed parser | Berita ekonomi dari sumber terpilih |
| `simulation_event` | Auto-generated | "Inflasi melewati threshold 10%" |

### 7.2 Event Data Schema

```json
{
  "event_id": "uuid",
  "type": "economic | political | social | international",
  "severity": "low | medium | high | critical",
  "title": "string (max 80 chars)",
  "description": "string (max 300 chars)",
  "economic_impact": {
    "usd_rp_delta": 0,
    "food_inflation_delta": 0,
    "approval_delta": 0
  },
  "decision_options": [
    {
      "id": "option_a",
      "label": "string",
      "description": "string",
      "predicted_impact": "string"
    }
  ],
  "timestamp": "ISO8601",
  "source": "manual | rss | simulation"
}
```

### 7.3 RSS Feed Integration

- Parse 1–3 RSS feeds (ekonomi/geopolitik Indonesia)
- Klasifikasi otomatis via keyword matching (MVP) atau simple LLM call
- Rate limit: cek feed setiap **15 menit**
- Hanya berita dengan relevance score ≥ threshold yang masuk sebagai event

---

## 8. Frontend — Dashboard Specification

### 8.1 Layout Zones

```
┌────────────────────────────────────────────────────────┐
│  HEADER: Nation Name | Date/Time | President Name      │
├──────────────┬──────────────────┬──────────────────────┤
│              │                  │                      │
│  NATIONAL    │  CRISIS ALERT    │  CABINET AI FEED     │
│  STATUS      │  PANEL           │  (Live Discussion)   │
│  (KPI cards) │  (Breaking News) │                      │
│              │                  │                      │
├──────────────┴──────────────────┤                      │
│                                 │                      │
│  ECONOMIC GRAPHS                │                      │
│  (USD/RP, Inflation, Approval)  │                      │
│                                 │                      │
├─────────────────────────────────┴──────────────────────┤
│  PUBLIC OPINION FEED           │  DECISION PANEL       │
│  (Anger levels per segment)    │  (Current Options)    │
└────────────────────────────────────────────────────────┘
```

### 8.2 Visual Design Tokens

| Token | Value |
|---|---|
| Background | `#0A0E1A` (deep navy) |
| Surface | `#111827` (dark graphite) |
| Accent Blue | `#3B82F6` (electric blue) |
| Accent Green | `#10B981` (emerald) |
| Alert Red | `#EF4444` |
| Font Primary | Inter / Space Grotesk |
| Style | Glassmorphism panels, animated data lines |

### 8.3 Dashboard Components (MVP Required)

| Component | Data Source | Update Rate |
|---|---|---|
| `KpiCard` | Simulation state | Real-time (WS) |
| `EconomicGraph` | Historical simulation data | Every tick |
| `CrisisAlertBanner` | Event system | On event trigger |
| `CabinetFeed` | AI agent responses | On discussion |
| `PublicOpinionBar` | Public opinion model | Every 2 ticks |
| `DecisionPanel` | Active event options | On event trigger |
| `BreakingNewsTicker` | RSS + manual events | On event trigger |

---

## 9. API Endpoints (MVP)

### 9.1 REST Endpoints

| Method | Path | Description |
|---|---|---|
| `GET` | `/api/simulation/state` | Get current full simulation state |
| `POST` | `/api/events/manual` | Trigger manual event |
| `POST` | `/api/decisions/{event_id}` | Submit player decision |
| `GET` | `/api/cabinet/agents` | Get all cabinet agent states |
| `GET` | `/api/history/events` | Get event history (last 50) |
| `GET` | `/api/history/decisions` | Get decision history |
| `POST` | `/api/simulation/reset` | Reset simulation to defaults (dev only) |

### 9.2 WebSocket Events

| Event Name | Direction | Payload |
|---|---|---|
| `state_update` | Server → Client | Full simulation state snapshot |
| `event_triggered` | Server → Client | New event object |
| `cabinet_message` | Server → Client | Single agent response |
| `public_reaction` | Server → Client | Updated public opinion |
| `decision_submitted` | Client → Server | `{ event_id, option_id }` |

---

## 10. Data Models (MVP)

### 10.1 SimulationState

```python
class SimulationState(BaseModel):
    timestamp: datetime
    tick_number: int
    economy: EconomyState
    public_opinion: PublicOpinionState
    active_events: List[Event]
    cabinet_agents: List[AgentState]
    decision_history: List[Decision]
```

### 10.2 EconomyState

```python
class EconomyState(BaseModel):
    usd_rp_rate: float
    food_inflation: float
    fuel_inflation: float
    unemployment_rate: float
    approval_rating: float
    national_debt_index: float
    investor_confidence: float
    last_updated: datetime
```

### 10.3 PublicOpinionState

```python
class PublicOpinionState(BaseModel):
    lower_class: SegmentOpinion
    middle_class: SegmentOpinion
    students: SegmentOpinion

class SegmentOpinion(BaseModel):
    anger_level: float  # 0-100
    support_level: float  # 0-100
    primary_concern: str
```

---

## 10.4 JSON Storage Specification

Tidak ada database engine. Semua data disimpan sebagai file JSON di `backend/data/`.

### File Layout

```
backend/
└── data/
    ├── state.json          # SimulationState aktif (overwrite setiap tick)
    ├── events.json         # Array event history, max 200 entries
    ├── decisions.json      # Array decision history, max 200 entries
    ├── agents.json         # State + stats semua 5 cabinet agents
    └── memory/
        ├── finance_minister.json   # Short-term memory agent, max 50 entries
        ├── social_minister.json
        ├── central_bank_gov.json
        ├── intel_chief.json
        └── media_ai.json
```

### Read/Write Strategy

| Operasi | Mekanisme |
|---|---|
| **Server startup** | Load semua JSON → simpan ke RAM dict (`app.state.data`) |
| **Read (per request)** | Baca dari RAM dict, bukan dari file |
| **Write state** | Update RAM dict dulu → flush ke `state.json` setiap tick |
| **Append event/decision** | Append ke list di RAM → flush ke file-nya |
| **Agent memory** | Append ke list per-agent di RAM → flush ke `memory/{id}.json` |
| **Rotation** | Jika list > max entries → buang yang paling lama (FIFO) |
| **Concurrency** | Gunakan `asyncio.Lock` satu lock global untuk semua file write |

### state.json — Schema

```json
{
  "timestamp": "2025-01-01T00:00:00Z",
  "tick_number": 42,
  "economy": {
    "usd_rp_rate": 15800.0,
    "food_inflation": 4.5,
    "fuel_inflation": 5.0,
    "unemployment_rate": 5.8,
    "approval_rating": 55.0,
    "national_debt_index": 60.0,
    "investor_confidence": 65.0
  },
  "public_opinion": {
    "lower_class":  { "anger_level": 40, "support_level": 50, "primary_concern": "harga sembako" },
    "middle_class": { "anger_level": 30, "support_level": 60, "primary_concern": "lapangan kerja" },
    "students":     { "anger_level": 55, "support_level": 35, "primary_concern": "biaya pendidikan" }
  },
  "active_events": []
}
```

### events.json — Schema

```json
[
  {
    "event_id": "uuid",
    "type": "economic",
    "severity": "high",
    "title": "Harga minyak dunia naik 15%",
    "description": "...",
    "economic_impact": { "fuel_inflation_delta": 3.5, "approval_delta": -5 },
    "decision_options": [
      { "id": "option_a", "label": "Subsidi BBM", "predicted_impact": "approval +8, debt +5" },
      { "id": "option_b", "label": "Harga pasar", "predicted_impact": "approval -10, debt -2" }
    ],
    "timestamp": "2025-01-01T00:00:00Z",
    "source": "rss",
    "resolved": false
  }
]
```

### memory/{agent_id}.json — Schema

```json
[
  {
    "timestamp": "2025-01-01T00:00:00Z",
    "event_id": "uuid",
    "agent_response": "Kami harus menjaga defisit di bawah 3% GDP...",
    "player_decision": "option_a",
    "outcome_summary": "Approval naik 5 poin, inflasi turun 1%"
  }
]
```

---

## 11. Tech Stack (MVP — Final)

| Layer | Technology | Justification |
|---|---|---|
| Frontend Framework | Astro | Fast, content-oriented, SSR-capable |
| Frontend Styling | TailwindCSS | Utility-first, fast iteration |
| Frontend Language | TypeScript | Type safety untuk WS data handling |
| Backend Framework | Python FastAPI | Async, fast, AI library ecosystem |
| AI Inference | Ollama (local) | No API cost, privacy, offline-capable |
| AI Models | Llama 3 / Qwen / DeepSeek | Open source, Indonesian language support |
| Agent Orchestration | LangGraph | Stateful, multi-agent, memory support |
| Storage | JSON files (flat file) | Zero setup, no engine, cukup untuk MVP |
| In-memory cache | Python dict (in-process) | State di-cache di RAM selama server hidup |
| Agent memory | JSON per-agent file | `memory/{agent_id}.json`, append-only log |
| WebSocket | FastAPI WebSocket | Built-in, sufficient for MVP |

---

## 12. Development Phases (MVP = Phase 1 + Phase 2)

### Phase 1 — Foundation (Target: Working Prototype)

| Step | Task | Priority |
|---|---|---|
| 1 | Astro dashboard UI (static mockup) | CRITICAL |
| 2 | FastAPI backend skeleton + data models | CRITICAL |
| 3 | WebSocket realtime connection | CRITICAL |
| 4 | Economic simulation engine (tick logic) | CRITICAL |
| 5 | Ollama integration + 1 AI agent (Finance Minister) | HIGH |
| 6 | Manual event trigger | HIGH |

### Phase 2 — Playable MVP (Target: Livestream-Ready)

| Step | Task | Priority |
|---|---|---|
| 7 | All 5 cabinet agents active | CRITICAL |
| 8 | Cabinet live discussion flow | CRITICAL |
| 9 | Decision panel + consequence engine | CRITICAL |
| 10 | Public opinion system (3 segments) | HIGH |
| 11 | RSS feed integration | MEDIUM |
| 12 | Dashboard polish + OBS layout | HIGH |

---

## 13. Acceptance Criteria — MVP Complete

MVP dianggap selesai jika semua kondisi berikut terpenuhi:

- [ ] Dashboard menampilkan 5 KPI ekonomi secara real-time
- [ ] 5 AI cabinet ministers aktif dan menghasilkan response berbeda saat event
- [ ] Player dapat membuat keputusan dan melihat perubahan simulasi
- [ ] Minimal 1 RSS feed ter-parse dan menghasilkan event otomatis
- [ ] Dashboard dapat di-overlay di OBS tanpa lag signifikan
- [ ] Simulation berjalan stabil selama 1 jam tanpa crash
- [ ] Cabinet members menunjukkan conflict/disagreement setidaknya 1 dari 3 events

---

## 14. Out of Scope (MVP)

Fitur-fitur berikut **dilarang diimplementasikan** dalam MVP tanpa explicit approval:

- War / military combat system
- Election mechanics
- 3D / city simulation
- Full real-world API sync (USD/RP live, oil prices live)
- Advanced agent betrayal / corruption mechanics
- Viewer interaction (Twitch/YouTube integration)
- Multi-player / multiplayer
- Mobile optimization

---

## 15. Risk Register

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Ollama response latency > 30s | Medium | High | Set timeout; use smaller model; cache common responses |
| AI agents tidak konsisten persona | High | Medium | Gunakan system prompt yang ketat + temperature rendah |
| WebSocket disconnect di livestream | Low | High | Auto-reconnect logic + state snapshot on reconnect |
| RSS feed tidak relevan | Medium | Low | Manual event sebagai fallback utama |
| Database state corruption | Low | High | Snapshot state setiap 5 menit ke backup table |

---

## 16. Glossary

| Term | Definition |
|---|---|
| Tick | Satu siklus simulasi (default: 60 detik) |
| Event | Krisis atau berita yang memerlukan respons dari player |
| Decision | Pilihan player sebagai respons terhadap event |
| Cabinet Feed | Live UI feed yang menampilkan diskusi AI ministers |
| Approval Rating | Metrik utama keberhasilan player sebagai presiden |
| Agent | AI minister yang memiliki persona, stats, dan memory |
| Modifier | Nilai delta yang diaplikasikan ke variabel ekonomi setelah keputusan |

---

*PRD ini adalah living document. Update setelah setiap sprint. Versi ini adalah baseline MVP v1.0.*
