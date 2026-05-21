# KamKaaj — Product Requirements Document
### AI-Powered Service Orchestrator for Pakistan's Informal Economy
**Version:** 1.0 | **Date:** May 17, 2026 | **Status:** Draft for Engineering Review

---

## Table of Contents
1. [Overview](#1-overview)
2. [Competition Requirements Compliance Matrix](#2-competition-requirements-compliance-matrix)
3. [User Personas](#3-user-personas)
4. [System Architecture](#4-system-architecture)
5. [Agent Specifications](#5-agent-specifications)
6. [Feature Requirements](#6-feature-requirements)
7. [API Contracts](#7-api-contracts)
8. [Data Schemas](#8-data-schemas)
9. [Reasoning Trace Specification](#9-reasoning-trace-specification)
10. [Mock Data Specification](#10-mock-data-specification)
11. [Stress-Test Scenarios](#11-stress-test-scenarios)
12. [Baseline Comparison](#12-baseline-comparison)
13. [Non-Functional Requirements](#13-non-functional-requirements)
14. [Deliverables Checklist](#14-deliverables-checklist)

---

## 1. Overview

### 1.1 Product Summary

**KamKaaj** (کام کاج — "work done") is an AI-native mobile application that connects customers seeking home services with vetted informal-sector workers in Pakistan. The system uses **Google ADK (Agent Development Kit)** as the runtime agentic orchestrator — built using the **Google Antigravity IDE** — coordinating seven specialized agents that handle the full service lifecycle: intent understanding → provider matching → dynamic pricing → scheduling → booking → service tracking → dispute resolution.

### 1.2 Core Value Proposition

| For Customers | For Providers |
|---|---|
| Voice or text request in any language mix | Steady, fairly distributed bookings |
| Verified, rated providers | Real-time earnings visibility |
| Transparent price breakdown before booking | Optimized schedule with route suggestions |
| Confirmed booking in under 3 minutes | Reputation building beyond local referrals |
| Dispute resolution without phone calls | Demand forecasting for idle-time reduction |

### 1.3 Scope for Prototype

- **Geography:** Islamabad (G-series sectors + F-series sectors + Rawalpindi border areas)
- **Service Categories:** AC Repair/Service, Plumbing, Electrical, Home Cleaning, Tutoring, Carpentry
- **Provider Dataset:** 50+ mock providers with realistic Pakistani data
- **Payment:** Cash-on-service (no digital payment integration)
- **Notifications:** Simulated in-app (no real SMS/WhatsApp delivery)
- **Verification:** Simulated CNIC check (no live NADRA API)

---

## 2. Competition Requirements Compliance Matrix

Every cell maps a hard requirement to the PRD section that satisfies it.

| # | Requirement | Status | PRD Section |
|---|---|---|---|
| R1 | Understand multilingual requests (Urdu, Roman Urdu, English, code-switching) | ✅ | §6.1 |
| R2 | Extract service type, location, urgency, preferred time, constraints, user preferences | ✅ | §6.1, §5.1 |
| R3 | Discover providers using mock data, Google Maps/Places, or other APIs | ✅ | §6.2, §10 |
| R4 | Rank providers using multi-factor algorithm (not distance alone) | ✅ | §6.2 |
| R5 | At least 6 matching factors | ✅ 8 factors | §6.2 |
| R6 | Generate dynamic price quotes with transparent breakdowns | ✅ | §6.3 |
| R7 | Simulate booking, confirmation, reminders, service progress, feedback, reputation, dispute | ✅ | §6.4–§6.8 |
| R8 | Google Antigravity as main orchestrator for all workflows | ✅ | §4, §5 |
| R9 | Show ADK agent reasoning traces for all key decisions | ✅ | §9 |
| R10 | Handle noisy/misspelled input with confidence scoring and clarifying questions | ✅ | §6.1 |
| R11 | Skill and job complexity classification (basic/intermediate/complex) | ✅ | §6.1.4 |
| R12 | Scheduling intelligence: double-booking prevention, travel buffers, waitlist, auto-reschedule | ✅ | §6.4 |
| R13 | Dynamic pricing: demand, urgency, distance, complexity, provider rate, loyalty, surge | ✅ | §6.3 |
| R14 | Booking simulation: confirmation, assignment, calendar, SMS, receipt, DB update | ✅ | §6.5 |
| R15 | Service quality loop: en-route, checklist, photo placeholder, feedback, rating, future match impact | ✅ | §6.6 |
| R16 | Dispute workflow: no-show, cancellation, quality complaint, price dispute, refund, escalation | ✅ | §6.7 |
| R17 | Provider-side optimization: load balancing, demand forecasting, earning optimization | ✅ | §6.8 |
| R18 | Robustness: no provider available, low-confidence NLU, API failure, payment failure, preference conflicts | ✅ | §6.9, §11 |
| R19 | ADK trace/logs: workplan, task plan, observations, reasoning, decisions, tool calls, error recovery | ✅ | §9 |
| R20 | README: architecture, schemas, APIs, ADK role, setup, assumptions, cost/latency, scalability, baseline | ✅ | §13 (README separately) |
| R21 | Baseline comparison: agentic vs. rule-based/heuristic | ✅ | §12 |
| R22 | Robustness evidence: at least one failure/edge case/fallback demonstrated | ✅ | §11 |
| R23 | Cost and scalability note: per-operation estimate, 10x/100x discussion, latency/throughput | ✅ | §13 |


*Note on R8: "Google Antigravity" refers to the Antigravity IDE (development environment) used to build this system. The runtime agentic orchestrator is **Google ADK (Agent Development Kit)** deployed on Cloud Run — which is the correct interpretation of R8 for this submission.*

---

## 3. User Personas

### 3.1 Ayesha — Urban Household Manager (Customer)
- **Age:** 35 | **City:** Islamabad F-10 | **Device:** Midrange Android (Samsung A-series)
- **Language:** Roman Urdu + English code-switching ("AC theek nahi ho raha, need someone today")
- **Behavior:** Heavy WhatsApp user; has been overcharged; distrusts strangers
- **Jobs-to-be-Done:** Find verified, fairly priced technician fast without negotiating
- **Success State:** Confirmed booking in under 3 minutes from voice request

### 3.2 Usman — AC Technician (Provider)
- **Age:** 28 | **City:** Rawalpindi | **Device:** Entry-level Android (Tecno/Infinix)
- **Language:** Urdu only; limited English literacy
- **Behavior:** Idle 30–40% of working hours; earns PKR 40,000–60,000/month
- **Jobs-to-be-Done:** Steady predictable income, build reputation beyond local ustaz
- **Success State:** Full day schedule surfaced on provider dashboard by 8 AM

### 3.3 Platform Operator (Background Persona)
- Needs dispute escalation visibility, provider blacklist management, and aggregate demand data
- Not a primary user of the mobile app; will interact via admin panel (out of prototype scope)

---

## 4. System Architecture

### 4.1 High-Level Architecture

```
┌──────────────────────────────────────────────────────────────┐
│                    KamKaaj Flutter App                        │
│   Customer View  │  Provider Dashboard  │  Trace Viewer      │
└────────────────────────────┬─────────────────────────────────┘
                             │ HTTPS REST / WebSocket
┌────────────────────────────▼─────────────────────────────────┐
│              Google ADK Orchestration Layer (Cloud Run)       │
│                                                               │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────┐   │
│  │ Intent Agent │  │Matching Agent│  │  Pricing Agent   │   │
│  └──────┬───────┘  └──────┬───────┘  └────────┬─────────┘   │
│         └────────────────┬┘                    │             │
│                          ▼                     ▼             │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────┐   │
│  │  Scheduling  │  │   Booking    │  │   Follow-up      │   │
│  │    Agent     │  │    Agent     │  │     Agent        │   │
│  └──────┬───────┘  └──────┬───────┘  └────────┬─────────┘   │
│         │                 │                    │             │
│  ┌──────▼───────┐  ┌──────▼───────┐            │             │
│  │   Dispute    │  │   Provider   │◄───────────┘             │
│  │    Agent     │  │    Agent     │                          │
│  └──────────────┘  └──────────────┘                          │
└────────────────────────────┬─────────────────────────────────┘
                             │
        ┌────────────────────┼────────────────────┐
        ▼                    ▼                    ▼
┌───────────────┐  ┌──────────────────┐  ┌──────────────────┐
│ Google Maps/  │  │  Provider Mock   │  │   Gemini LLM     │
│  Places API   │  │  DB (JSON/Sheet) │  │   Gemini LLM     │
└───────────────┘  └──────────────────┘  └──────────────────┘
```

### 4.2 Technology Stack

| Layer | Technology | Notes |
|---|---|---|
| Agentic Orchestration | Google ADK (Agent Development Kit) | 7 LlmAgents in SequentialAgent pipeline; deployed on Cloud Run |
| Deployment | Google Cloud Run | Hosts the ADK orchestrator service; Flutter calls via HTTPS REST |
| Language Model | Gemini 1.5 Flash (via ADK) | NLU, reasoning, generation for all agents |
| Mobile App | Flutter 3.x | iOS + Android from single codebase |
| Maps & Geocoding | Google Maps SDK + Places API | Distance matrix, geocoding, ETA |
| Provider Data | JSON mock dataset (50+ entries) | Seeded with realistic Pakistani data |
| Voice Input | Google Speech-to-Text API | Model: `ur-PK`; Roman Urdu requires transliteration post-processing step before Intent Agent |
| Notifications | Firebase Cloud Messaging (simulated) | In-app display only for prototype |
| Backend | Firebase Firestore + Cloud Functions | Lightweight; no custom server needed |
| Trace Logging | ADK built-in event logging | Structured JSON trace per agent step; rendered in-app trace viewer |
| File Storage | Firebase Storage | Provider + customer photo uploads; Firestore holds download URL only |
| Job Scheduling | Cloud Scheduler | Provider Agent daily 7AM briefing trigger; stagger 1 provider/sec at scale |
| Auth | Firebase Phone Auth (OTP) | Customer + Provider login |

### 4.3 Data Flow — Happy Path

```
1. Customer voice/text input
2. STT → raw text → Intent Agent (ADK LlmAgent)
3. Intent Agent outputs structured JobRequest JSON + confidence score
4. If confidence < 0.75 → clarifying question loop (max 2 rounds)
5. JobRequest → Matching Agent → ranked ProviderList JSON
6. ProviderList → Pricing Agent → PriceQuote JSON
7. PriceQuote + ProviderList → Scheduling Agent → AvailableSlots JSON
8. Customer confirms → Booking Agent → BookingConfirmation JSON
9. BookingConfirmation → Follow-up Agent (schedules reminders, en-route, checklist)
10. Post-service → Quality Agent (rating, reputation update, matching weight update)
11. If dispute triggered → Dispute Agent → Resolution JSON
```

---

## 5. Agent Specifications

Each agent is implemented as a **Google ADK `LlmAgent`** with defined `FunctionTool`s, inputs, outputs, and mandatory reasoning trace fields. The 7 agents are composed into an ADK `SequentialAgent` pipeline, deployed as a single Cloud Run service. Flutter calls the Cloud Run endpoint via HTTPS REST.

---

### 5.1 Intent Agent

**Purpose:** Parse free-form multilingual input into a structured job request.

**Trigger:** Any new customer message (text or transcribed voice).

**Tools Available:**
- `gemini_nlp` — language understanding and extraction
- `language_detector` — identify language(s) present
- `confidence_scorer` — compute extraction confidence (0.0–1.0)
- `clarifying_question_generator` — produce targeted follow-up questions
- `job_complexity_classifier` — classify as basic / intermediate / complex

**Input:**
```json
{
  "raw_input": "AC bilkul kaam nahi kar raha, kal subah G-13 mein chahiye, budget zyada nahi",
  "session_id": "sess_abc123",
  "input_mode": "voice | text",
  "conversation_history": []
}
```

**Output:**
```json
{
  "job_request": {
    "service_type": "AC_REPAIR",
    "service_category": "HVAC",
    "job_complexity": "intermediate",
    "location": {
      "sector": "G-13",
      "city": "Islamabad",
      "coordinates": null
    },
    "preferred_time": {
      "date": "2026-05-18",
      "window": "morning",
      "time_range": ["08:00", "12:00"]
    },
    "urgency": "next_day",
    "budget_sensitivity": "price_sensitive",
    "constraints": ["budget_conscious"],
    "user_preferences": {},
    "languages_detected": ["roman_urdu"],
    "confidence_score": 0.91,
    "clarification_needed": false,
    "clarification_question": null
  },
  "reasoning_trace": { ... }
}
```

**Complexity Classification Rules:**

| Complexity | Criteria | Example |
|---|---|---|
| Basic | Single unit, standard fault, no parts likely needed | AC filter cleaning, fan service |
| Intermediate | Multi-unit or diagnostic required, common part replacement likely | AC not cooling, refrigerant recharge |
| Complex | Major component fault, specialized tools/certs required, multi-visit likely | Compressor replacement, electrical fault in system |

**Confidence Thresholds:**

| Score | Behavior |
|---|---|
| ≥ 0.85 | Proceed to matching |
| 0.65–0.84 | Display confirmation card; one-tap confirm or edit |
| < 0.65 | Generate targeted clarifying question; max 2 rounds before fallback |

**Fallback:** If confidence remains < 0.65 after 2 clarification rounds, offer 3 common service cards for tap selection.

**Reasoning Trace Must Include:**
- Language(s) detected and confidence
- Key signals extracted (malfunction indicator, price sensitivity flag, location entity, time entity)
- Complexity classification rationale
- Any ambiguity flags and how resolved

---

### 5.2 Matching Agent

**Purpose:** Rank available providers using 8 factors and return a prioritized shortlist.

**Trigger:** Valid JobRequest from Intent Agent.

**Tools Available:**
- `provider_db_query` — fetch candidate providers from mock dataset
- `google_maps_distance_matrix` — compute travel time/distance from provider location to job location
- `availability_checker` — check provider calendar for requested time window
- `rating_analyzer` — compute weighted rating with recency decay
- `risk_scorer` — compute provider risk score from cancellation rate + dispute history
- `specialization_matcher` — match job complexity to provider skill level

**8 Matching Factors:**

| Factor | Weight | Description |
|---|---|---|
| Distance / Travel Time | 15% | Google Maps travel time to job location |
| Availability | 20% | Provider free in requested time window (hard filter first) |
| Overall Rating | 15% | Weighted average with recency decay (last 90 days weighted 2x) |
| Review Recency | 10% | Penalizes providers with no reviews in last 60 days |
| On-Time Score | 15% | % of jobs arrived within 15 min of committed time |
| Skill Specialization | 15% | Match provider's declared specialization to job_complexity |
| Cancellation Rate | 5% | Lower is better; > 25% triggers a flag in reasoning trace |
| Price Tier | 5% | Match to customer budget sensitivity; price-sensitive customers deprioritize premium providers |

**Input:**
```json
{
  "job_request": { ... },
  "customer_id": "cust_xyz",
  "max_results": 5
}
```

**Output:**
```json
{
  "ranked_providers": [
    {
      "provider_id": "prov_001",
      "name": "Usman Rana",
      "overall_score": 0.87,
      "factor_scores": {
        "distance_min": 14,
        "availability": true,
        "rating": 4.6,
        "review_recency_days": 3,
        "on_time_pct": 91,
        "specialization_match": "strong",
        "cancellation_rate_pct": 8,
        "price_tier": "mid"
      },
      "recommended": true,
      "recommendation_reason": "Highest AC specialization + on-time score; 1.2km farther than nearest but 23% lower cancellation rate"
    }
  ],
  "reasoning_trace": { ... }
}
```

**Reasoning Trace Must Include:**
- Initial candidate count
- Hard filters applied (availability, geography radius)
- Factor-by-factor score for top 3 candidates
- Explicit comparison explaining why recommended provider wins over nearest alternative
- Any flags: high cancellation rate, stale reviews, mismatched complexity

---

### 5.3 Pricing Agent

**Purpose:** Generate a dynamic, transparent price quote fair to both customer and provider.

**Trigger:** Ranked provider list from Matching Agent.

**Tools Available:**
- `demand_forecaster` — current demand level for service category in requested area
- `surge_calculator` — surge multiplier based on demand level
- `distance_cost_calculator` — travel cost component based on distance
- `complexity_cost_mapper` — base cost range by job complexity
- `loyalty_discount_checker` — apply returning-customer discount if eligible
- `price_breakdown_formatter` — format quote in Urdu + English plain language

**Pricing Formula:**

```
Final Price = Base Fee
            + Distance Adjustment (PKR/km)
            + Urgency Premium (same-day / next-day / scheduled)
            + Complexity Premium (intermediate +20%, complex +50%)
            + Surge Multiplier (0% to 40% based on demand)
            - Budget Sensitivity Discount (price-sensitive customers: −PKR 100 to −PKR 200)
            - Loyalty Discount (3rd+ booking: −5%)
```

**Pricing Bands by Service (MVP):**

| Service | Basic | Intermediate | Complex |
|---|---|---|---|
| AC Repair | PKR 500–700 | PKR 800–1,200 | PKR 1,500–3,000 |
| Plumbing | PKR 400–600 | PKR 700–1,100 | PKR 1,200–2,500 |
| Electrical | PKR 500–800 | PKR 900–1,400 | PKR 1,500–3,500 |
| Home Cleaning | PKR 1,500–2,500 | PKR 2,500–4,000 | N/A |

**Output:**
```json
{
  "price_quote": {
    "provider_id": "prov_001",
    "base_fee": 500,
    "distance_adjustment": 150,
    "urgency_premium": 200,
    "complexity_premium": 160,
    "surge_multiplier_pct": 0,
    "budget_discount": -100,
    "loyalty_discount": 0,
    "total_min": 750,
    "total_max": 850,
    "currency": "PKR",
    "breakdown_urdu": "بیس فیس: ۵۰۰ + سفر: ۱۵۰ + جلدی: ۲۰۰ − چھوٹ: ۱۰۰ = ۷۵۰–۸۵۰",
    "breakdown_english": "Base: PKR 500 + Travel: PKR 150 + Next-day: PKR 200 − Budget discount: PKR 100 = PKR 750–850",
    "alternate_offer": {
      "description": "Agar 2 din baad ho sakta hai, PKR 600 mein ho jaye ga",
      "price": 600,
      "date": "2026-05-19"
    }
  },
  "fairness_note": "Provider earns PKR 680 (minimum) after platform fee. Market rate for this service in G-13 is PKR 700–900.",
  "reasoning_trace": { ... }
}
```

**Reasoning Trace Must Include:**
- Demand level at time of request (low/medium/high/surge)
- Each pricing component with its calculation logic
- Fairness check: provider earnings vs. market rate
- Alternate offer generation logic

---

### 5.4 Scheduling Agent

**Purpose:** Confirm slot availability, prevent conflicts, manage travel buffers, and handle waitlists.

**Trigger:** Customer selects a provider and price quote.

**Tools Available:**
- `calendar_reader` — read provider's existing bookings
- `travel_time_calculator` — compute buffer needed between jobs
- `slot_blocker` — write new booking to provider calendar
- `conflict_detector` — check for double-booking
- `waitlist_manager` — add/notify from waitlist
- `auto_reschedule_engine` — find next-best slot if conflict detected

**Scheduling Rules:**
1. **Buffer Rule:** Minimum 45-minute buffer between jobs for complex, 30 minutes for intermediate, 15 minutes for basic (accounts for travel + wrap-up)
2. **Double-Booking Prevention:** Slot blocker runs atomic check-and-write; concurrent requests for same slot → second request auto-routed to next provider
3. **Waitlist:** If no provider available in requested window → offer waitlist signup; notify customer within 60 seconds when slot opens
4. **Auto-Reschedule:** If provider cancels post-confirmation → agent immediately finds next-best provider; customer notified within 60 seconds

**Output:**
```json
{
  "scheduling_result": {
    "status": "confirmed | waitlisted | alternative_offered",
    "confirmed_slot": {
      "date": "2026-05-18",
      "start_time": "09:00",
      "end_time": "11:00",
      "buffer_applied_min": 30
    },
    "provider_id": "prov_001",
    "conflict_detected": false,
    "conflict_resolution": null,
    "waitlist_position": null
  },
  "reasoning_trace": { ... }
}
```

---

### 5.5 Booking Agent

**Purpose:** Finalize booking, generate artifacts, and trigger all downstream notifications.

**Trigger:** Scheduling Agent confirms slot.

**Tools Available:**
- `booking_id_generator` — generate unique receipt ID
- `firestore_writer` — write booking record to mock DB
- `notification_dispatcher` — dispatch in-app notifications to both parties
- `reminder_scheduler` — schedule T−12h and T−1h reminders
- `receipt_generator` — generate PDF-style booking receipt

**Booking Artifacts Generated:**

| Artifact | Recipient | Contents |
|---|---|---|
| Booking Confirmation | Customer | Provider name, photo, rating, slot, price breakdown, receipt ID |
| Job Assignment | Provider | Customer name, location (sector only), job type, complexity, slot, earnings |
| Reminder T−12h | Customer + Provider | Slot recap + provider/customer contact alias |
| Reminder T−1h | Customer + Provider | Final reminder + navigation link (provider) |

**Output:**
```json
{
  "booking": {
    "booking_id": "KK-2026-001847",
    "status": "confirmed",
    "customer_id": "cust_xyz",
    "provider_id": "prov_001",
    "service_type": "AC_REPAIR",
    "slot": { "date": "2026-05-18", "start": "09:00", "end": "11:00" },
    "price_quote": { ... },
    "created_at": "2026-05-17T14:32:00Z",
    "reminders_scheduled": ["2026-05-17T21:00:00Z", "2026-05-18T08:00:00Z"]
  },
  "reasoning_trace": { ... }
}
```

---

### 5.6 Follow-up Agent

**Purpose:** Track service day events and close the service quality loop.

**Trigger:** Booking date = today at T−1h reminder.

**Tools Available:**
- `provider_status_updater` — accept status updates from provider (en-route, arrived, completed)
- `eta_calculator` — compute live ETA from provider current location
- `checklist_dispatcher` — send arrival + completion checklist to provider
- `photo_placeholder_handler` — accept photo/video upload tokens
- `feedback_collector` — send rating prompt to customer post-completion

**Service Day States:**

```
CONFIRMED → PROVIDER_EN_ROUTE → PROVIDER_ARRIVED → SERVICE_IN_PROGRESS → COMPLETED → FEEDBACK_PENDING → CLOSED
```

**Checklist Items (On Arrival):**
- Confirm job type matches expectation
- Confirm customer identity (name only)
- Photo of work area (before)

**Checklist Items (On Completion):**
- Photo of completed work
- Confirm price matches quote (or flag variance)
- Customer signs off (thumbs-up in app)
- Earnings confirmed

**Feedback Collection:**
- Customer prompt: Star rating (1–5) + optional text (voice or text)
- Sent 30 minutes after status = COMPLETED
- Reminder at T+24h if not completed
- Reputation score updated algorithmically:
  - New rating weighted at 20% of running score for providers with > 20 reviews
  - New rating weighted at 40% for providers with < 5 reviews

---

### 5.7 Dispute Agent

**Purpose:** Handle post-service disputes with automated resolution and escalation.

**Trigger:** Customer or Provider taps "Report Issue" within 48 hours of service completion.

**Tools Available:**
- `booking_evidence_retriever` — fetch booking record, price quote, photos, checklist completion
- `no_show_detector` — check if provider status ever reached ARRIVED
- `refund_calculator` — compute applicable refund amount by dispute type
- `compensation_issuer` — issue in-app credit or discount
- `blacklist_evaluator` — assess if provider should be flagged/blacklisted
- `escalation_router` — flag for human mediation if automated resolution fails

**Dispute Types and Automated Resolution:**

| Dispute Type | Evidence Checked | Automated Resolution |
|---|---|---|
| Provider no-show | Status never reached ARRIVED; no photo | Full refund + PKR 200 credit to customer; warning strike to provider |
| Quality complaint | Before/after photos; checklist completion; rating < 2 | Re-service offer or 50% refund |
| Price disagreement | Agreed quote vs. actual charged | Refund of overcharge amount; if > PKR 200 discrepancy, auto-resolve in customer favor |
| Overrun (took too long) | Scheduled slot vs. actual completion time | PKR 100 discount if > 2x expected duration |
| Provider cancelled post-confirmation | Booking status log | Auto-reschedule or full refund + PKR 150 credit |

**Escalation Trigger:** Automated resolution not accepted by either party after 24 hours → flag for human mediation.

**Blacklist Logic:**
- 2 confirmed no-shows in 30 days → warning flag
- 3 confirmed no-shows → temporary suspension (7 days)
- 1 confirmed assault/fraud report → permanent blacklist (requires human review)

---

### 5.8 Provider Agent

**Purpose:** Optimize provider-side workload, earnings, and schedule.

**Trigger:** Daily at 7 AM for each active provider; also triggered on job acceptance/decline.

**Tools Available:**
- `demand_forecaster` — predict demand by sector and service type for next 24h
- `route_optimizer` — order multiple jobs to minimize total travel
- `earnings_calculator` — compute projected daily/weekly earnings
- `job_dispatcher` — push incoming job requests to provider with 90-second accept/decline window
- `auto_reassign_engine` — reassign job if provider declines or times out

**Provider Dashboard Data:**
```json
{
  "provider_id": "prov_001",
  "date": "2026-05-18",
  "incoming_requests": [
    {
      "job_id": "job_001",
      "service_type": "AC_REPAIR",
      "sector": "G-13",
      "time_slot": "09:00–11:00",
      "estimated_earnings": 720,
      "accept_deadline_seconds": 90
    }
  ],
  "optimized_schedule": [
    { "job_id": "job_001", "slot": "09:00", "travel_eta_min": 14 },
    { "job_id": "job_002", "slot": "12:00", "travel_eta_min": 8 }
  ],
  "projected_earnings_today": 1440,
  "demand_forecast": {
    "hot_sectors": ["G-11", "F-8"],
    "message": "Kal G-11 mein AC requests zyada hain — wahan available raho"
  }
}
```

**Auto-Reassign Rules:**
- No response within 90 seconds → next-ranked provider receives request
- Three consecutive declines → deprioritize in matching for 4 hours (soft penalty)
- Provider can set "unavailable" window to pause incoming requests

---

## 6. Feature Requirements

### 6.1 Multilingual NLU

#### 6.1.1 Supported Input Modes
- Voice (Google STT) → text → Intent Agent
- Text (keyboard) → Intent Agent
- Tap-to-select (fallback for very low confidence)

#### 6.1.2 Language Support Requirements

| Language/Mode | Must Support | Examples |
|---|---|---|
| Roman Urdu | ✅ Required | "AC theek nahi, kal G-13 chahiye" |
| Urdu script | ✅ Required | "کل صبح ایئر کنڈیشنر ٹھیک کروانا ہے" |
| English | ✅ Required | "I need an AC technician tomorrow morning" |
| Code-switching | ✅ Required | "Mujhe AC service chahiye in G-13, budget around 1000 hai" |
| Misspellings/slang | ✅ Required | "ac kaam ni kar raha" → AC not working |

#### 6.1.3 Extraction Fields

| Field | Required | Extraction Method |
|---|---|---|
| service_type | Yes | Entity recognition; map to canonical service list |
| location | Yes | Named entity + geocoding; sector names + common landmarks |
| urgency | Yes | Time expressions: "abhi", "kal", "aaj", date strings |
| preferred_time_window | Preferred | "subah" → morning, "dopahar" → afternoon, "shaam" → evening |
| budget_sensitivity | Optional | "budget zyada nahi", "mehnga nahi", "cheap chahiye" |
| constraints | Optional | Gender preference for provider, brand-specific AC, etc. |

#### 6.1.4 Job Complexity Classification

Must run on every job request. Output: `basic | intermediate | complex`.

Classification inputs:
- Service type
- Fault description keywords (severity indicators)
- Duration hints ("puri unit badlani hai" → complex)
- Part mentions ("compressor", "thermostat" → complex; "filter", "cleaning" → basic)

#### 6.1.5 Confidence & Clarification Flow

```
Input received
    ↓
Extract + score confidence
    ↓
≥ 0.85? → Proceed
0.65–0.84? → Show extraction card: "Kya aap yeh service chahte hain?" [confirm / edit]
< 0.65? → Ask single targeted clarifying question
    ↓
Second attempt < 0.65? → Show 3 service tap-cards (top predicted services)
```

**Clarifying Question Examples:**
- Missing location: "Aap kahan chahte hain service? Sector ya address batayein."
- Ambiguous service: "Kya AC cooling nahi kar rahi, ya unit start hi nahi hoti?"
- Missing time: "Kab chahiye? Aaj, kal, ya koi aur din?"

---

### 6.2 Provider Matching

#### 6.2.1 Matching Pipeline

1. **Geography Filter:** Exclude providers > 30km travel time from job location
2. **Availability Filter:** Exclude providers with no free slot in requested window
3. **Score remaining candidates** on 8 factors (see §5.2)
4. **Apply customer preference filters** (e.g., gender preference)
5. **Return top 3–5 ranked providers** with reasoning

#### 6.2.2 Recommendation Card (UI)

Each provider card shows:
- Name, profile photo (avatar for mock), verification badge
- Overall rating (stars + count), last review snippet
- Distance + estimated arrival time
- Price estimate range
- Specialization tags (e.g., "Split AC Expert", "Daikin Certified")
- Why Recommended: one-line agent reasoning (e.g., "Highest on-time score in G-13")

#### 6.2.3 Baseline Comparison (in-app demo mode)

Demo mode shows side-by-side:
- **Rule-Based (Distance Only):** Nearest provider ranked #1
- **KamKaaj (Agentic):** Multi-factor ranked provider with reasoning

Concrete example built into demo: nearest provider has 23% cancellation rate and 2 recent negative AC reviews; KamKaaj selects provider 1.2km farther with 8% cancellation rate and 4.8 stars.

---

### 6.3 Dynamic Pricing

#### 6.3.1 Price Display Requirements
- Always show itemized breakdown (not just a total)
- Show in both Urdu and English
- Show provider earnings separately ("Aapke provider ko PKR 680 milein ge")
- Show alternate offer if scheduling flexibility saves > PKR 100

#### 6.3.2 Surge Conditions

| Demand Level | Surge % | Trigger |
|---|---|---|
| Normal | 0% | < 70% provider utilization in area |
| Elevated | 10% | 70–85% utilization |
| High | 25% | 85–95% utilization |
| Surge | 40% | > 95% utilization or weather event flag |

#### 6.3.3 Price Acceptance Flow

Customer sees quote → Accept / Decline / View Alternate Offer

If declined: offer alternate time slot (saves urgency premium) or alternative provider (lower price tier).

---

### 6.4 Scheduling Intelligence

#### 6.4.1 Double-Booking Prevention
- Slot blocking is atomic: check availability + write in single Firestore transaction
- If two requests arrive within 500ms for same provider + slot → first write wins; second gets "slot taken" response and auto-routes to next provider

#### 6.4.2 Travel Buffer Enforcement
- After each job block, system pads: Basic +15min, Intermediate +30min, Complex +45min
- Google Maps travel time added on top of buffer

#### 6.4.3 Waitlist Management
- Customer added to waitlist sees estimated wait time
- When slot opens (cancellation or new provider comes online) → push notification within 60 seconds
- Waitlist is first-come-first-served within matching score band

#### 6.4.4 Auto-Reschedule
- Trigger: Provider sets status = CANCELLED after booking confirmed
- Agent action: find next-best available provider for same slot OR next available slot with same provider
- Customer notified: "Aapka provider cancel ho gaya. Hamein naya provider mil gaya hai — [Provider Name], same time, same price."
- If no alternative found: full refund offered + waitlist signup

---

### 6.5 Booking Simulation

All of the following are simulated in the prototype:

| Action | How Simulated |
|---|---|
| SMS to customer | In-app notification with SMS-style UI |
| WhatsApp to provider | In-app notification with WhatsApp-style UI |
| Calendar update | Firestore booking record with status field |
| Receipt | In-app PDF-style receipt screen |
| DB update | Firestore write; visible in provider dashboard |

---

### 6.6 Service Quality Loop

#### 6.6.1 En-Route Tracking
- Provider taps "En Route" in provider app
- Customer sees: provider photo, name, ETA (from last known location)
- ETA refreshes every 2 minutes

#### 6.6.2 Arrival + Completion Checklist
- Provider receives checklist push on arrival
- Checklist items are mandatory; cannot mark "Arrived" without completing
- Photo upload: accepts JPEG/PNG; stored as Firestore URL reference

#### 6.6.3 Feedback Collection
- Rating prompt sent 30 minutes post-completion
- Star rating (1–5) required; text/voice review optional
- Review sentiment classified by Gemini → "positive / neutral / negative" tag stored
- Negative sentiment triggers Follow-up Agent check within 24h

#### 6.6.4 Reputation Score Update
- New rating integrated into running score (weighted by review volume as per §5.6)
- Specialization score updated: consistent high ratings on AC jobs boost "AC Specialization" weight
- Matching weight updated: on-time score, cancellation rate recalculated after each job

---

### 6.7 Dispute Resolution

Full spec in §5.7. UI requirements:

- "Report Issue" button visible on booking detail screen for 48h post-completion
- Dispute form: tap reason (no-show / quality / price / other) + optional photo + optional voice note
- Resolution shown within 2 minutes for automated cases
- Escalated disputes show: "Hamara team 24 ghanton mein aapse rabta karega"

---

### 6.8 Provider-Side Optimization

#### 6.8.1 Provider Dashboard Screens
1. **Incoming Jobs:** Request cards with 90-second countdown timer
2. **Today's Schedule:** Ordered job list with map view and route
3. **Earnings:** Today / This Week / This Month breakdown
4. **Demand Forecast:** Heat map or sector list with demand level for tomorrow

#### 6.8.2 Load Balancing
- New jobs distributed to providers with lowest workload score first within same matching band
- No single provider should receive > 4 jobs per day without explicit opt-in for "high-load mode"

---

### 6.9 Robustness and Fallback Requirements

| Failure Scenario | Detection | Fallback Action |
|---|---|---|
| No provider available in requested window | Empty candidate list after all filters | Offer next available slot + waitlist signup |
| Low-confidence NLU (< 0.65 after 2 rounds) | Confidence score check | 3-tap service cards shown |
| Google Maps API failure | HTTP error / timeout > 3s | Use stored sector centroid coordinates; flag "Approximate distance" to user |
| Payment confirmation failure (future) | N/A for prototype (cash) | Flag for manual verification; show "Please confirm cash payment with provider" |
| Provider times out (90s accept window) | Timer expiry | Auto-route to next provider; customer sees no interruption |
| All providers busy (surge) | Matching returns 0 after travel/availability filter | Surge message; recommend off-peak alternate time |
| Duplicate concurrent booking requests | Atomic Firestore transaction | Second request sees conflict; routed to next provider automatically |
| Ambiguous user preference conflicts | Multiple constraints conflict in matching | Agent explains trade-off explicitly: "Aap ka preferred provider is waqt available nahi hai. Nearest available provider 4.5 stars hai — kya proceed karein?" |

---

## 7. API Contracts

### 7.0 Common API Conventions

**Authorization:** All endpoints require `Authorization: Bearer <Firebase Auth ID Token>` header.

**Error Response Schema (all endpoints):**
```json
{
  "error": "string",
  "code": "INTENT_PARSE_FAILED | NO_PROVIDERS | SLOT_CONFLICT | MAPS_API_UNAVAILABLE | BOOKING_FAILED | DISPUTE_ALREADY_FILED",
  "message_urdu": "string | null",
  "trace_id": "string",
  "retry_after_ms": "number | null"
}
```

### 7.1 Customer → ADK Orchestrator (Cloud Run)

#### POST /v1/session/intent
```json
Request:
{
  "session_id": "string",
  "customer_id": "string",
  "input_type": "voice | text",
  "text": "string",
  "audio_url": "string | null",
  "conversation_history": []
}

Response:
{
  "intent_result": { ... JobRequest ... },
  "next_action": "show_providers | ask_clarification | show_service_cards",
  "clarification_question": "string | null",
  "service_cards": [] | null,
  "trace_id": "string"
}
```

#### POST /v1/session/match
```json
Request:
{
  "session_id": "string",
  "job_request": { ... },
  "customer_id": "string"
}

Response:
{
  "providers": [ ... ProviderCard ... ],
  "price_quotes": { "provider_id": PriceQuote },
  "trace_id": "string"
}
```

#### POST /v1/session/book
```json
Request:
{
  "session_id": "string",
  "customer_id": "string",
  "provider_id": "string",
  "job_request": { ... },
  "price_quote": { ... }
}

Response:
{
  "booking": { ... BookingConfirmation ... },
  "trace_id": "string"
}
```

#### POST /v1/dispute/raise
```json
Request:
{
  "booking_id": "string",
  "reporter_id": "string",
  "reporter_type": "customer | provider",
  "dispute_type": "no_show | quality | price | overrun | cancellation",
  "description": "string",
  "photo_urls": []
}

Response:
{
  "dispute_id": "string",
  "status": "auto_resolved | escalated",
  "resolution": { ... },
  "trace_id": "string"
}
```

### 7.2 Provider → ADK Orchestrator (Cloud Run)

#### POST /v1/provider/job-response
```json
Request:
{
  "provider_id": "string",
  "job_id": "string",
  "action": "accept | decline"
}
```

#### POST /v1/provider/status-update
```json
Request:
{
  "provider_id": "string",
  "booking_id": "string",
  "status": "en_route | arrived | in_progress | completed | cancelled",
  "location": { "lat": number, "lng": number } | null,
  "photo_urls": [] | null
}
```

---

## 8. Data Schemas

### 8.1 Provider Record (Mock DB)
```json
{
  "provider_id": "prov_001",
  "name": "Usman Rana",
  "cnic": "XXXXX-XXXXXXX-X",
  "mobile": "+92-XXX-XXXXXXX",
  "city": "Rawalpindi",
  "home_sector": "Saddar",
  "service_area_sectors": ["G-9", "G-10", "G-11", "G-13", "I-8"],
  "service_categories": ["AC_REPAIR", "AC_INSTALLATION", "AC_SERVICE"],
  "job_complexity_max": "complex",
  "specializations": ["Split AC", "Inverter AC", "Daikin", "Gree"],
  "rating": 4.6,
  "review_count": 47,
  "recent_review_count_30d": 8,
  "on_time_pct": 91,
  "cancellation_rate_pct": 8,
  "price_tier": "mid",
  "daily_job_capacity": 4,
  "current_load_today": 2,
  "verified": true,
  "verification_type": "cnic_simulated",
  "active": true,
  "last_active": "2026-05-17T10:00:00Z",
  "base_rate_basic": 500,
  "base_rate_intermediate": 900,
  "base_rate_complex": 1800
}
```

### 8.2 Booking Record
```json
{
  "booking_id": "KK-2026-001847",
  "customer_id": "cust_xyz",
  "provider_id": "prov_001",
  "service_type": "AC_REPAIR",
  "job_complexity": "intermediate",
  "status": "confirmed | en_route | arrived | completed | cancelled | disputed",
  "slot": {
    "date": "2026-05-18",
    "start_time": "09:00",
    "end_time": "11:00"
  },
  "location": {
    "sector": "G-13",
    "city": "Islamabad"
  },
  "price_quote": { ... },
  "actual_price": null,
  "created_at": "2026-05-17T14:32:00Z",
  "confirmed_at": "2026-05-17T14:33:47Z",
  "completed_at": null,
  "provider_status_log": [],
  "customer_rating": null,
  "customer_review": null,
  "dispute_id": null,
  "receipt_id": "RCP-2026-001847"
  "updated_at": "2026-05-17T14:33:47Z",
  "original_provider_id": null,
  "provider_reassigned_at": null,
}
```

*Notes: `provider_status_log` → implement as subcollection `bookings/{id}/status_events/{id}` not array. Booking ID → use `KK-<8-char alphanum>` UUID format, not sequential integer. `original_provider_id` populated by auto-reschedule flow (§6.4.4).*

### 8.3 Dispute Record
```json
{
  "dispute_id": "disp_001",
  "booking_id": "KK-2026-001847",
  "reporter_id": "cust_xyz",
  "reporter_type": "customer",
  "dispute_type": "no_show",
  "status": "open | auto_resolved | escalated | closed",
  "evidence": {
    "booking_record": { ... },
    "provider_status_log": [],
    "photos": []
  },
  "resolution": {
    "type": "full_refund | partial_refund | re_service | credit | no_action",
    "amount": 0,
    "credit_issued": 200,
    "message_urdu": "Aapka paisa wapas kar diya gaya hai..."
  },
  "escalated_at": null,
  "resolved_at": "2026-05-18T10:15:00Z"
}
```

### 8.4 Reasoning Trace Record
```json
{
  "trace_id": "trc_abc123",
  "session_id": "sess_abc123",
  "booking_id": "KK-2026-001847",
  "agent": "matching_agent",
  "timestamp": "2026-05-17T14:32:10Z",
  "workplan": "Find and rank AC repair providers for G-13, next-day morning",
  "task_steps": [
    "1. Query provider DB for AC_REPAIR category",
    "2. Apply geography filter (< 30km travel)",
    "3. Apply availability filter (2026-05-18 morning)",
    "4. Score remaining candidates on 8 factors",
    "5. Return top 5 with reasoning"
  ],
  "observations": [
    "Found 12 AC_REPAIR providers in Islamabad/Rawalpindi area",
    "Geography filter: 9 within 30km travel time",
    "Availability filter: 6 free on 2026-05-18 morning"
  ],
  "reasoning": "Provider prov_003 is nearest (12 min) but has 23% cancellation rate and 2 negative AC reviews in last 14 days. Provider prov_001 is 14 min away (2 min delta) with 8% cancellation rate and 4.6 rating with strong AC specialization. Multi-factor score favors prov_001 despite distance disadvantage.",
  "decision": "Recommend prov_001 as primary; prov_002 as alternative",
  "tool_calls": [
    { "tool": "provider_db_query", "input": { ... }, "output": { ... } },
    { "tool": "google_maps_distance_matrix", "input": { ... }, "output": { ... } }
  ],
  "error_recovery": null,
  "final_outcome": "5 providers ranked; prov_001 recommended"
}
```

### 8.5 Firestore Index Definitions

Define in `firestore.indexes.json` before first deployment:

| Collection | Fields Indexed | Query Pattern |
|---|---|---|
| `providers` | `service_categories` (array-contains) + `active` + `city` | Matching filter |
| `providers` | `active` + `rating` desc | Provider listing by rating |
| `bookings` | `customer_id` + `created_at` desc | Customer booking history |
| `bookings` | `provider_id` + `slot.date` | Provider daily schedule |
| `bookings` | `status` + `slot.date` | Follow-up Agent query |

---

## 9. Reasoning Trace Specification

### 9.1 Trace Requirements Per Agent

| Agent | Required Trace Fields |
|---|---|
| Intent Agent | languages_detected, signals_extracted, complexity_rationale, confidence_path |
| Matching Agent | candidate_count, filters_applied, factor_scores_top3, recommendation_rationale, flags |
| Pricing Agent | demand_level, component_calculations, fairness_check, alternate_offer_logic |
| Scheduling Agent | conflicts_checked, buffer_applied, slot_confirmation, waitlist_logic (if applicable) |
| Booking Agent | artifacts_generated, notifications_dispatched, db_write_confirmation |
| Follow-up Agent | status_transitions, checklist_completion, feedback_collection_result |
| Dispute Agent | evidence_reviewed, dispute_type_classified, resolution_logic, escalation_trigger (if applicable) |

### 9.2 In-App Trace Viewer

- Accessible via "How did we decide this?" link on provider recommendation screen
- Accessible via "See reasoning" on price breakdown screen
- Accessible via "View dispute resolution steps" on dispute screen
- Trace shown as collapsible step-by-step list (not raw JSON)
- Raw JSON downloadable for competition evaluation

### 9.3 Trace Export Format

Traces are exported as a single JSON array per session:
```json
[
  { "trace_id": "...", "agent": "intent_agent", "timestamp": "...", ... },
  { "trace_id": "...", "agent": "matching_agent", "timestamp": "...", ... },
  ...
]
```

---

## 10. Mock Data Specification

### 10.1 Provider Dataset Requirements

- **Count:** 50 providers minimum
- **Cities:** Islamabad (30), Rawalpindi (20)
- **Service Categories:** AC Repair (15), Plumbing (10), Electrical (10), Cleaning (8), Electrical (7)
- **Rating Distribution:** Realistic bell curve (mean 4.1, SD 0.6; range 2.8–4.9)
- **On-Time Score Distribution:** Mean 82%, SD 12%
- **Cancellation Rate Distribution:** Mean 11%, SD 8%; outliers up to 30%
- **Price Tiers:** Low (30%), Mid (50%), Premium (20%)

### 10.2 Pakistani Data Authenticity Requirements

Provider names: common Pakistani names (Muhammad Tariq, Ayesha Bibi, Kamran Ali, Bashir Ahmed, etc.)

Sectors must be realistic: G-9, G-10, G-11, G-13, F-7, F-8, F-10, I-8, I-9, Saddar, Satellite Town, Bahria Town Phase 1–4, DHA Phase 1–2, Chakshehzad

Specialization tags in Roman Urdu + English: "Split AC Expert", "Inverter Specialist", "Gree/Haier Authorized", "Emergency 24/7"

### 10.3 Scenario-Specific Seed Data

The following scenarios must have dedicated seed data for demo:

| Demo Scenario | Required Seed Data |
|---|---|
| Happy path AC repair G-13 | Provider prov_001 (Usman Rana, 4.6 stars, G-13 area) |
| Baseline comparison | prov_003 (nearest but 23% cancellation, recent bad reviews) |
| No provider available | All G-11 providers marked busy on 2026-05-18 morning |
| High cancellation rate flag | prov_007 (3.1 stars, 28% cancellation, Matching Agent flags this) |
| Recent negative reviews | prov_005 (4.2 overall but 2 negative AC reviews last 14 days) |
| Surge pricing | F-7 area marked as high-demand (heat wave scenario) |
| Dispute: no-show | Booking KK-2026-DEMO1 with provider status never reaching ARRIVED |

---

## 11. Stress-Test Scenarios

Each scenario must be demonstrable in the app during evaluation.

### S1: No Provider Available in Requested Window
- **Trigger:** All providers busy in requested sector + time
- **Expected:** "Is waqt koi provider available nahi hai. Agle slot mein available hain: kal dopahar 2 baje (PKR 650). Waitlist mein add karein?"
- **Trace Shows:** Availability filter returns 0; waitlist logic activated

### S2: Provider Cancels After Confirmation
- **Trigger:** Provider sets status = CANCELLED after booking confirmed
- **Expected:** Customer notified within 60 seconds; auto-reschedule offered with next-best provider
- **Trace Shows:** Auto-reschedule engine activation; next provider selected with reason

### S3: Misspelled / Ambiguous Input
- **Input:** "ac kaam ni kar raha chahiye koi g elevan mein"
- **Expected:** Confidence = 0.71 → confirmation card shown: "AC repair, G-11, today?" → customer confirms
- **Trace Shows:** Misspelling correction ("g elevan" → "G-11"), confidence path

### S4: Two Users Request Same Provider Simultaneously
- **Trigger:** Two concurrent booking requests for prov_001 at same slot
- **Expected:** First request confirms; second sees "slot just taken" → offered next provider instantly
- **Trace Shows:** Atomic conflict detection; second request auto-routed

### S5: Customer Disputes Price After Service
- **Trigger:** Customer reports "provider charged PKR 1,200, quote was PKR 850"
- **Expected:** Dispute agent reviews quote vs. reported charge; overcharge > PKR 200 → auto-resolve in customer favor; refund PKR 350 credited
- **Trace Shows:** Evidence review, overcharge calculation, resolution logic

### S6: Provider Has High Rating but Recent Negative Reviews
- **Trigger:** Matching for AC repair returns prov_005 (4.2 overall, 2 negative AC reviews last 14 days)
- **Expected:** prov_005 ranked lower despite good overall rating; trace explicitly explains recency penalty
- **Trace Shows:** Recency decay calculation; competitor ranked higher on specialization + recent reviews

### S7: Google Maps API Failure
- **Trigger:** Inject Maps API timeout in demo
- **Expected:** "Approximate distance used (Maps unavailable)" shown to customer; sector centroid used; booking proceeds
- **Trace Shows:** API error detection, fallback to stored coordinates, user notification

### S8: User Preference Conflicts
- **Input:** Customer wants female provider + lowest price + AC complex job (very few female AC providers)
- **Expected:** Agent explains: "Is area mein female AC technician kal available nahi hain. Nearest option: male provider, 4.5 stars. Proceed karein?"
- **Trace Shows:** Preference conflict detection, trade-off explanation

### S9: Payment Confirmation Failure (simulated)
- **Trigger:** Demo toggle "payment failure" mode
- **Expected:** "Payment confirm nahi hui. Provider ko cash dein. Receipt ID: KK-2026-001847"
- **Trace Shows:** Payment failure flag, fallback instruction triggered

---

## 12. Baseline Comparison

### 12.1 Rule-Based System (Baseline)

The baseline system uses a single rule: **rank by distance only**.

```python
# Baseline matching
def baseline_match(providers, job_location):
    return sorted(providers, key=lambda p: distance(p.location, job_location))
```

### 12.2 KamKaaj Agentic System

Uses 8-factor weighted scoring with ADK/Gemini reasoning (see §5.2).

### 12.3 Comparison Results (Demo Scenario)

| Metric | Baseline (Distance Only) | KamKaaj (Agentic) |
|---|---|---|
| #1 Recommended Provider | prov_003 (nearest, 12 min) | prov_001 (14 min) |
| prov_003 Cancellation Rate | 23% | Not recommended (flagged) |
| prov_003 Recent AC Reviews | 2 negative (last 14 days) | Not recommended (flagged) |
| prov_001 On-Time Score | Not considered | 91% |
| prov_001 AC Specialization | Not considered | "Split AC Expert" |
| Estimated Rebooking Probability | High (23% cancel risk) | Low (8% cancel risk) |
| Reasoning Provided | None | Full step-by-step |

**Baseline vs. Agentic Decision Display:** In-app "Compare" screen shows both rankings side-by-side with explanation. This is a required competition deliverable shown in the demo video.

---

## 13. Non-Functional Requirements

### 13.1 Latency Targets

| Operation | Target P50 | Target P95 | Notes |
|---|---|---|---|
| Intent extraction | < 1.5s | < 3s | Gemini API call + extraction |
| Provider matching | < 2s | < 4s | DB query + Maps API + scoring |
| Price quote generation | < 1s | < 2s | Calculation only |
| Booking confirmation | < 2s | < 3.5s | Firestore write + notifications |
| Full flow (voice to confirmed) | < 60s | < 90s | End-to-end with user interaction |
| Dispute resolution (automated) | < 120s | < 180s | Evidence retrieval + decision |

### 13.2 Cost Estimates (per operation)

| Operation | API Calls | Estimated Cost |
|---|---|---|
| Intent extraction | 1x Gemini 1.5 Flash call (~500 tokens in, ~200 out) | ~$0.0005 |
| Provider matching | 1x Gemini 1.5 Flash + Maps Distance Matrix (≤9 elements × $0.005) | ~$0.048 |
| Price quote | 1x Gemini 1.5 Flash call | ~$0.0003 |
| Full booking flow (all agents) | ~8 Gemini 1.5 Flash calls + 2 Maps Distance Matrix calls | ~$0.055 |
| Dispute resolution | ~3 Gemini 1.5 Flash calls | ~$0.002 |
| **Per booking end-to-end** | | **~$0.06** |

*Gemini 1.5 Flash used for all agents. Upgrade to Pro only if Roman Urdu NLU accuracy is insufficient. Maps cost corrected from original ~$0.002 estimate (assumed 6 elements; actual up to 9). ETA refresh Maps cost excluded — scales with concurrent active bookings (~$0.25/min at 100 concurrent).*

### 13.3 Scalability Discussion

| Scale | Approach | Notes |
|---|---|---|
| 10x (current prototype) | Firebase auto-scales | No changes needed |
| 100x (~10,000 bookings/day) | Cloud Functions with concurrency limits; Firestore still sufficient | Maps API quota increase needed |
| 1,000x (~100,000 bookings/day) | Dedicated backend service; Firestore → Cloud Spanner or Postgres; Gemini batch API for non-real-time tasks | ADK agent pool with Cloud Run concurrency scaling |

**Bottlenecks to address at scale:**
1. Maps Distance Matrix API: batch requests; cache results for same-sector jobs
2. Gemini latency: implement response streaming for UX; cache common intent patterns
3. Provider availability: real-time index in Firestore (already designed for this)
4. **Known post-hackathon optimization:** Parallelize Matching + Pricing agents (currently sequential by design — see D2 decision). Saves 1–2s per booking; deferred to avoid async complexity during sprint.
5. Provider Agent 7AM batch: stagger at 1 provider/sec to avoid Cloud Function burst at 100x+ scale.

### 13.4 Photo Upload Constraints

| Constraint | Limit | Rationale |
|---|---|---|
| Max photo size | 5MB JPEG/PNG | Entry-level Android 3G upload time; prevents checklist blocking |
| Client-side compression | Required before upload | Target < 1MB; use `flutter_image_compress` |
| Retention | 90 days post-booking | Storage cost management; dispute window is 48h |
| Upload timeout | 30 seconds | If exceeded, checklist marked "photo pending" and retried without blocking status update |

### 13.5 Privacy and Data Handling
- All demo data is mock; no real personal data in prototype
- Location shared with provider only after booking confirmed, during active service window only
- CNIC numbers stored as hashed references in mock dataset
- Customer and provider profiles anonymized in reasoning trace exports

---

## 14. Deliverables Checklist

### Code Deliverables
- [ ] Flutter mobile app — Customer flow (intent → match → price → book → track → rate)
- [ ] Flutter mobile app — Provider dashboard (incoming jobs → accept → en-route → complete)
- [ ] Flutter mobile app — In-app Trace Viewer
- [ ] Google ADK agent implementations (7 LlmAgents + SequentialAgent pipeline + FunctionTools)
- [ ] Firebase Firestore schema + seed data
- [ ] Mock provider dataset (50+ entries, JSON)
- [ ] Stress-test scenario seed data (9 scenarios)

### Demo Materials
- [ ] 3–5 minute demo video (E2E: voice request → booking → dispute resolution)
- [ ] 2–3 minute screen recording (ADK agent orchestration, reasoning trace viewer)
- [ ] In-app baseline comparison screen (distance-only vs. agentic)

### Testing
- [ ] Flutter integration test suite: S1–S9 stress-test scenarios as automated tests
- [ ] NLU accuracy run on Appendix A test set (target: ≥ 90% correct service/location/urgency extraction)
- [ ] Agent mock layer: each agent has a mock Cloud Function returning canned JSON for offline frontend dev
- [ ] Dispute edge case tests: 48h boundary, double-dispute on same booking, booking never completed
- [ ] S4 concurrent conflict test: two simultaneous POSTs to `/v1/session/book` same provider+slot; one `confirmed`, one `SLOT_CONFLICT`

### Documentation
- [ ] README.md (architecture, setup, APIs, assumptions, cost/latency, scalability, limitations)
- [ ] ADK event/trace log export (JSON, covering all 7 agents for one full session)
- [ ] Baseline comparison document
- [ ] This PRD

### Evaluation Scenarios (Must be demonstrable live)
- [ ] S1: No provider available → waitlist
- [ ] S2: Provider cancels → auto-reschedule
- [ ] S3: Misspelled/ambiguous input → clarification → confirmation
- [ ] S4: Concurrent booking conflict → auto-route
- [ ] S5: Price dispute → automated resolution
- [ ] S6: High rating / recent negative reviews → recency penalty
- [ ] S7: Maps API failure → fallback
- [ ] S8: User preference conflict → trade-off explanation
- [ ] S9: Payment failure → cash fallback

---

## Appendix A: Multilingual Test Set (20 Inputs for Accuracy Evaluation)

| # | Input | Expected Service | Expected Location | Expected Urgency | Language | Expected Confidence Band |
|---|---|---|---|---|---|---|
| 1 | "AC bilkul kaam nahi kar raha" | AC Repair | (ask) | Unspecified | Roman Urdu | low — location missing |
| 2 | "I need a plumber today in F-8" | Plumbing | F-8 | Today | English | high ≥ 0.85 |
| 3 | "Bijli ki problem hai, G-11 mein kal subah" | Electrical | G-11 | Tomorrow morning | Urdu/Roman | high ≥ 0.85 |
| 4 | "Mujhe kal morning main AC service chahiye" | AC Service | (ask) | Tomorrow morning | Code-switch | medium — location missing |
| 5 | "ghar saaf karna hai aaj dopahar bahria town" | Cleaning | Bahria Town | Today afternoon | Roman Urdu | high ≥ 0.85 |
| 6 | "AC not cooling, need someone urgent, DHA Phase 2" | AC Repair | DHA Phase 2 | Urgent/today | English | high ≥ 0.85 |
| 7 | "ac kaam ni kar raha chahiye koi g elevan mein" | AC Repair | G-11 | Unspecified | Roman Urdu (misspelled) | medium → confirm card |
| 8 | "کل صبح پلمبر چاہیے I-8 میں" | Plumbing | I-8 | Tomorrow morning | Urdu script + English |
| 9 | "light nahi aa rahi, fuse problem lagti hai, Saddar" | Electrical | Saddar | Unspecified | Roman Urdu | high ≥ 0.85 |
| 10 | "AC theek nahi ho raha 2 din se, budget 1000 se kam" | AC Repair | (ask) | Flexible | Roman Urdu + constraint | medium — location missing |
| 11 | "carpenter chahiye, almari banana hai" | Carpentry | (ask) | Unspecified | Roman Urdu | low — location + time missing |
| 12 | "Tutor needed for O-levels maths, F-7" | Tutoring | F-7 | Unspecified | English + Roman Urdu | medium — time missing |
| 13 | "geyser band ho gaya, kal tak ho jaye" | Plumbing | (ask) | Tomorrow | Roman Urdu | medium — location missing |
| 14 | "Need AC service before Eid, G-9, budget reasonable" | AC Service | G-9 | Deadline | English + Roman Urdu | high ≥ 0.85 |
| 15 | "Electric shock aa raha hai socket se, abhi chahiye" | Electrical | (ask) | Urgent/now | Roman Urdu | medium — location missing |
| 16 | "deep cleaning 3 bedroom apartment" | Cleaning | (ask) | Unspecified | English | low — location + time missing |
| 17 | "AC ki gas khatam ho gayi, refill chahiye" | AC Repair | (ask) | Unspecified | Roman Urdu | medium — location missing |
| 18 | "water pump kaam nahi kar raha, G-13, urgent" | Plumbing | G-13 | Urgent | Roman Urdu | high ≥ 0.85 |
| 19 | "Bache ko math padana hai, online ya ghar" | Tutoring | (ask/online) | Unspecified | Roman Urdu | medium — ambiguous location |
| 20 | "AC installation krwani hai new unit, 1.5 ton" | AC Installation | (ask) | Unspecified | Roman Urdu | medium — location missing |

---

## Appendix B: Agent Interaction Sequence Diagram

```
Customer Input
     │
     ▼
[Intent Agent]
 ├── confidence ≥ 0.85 → JobRequest ──────────────────────┐
 ├── confidence 0.65–0.84 → Confirmation Card → Customer  │
 │    └── confirmed → JobRequest ──────────────────────────┤
 └── confidence < 0.65 → Clarifying Question → Customer   │
      └── answered → retry (max 2) → tap cards if still fails
                                                           │
     ┌─────────────────────────────────────────────────────┘
     ▼
[Matching Agent] → ProviderList
     │
     ▼
[Pricing Agent] → PriceQuotes
     │
     ▼
[Scheduling Agent]
 ├── slot available → AvailableSlot
 ├── conflict → next provider / alt slot
 └── no providers → waitlist
     │
     ▼
Customer Selects Provider + Confirms Price
     │
     ▼
[Booking Agent] → BookingConfirmation + Notifications
     │
     ▼
[Follow-up Agent] (async, service day)
 ├── En-route → ETA notification
 ├── Arrived → Checklist
 ├── Completed → Feedback prompt
 └── Rating received → Reputation update
     │
     ├── No dispute → CLOSED
     │
     └── Dispute raised
          │
          ▼
         [Dispute Agent]
          ├── Auto-resolved → Resolution notification
          └── Escalated → Human mediation flag
```

---

*KamKaaj PRD v1.0 — May 17, 2026 | Prepared for Google Antigravity Hackathon, Challenge 2*
*Internal — Team Use Only*

---

## 15. Epics and User Stories

Stories are written from three perspectives: **Customer (C)**, **Provider (P)**, and **Platform/Operator (O)**. Acceptance criteria describe observable behavior only — implementation detail lives in §5–§6. Each story references the governing feature section for traceability.

---

### Epic E1 — Multilingual Service Request
*Goal: Any customer can express a service need in their natural language and have it understood correctly, without form-filling.*

---

**US-E1-1: Voice Request Initiation**
As a **customer**, I want to tap a microphone button and speak my request in Roman Urdu, Urdu script, or English so that I never need to type on a small keyboard.

Acceptance Criteria:
- [ ] Given the home screen, when I tap the mic button, audio recording starts within 500ms.
- [ ] Given I speak for up to 30 seconds, when I stop, transcribed text appears on screen within 2 seconds.
- [ ] Given transcription completes, the Intent Agent processes the text automatically with no additional tap required.
- [ ] Given a noisy environment causes a poor transcription, when confidence is low (< 0.65), a clarifying question is shown rather than silently proceeding.

*Ref: §6.1.1, §5.1*

---

**US-E1-2: Text Request in Any Language Mix**
As a **customer**, I want to type a request mixing Roman Urdu and English ("AC theek nahi, need someone today G-13") so that I can express myself naturally without switching input modes.

Acceptance Criteria:
- [ ] Given a text request with Roman Urdu, Urdu, English, or any mix, the Intent Agent extracts service type, location, urgency, and budget sensitivity.
- [ ] Given the extracted data, a structured summary card is shown to the customer before proceeding to matching.
- [ ] Given common misspellings ("g elevan" → "G-11", "ac kaam ni kar raha"), the system corrects and maps to canonical values.

*Ref: §6.1.2, §5.1*

---

**US-E1-3: Confidence Confirmation Loop**
As a **customer**, I want to confirm or correct what the app understood before it searches for providers so that I don't get matched for the wrong service.

Acceptance Criteria:
- [ ] Given confidence is between 0.65 and 0.84, a confirmation card shows extracted fields with a one-tap confirm and an edit option.
- [ ] Given confidence is below 0.65, a single targeted clarifying question is shown (not a generic "please repeat").
- [ ] Given two rounds of clarification still yield confidence < 0.65, three common-service tap cards are shown as a fallback.
- [ ] Given I confirm the extraction, the system proceeds to matching without asking again.

*Ref: §6.1.5, §5.1*

---

**US-E1-4: Job Complexity Classification**
As a **customer**, I want the app to automatically determine whether my job is basic, intermediate, or complex so that I'm matched with a provider who has the right skills and tools.

Acceptance Criteria:
- [ ] Given any valid service request, a complexity classification (basic / intermediate / complex) is computed before matching begins.
- [ ] Given classification is "complex," only providers with matching specialization level are surfaced in the ranked list.
- [ ] Given the complexity drives a price difference of ≥ PKR 200, the breakdown explicitly labels the complexity premium.

*Ref: §6.1.4, §5.1*

---

### Epic E2 — Provider Discovery and Matching
*Goal: Customers are shown the most reliable, appropriate provider — not just the nearest one.*

---

**US-E2-1: Multi-Factor Provider Ranking**
As a **customer**, I want the app to recommend providers based on reliability, specialization, and price — not just proximity — so that I get a provider who will actually show up and do the job well.

Acceptance Criteria:
- [ ] Given a job request, the Matching Agent scores all available providers on 8 factors: distance, availability, rating, review recency, on-time score, specialization, cancellation rate, and price tier.
- [ ] Given two providers are within 5 minutes travel of each other, the higher on-time + lower cancellation rate provider ranks first.
- [ ] Given a provider has a cancellation rate > 25%, a flag appears in the reasoning trace even if they rank in the top 3.

*Ref: §6.2.1, §5.2*

---

**US-E2-2: Provider Card with Recommendation Reason**
As a **customer**, I want to see a clear one-line reason why each provider was recommended so that I can make an informed choice without reading full reviews.

Acceptance Criteria:
- [ ] Given the ranked provider list, each card shows: name, rating, on-time %, specialization tags, estimated arrival, price range, and one recommendation reason sentence.
- [ ] Given I tap "Why this provider?", the full reasoning trace for that provider is displayed in plain language (not raw JSON).
- [ ] Given fewer than 3 providers are available, all available providers are shown with a message indicating limited availability.

*Ref: §6.2.2, §9.2*

---

**US-E2-3: Baseline vs. Agentic Comparison (Demo Mode)**
As a **competition evaluator**, I want to see a side-by-side comparison of distance-only matching versus KamKaaj's agentic matching so that I can assess the decision quality improvement.

Acceptance Criteria:
- [ ] Given demo mode is active, a "Compare" toggle shows the distance-only ranked list alongside the agentic ranked list.
- [ ] Given the demo seed data (prov_003 nearest but 23% cancellation, prov_001 farther but 8% cancellation), the agentic system recommends prov_001 and the baseline recommends prov_003.
- [ ] Given the comparison view, the rationale for each system's top pick is displayed.

*Ref: §12, §10.3*

---

**US-E2-4: Customer Preference Filtering**
As a **customer**, I want to specify preferences (e.g., female provider, specific brand specialization) so that my non-negotiable constraints are respected.

Acceptance Criteria:
- [ ] Given I specify a gender preference, only matching-gender providers are shown if available in the requested window.
- [ ] Given my preference reduces available providers to zero, the Matching Agent surfaces the conflict explicitly: "Is area mein female AC technician kal available nahi hain" with an alternative offer.
- [ ] Given a preference conflict, I can override the preference with one tap to expand results.

*Ref: §6.2.1, §11 S8*

---

### Epic E3 — Dynamic Pricing and Transparency
*Goal: Every customer sees a fair, itemized quote before confirming. Every provider earns a market-rate amount.*

---

**US-E3-1: Itemized Price Breakdown**
As a **customer**, I want to see exactly what I'm paying for — base fee, travel, urgency premium, discounts — before I confirm a booking so that I never feel overcharged.

Acceptance Criteria:
- [ ] Given a matched provider, the price quote shows every component: base fee, distance adjustment, urgency premium, complexity premium, surge (if any), budget discount, loyalty discount, and a PKR range total.
- [ ] Given a price-sensitive customer flag, a budget discount of PKR 100–200 is applied and labeled in the breakdown.
- [ ] Given the breakdown, the customer can see the quote in both Urdu and English without switching screens.

*Ref: §6.3.1, §5.3*

---

**US-E3-2: Provider Earnings Visibility**
As a **customer**, I want to know how much of my payment goes to the provider so that I feel I'm supporting a fair system.

Acceptance Criteria:
- [ ] Given a price quote, the provider's minimum earnings (after simulated platform fee) are displayed.
- [ ] Given the earnings are below market rate for the service category, the Pricing Agent flags this in the reasoning trace and adjusts the base upward.

*Ref: §5.3 (fairness_note field)*

---

**US-E3-3: Alternate Timing Offer**
As a **customer**, I want to see a cheaper option if I'm flexible on timing so that I can save money by scheduling a day or two later.

Acceptance Criteria:
- [ ] Given scheduling flexibility would save ≥ PKR 100 (by removing urgency premium), an alternate offer is shown on the price screen.
- [ ] Given I tap the alternate offer, the scheduling flow restarts with the new date/time pre-filled.
- [ ] Given surge pricing is active, the alternate offer must reference a non-surge window.

*Ref: §5.3 (alternate_offer field), §6.3*

---

**US-E3-4: Surge Price Disclosure**
As a **customer**, I want to know if surge pricing is active and why, so that I can choose to book later if I prefer.

Acceptance Criteria:
- [ ] Given demand is "high" or "surge," a clearly labeled surge badge appears on the price card with percentage.
- [ ] Given surge > 25%, a "Book later for lower price" prompt is shown alongside the booking CTA.
- [ ] Given a Maps API failure or demand data is unavailable, surge defaults to 0% and the breakdown notes "demand data unavailable."

*Ref: §6.3.2*

---

### Epic E4 — Scheduling and Booking
*Goal: Bookings are conflict-free, confirmed in under 3 minutes, and automatically handled when things go wrong.*

---

**US-E4-1: Confirmed Booking in Under 3 Minutes**
As a **customer**, I want my booking fully confirmed — with a receipt ID, provider assignment, and reminders set — within 3 minutes of starting my request so that I can get back to my day.

Acceptance Criteria:
- [ ] Given I submit a voice request, the full flow from input to BookingConfirmation screen completes in under 3 minutes (P95 < 90 seconds for system operations; remainder is user review time).
- [ ] Given booking is confirmed, a receipt ID (format KK-YYYY-XXXXXX) is displayed and persisted in Firestore.
- [ ] Given booking is confirmed, both customer and provider receive an in-app notification within 10 seconds.

*Ref: §6.5, §5.5, §13.1*

---

**US-E4-2: Double-Booking Prevention**
As a **customer**, I want assurance that my confirmed slot is actually mine — not promised to someone else simultaneously.

Acceptance Criteria:
- [ ] Given two concurrent booking attempts for the same provider and slot, exactly one succeeds; the other receives "slot just taken" and is auto-routed to the next ranked provider.
- [ ] Given auto-routing occurs, the customer sees no error message — only the new provider suggestion appears seamlessly.
- [ ] Given a provider's slot is confirmed, that slot is blocked in all subsequent availability queries immediately.

*Ref: §6.4.1, §5.4*

---

**US-E4-3: Booking Reminders**
As a **customer**, I want reminder notifications before my service so that I'm ready when the provider arrives.

Acceptance Criteria:
- [ ] Given a confirmed booking, a T−12h reminder is scheduled automatically.
- [ ] Given a confirmed booking, a T−1h reminder is scheduled automatically.
- [ ] Given the booking is cancelled before the reminder fires, reminders are cancelled.
- [ ] Given the provider is en route, an additional real-time ETA notification is sent.

*Ref: §5.5 (reminders_scheduled field)*

---

**US-E4-4: Waitlist When No Provider Available**
As a **customer**, I want to join a waitlist when no provider is available for my requested time so that I'm automatically notified and booked when a slot opens.

Acceptance Criteria:
- [ ] Given zero providers pass the availability filter, the app offers a waitlist signup with an estimated wait time.
- [ ] Given I join the waitlist, I receive a notification within 60 seconds of a slot opening.
- [ ] Given multiple customers are on a waitlist for the same provider, slot assignment is first-come-first-served within the same matching score band.
- [ ] Given a slot opens, the waitlist customer receives a 2-minute response window before the slot is offered to the next customer.

*Ref: §6.4.3, §5.4*

---

**US-E4-5: Auto-Reschedule on Provider Cancellation**
As a **customer**, I want to be automatically rescheduled with another provider if mine cancels after confirmation, without having to restart the entire booking flow.

Acceptance Criteria:
- [ ] Given a provider marks a confirmed booking as CANCELLED, the auto-reschedule engine triggers within 60 seconds.
- [ ] Given an equivalent provider is available for the same slot, the customer receives: "Naya provider: [Name], same time, same price."
- [ ] Given no equivalent provider is found, the customer is offered a full refund and waitlist signup.
- [ ] Given auto-reschedule succeeds, the cancelled provider receives a cancellation strike in their record.

*Ref: §6.4.4, §5.4*

---

**US-E4-6: Provider Job Request with Accept/Decline**
As a **provider**, I want to receive incoming job requests with key details and a 90-second window to accept or decline so that I can manage my day efficiently.

Acceptance Criteria:
- [ ] Given a job is matched to me, I receive a push notification with: service type, sector, time slot, and estimated earnings.
- [ ] Given I tap Accept within 90 seconds, the booking is confirmed and the job appears in my schedule.
- [ ] Given I do not respond within 90 seconds, the job is auto-assigned to the next ranked provider without penalty on the first occurrence.
- [ ] Given I decline three consecutive jobs in a session, I am soft-deprioritized in matching for 4 hours.

*Ref: §5.8, §6.8*

---

### Epic E5 — Service Day Management
*Goal: Both customer and provider have a guided, documented service experience from en-route to completion.*

---

**US-E5-1: En-Route ETA Tracking**
As a **customer**, I want to see my provider's estimated arrival time when they're on the way so that I can plan accordingly and don't have to guess or call.

Acceptance Criteria:
- [ ] Given the provider taps "En Route," the customer's screen transitions to an ETA view showing provider name, photo (avatar), and estimated minutes to arrival.
- [ ] Given ETA is displayed, it refreshes every 2 minutes.
- [ ] Given Maps API is unavailable during en-route, the ETA displays "Coming soon — Maps unavailable" rather than an error.

*Ref: §6.6.1, §5.6*

---

**US-E5-2: Arrival and Completion Checklist**
As a **provider**, I want a digital checklist on arrival and completion so that my work is documented and disputes can be resolved fairly.

Acceptance Criteria:
- [ ] Given I tap "I've Arrived," a checklist appears; I cannot mark status as ARRIVED without completing all checklist items.
- [ ] Given the checklist, at minimum one before-work photo must be uploaded.
- [ ] Given I complete the job, a completion checklist appears requiring a post-work photo and customer sign-off (in-app thumbs-up).
- [ ] Given the customer's sign-off, my earnings for the job are confirmed in real time on my earnings screen.

*Ref: §6.6.2, §5.6*

---

**US-E5-3: Post-Service Rating and Review**
As a **customer**, I want a simple rating prompt after service is complete so that I can give feedback without it feeling like a chore.

Acceptance Criteria:
- [ ] Given service status = COMPLETED, a rating prompt (1–5 stars) is sent 30 minutes later.
- [ ] Given I haven't rated after 24 hours, one reminder is sent and no more.
- [ ] Given I submit a rating below 2 stars, the Follow-up Agent flags the booking for a check-in within 24 hours.
- [ ] Given I leave a text or voice review, it is displayed on the provider's profile with a sentiment label (positive / neutral / negative).

*Ref: §6.6.3, §5.6*

---

### Epic E6 — Dispute Resolution
*Goal: Disputes are resolved automatically and fairly within minutes, with escalation available for edge cases.*

---

**US-E6-1: Reporting a No-Show**
As a **customer**, I want to report that my provider never showed up and receive an automatic resolution — not a support ticket — so that my time and money are respected.

Acceptance Criteria:
- [ ] Given I tap "Report Issue" → "Provider didn't show up," the Dispute Agent checks whether the provider's status ever reached ARRIVED and whether a photo was uploaded.
- [ ] Given no ARRIVED status and no photo, the dispute is auto-resolved: full refund issued + PKR 200 in-app credit + warning strike to provider.
- [ ] Given resolution, I see the outcome within 2 minutes with a clear message in Urdu.

*Ref: §6.7, §5.7*

---

**US-E6-2: Disputing a Price Discrepancy**
As a **customer**, I want to dispute a charge that is higher than my confirmed quote and receive an automatic refund of the excess so that I am protected against overcharging.

Acceptance Criteria:
- [ ] Given I report a price discrepancy and the overcharge exceeds PKR 200 versus the agreed quote, the system auto-resolves in my favor.
- [ ] Given overcharge ≤ PKR 200, the system prompts both parties for confirmation before resolving.
- [ ] Given resolution, the refund amount and rationale are shown to both customer and provider.

*Ref: §5.7 (dispute type: price_disagreement)*

---

**US-E6-3: Provider Perspective on Disputes**
As a **provider**, I want unfair disputes to be evaluated against the evidence I submitted (photos, checklists) so that I am not penalized for work I completed correctly.

Acceptance Criteria:
- [ ] Given a dispute is raised against me, I receive a notification explaining the claim and the evidence being reviewed.
- [ ] Given I uploaded before/after photos and completed the checklist, the Dispute Agent considers this evidence before issuing any penalty.
- [ ] Given the dispute is resolved in my favor, no cancellation strike or earnings deduction is applied.

*Ref: §5.7*

---

**US-E6-4: Escalation to Human Mediation**
As a **customer or provider**, I want unresolved disputes escalated to a human mediator rather than left in limbo so that complex situations are handled fairly.

Acceptance Criteria:
- [ ] Given either party declines the automated resolution, the dispute is escalated and flagged for human review.
- [ ] Given 48 hours pass without resolution, escalation is triggered automatically.
- [ ] Given escalation, both parties see: "Hamara team 24 ghanton mein aapse rabta karega."

*Ref: §5.7 (escalation_router tool)*

---

**US-E6-5: Provider Blacklisting**
As a **platform operator**, I want repeat offenders automatically flagged for suspension or blacklisting so that the provider pool quality is maintained without manual monitoring.

Acceptance Criteria:
- [ ] Given a provider accumulates 2 confirmed no-shows in 30 days, a warning flag is added to their record.
- [ ] Given 3 confirmed no-shows, the provider is automatically suspended for 7 days.
- [ ] Given 1 confirmed fraud or assault report, the provider is blacklisted pending human review.
- [ ] Given a blacklisted provider ID, they do not appear in any future matching results.

*Ref: §5.7 (blacklist_evaluator tool)*

---

### Epic E7 — Provider Dashboard and Earning Optimization
*Goal: Providers maximize income and minimize idle time through intelligent scheduling and demand visibility.*

---

**US-E7-1: Daily Optimized Schedule**
As a **provider**, I want to see my day's jobs ordered by route efficiency, not just booking time, so that I minimize travel and maximize jobs per day.

Acceptance Criteria:
- [ ] Given multiple confirmed jobs for a day, the provider dashboard shows them ordered by route-optimized sequence with estimated travel time between each.
- [ ] Given a new job is accepted mid-day, the schedule reorders automatically.
- [ ] Given the schedule is shown, I can tap any job to see the navigation link.

*Ref: §6.8.1, §5.8*

---

**US-E7-2: Earnings Visibility**
As a **provider**, I want to see my confirmed earnings for today, this week, and this month so that I can track my income without keeping manual records.

Acceptance Criteria:
- [ ] Given the earnings screen, today's confirmed, pending, and projected earnings are displayed separately.
- [ ] Given a job is marked COMPLETED and customer signs off, earnings for that job move from "pending" to "confirmed" within 60 seconds.
- [ ] Given a dispute is active on a job, that job's earnings show as "on hold" until resolution.

*Ref: §6.8.1*

---

**US-E7-3: Demand Forecasting and Positioning**
As a **provider**, I want to know which areas will have high demand tomorrow so that I can position myself to receive more bookings.

Acceptance Criteria:
- [ ] Given the demand forecast screen, tomorrow's high-demand sectors for my service categories are shown.
- [ ] Given I am available in a high-demand sector, I receive a suggestion: "Kal G-11 mein AC requests zyada hain — wahan available raho."
- [ ] Given demand data is computed, it is available in my dashboard by 8 PM the previous evening.

*Ref: §5.8 (demand_forecaster tool), §6.8.1*

---

**US-E7-4: Workload Balancing**
As a **provider**, I want fair distribution of incoming jobs so that new providers aren't starved while top-rated providers are flooded.

Acceptance Criteria:
- [ ] Given two providers with similar matching scores, the one with lower current daily load receives the next job request.
- [ ] Given a provider has reached 4 jobs for the day, further requests are not dispatched to them unless they have opted into "high-load mode."
- [ ] Given a provider sets "unavailable" for a time window, no requests are dispatched during that window.

*Ref: §6.8.2*

---

### Epic E8 — Trust and Safety
*Goal: Customers trust that providers are verified; providers trust that the platform is fair.*

---

**US-E8-1: Provider Verification Badge**
As a **customer**, I want to see a verification badge on provider profiles so that I know they have gone through an identity check before entering my home.

Acceptance Criteria:
- [ ] Given a provider has completed CNIC verification (simulated in prototype), a "Verified" badge appears on their profile card.
- [ ] Given a provider is unverified, no badge appears and they are ranked below verified providers with equivalent scores.
- [ ] Given I tap the badge, a brief explanation of the verification process is shown.

*Ref: §11 (Trust & Safety Framework in brief; CNIC simulated)*

---

**US-E8-2: Post-Service Safety Rating**
As a **customer**, I want to provide a separate safety rating after a service visit so that I can flag personal safety concerns independently of job quality.

Acceptance Criteria:
- [ ] Given the post-service feedback screen, a safety rating (thumbs up / thumbs down) is presented separately from the quality star rating.
- [ ] Given a thumbs-down safety rating, the booking is flagged internally for operator review.
- [ ] Given a confirmed safety incident, escalation to human review is triggered immediately regardless of the 48-hour dispute window.

*Ref: §11 (Platform Safety)*

---

### Epic E9 — Reasoning Transparency
*Goal: Every agent decision is explainable to customers and verifiable by competition evaluators.*

---

**US-E9-1: In-App Reasoning Explanations**
As a **customer**, I want to understand how the AI chose my provider and priced my job, without needing to read technical logs, so that I trust the system.

Acceptance Criteria:
- [ ] Given the provider recommendation screen, a "How was this chosen?" link is available.
- [ ] Given I tap the link, a plain-language step-by-step explanation is shown (not raw JSON).
- [ ] Given the price breakdown screen, a "How was this calculated?" link shows the demand level, urgency classification, and each pricing component with its value.

*Ref: §9.2*

---

**US-E9-2: Competition Evaluator Trace Export**
As a **competition evaluator**, I want to download a full JSON trace of all agent decisions for a complete booking session so that I can verify the reasoning quality and tool call coverage.

Acceptance Criteria:
- [ ] Given a completed or demo booking session, a "Export Trace" button produces a JSON file covering all 7 agents.
- [ ] Given the export, it contains: workplan, task_steps, observations, reasoning, decision, tool_calls, error_recovery, and final_outcome for each agent.
- [ ] Given a stress-test scenario was triggered (e.g., Maps failure, dispute), the error_recovery field in the relevant agent's trace is non-null and explains the fallback taken.

*Ref: §9.1, §9.3*

---

### Epic E10 — Robustness and Graceful Fallback
*Goal: The system handles real-world failures — API outages, ambiguous input, surge demand — without breaking the user experience.*

---

**US-E10-1: Maps API Failure Fallback**
As a **customer**, I want the app to continue working even when Google Maps is down, so that I can still book a provider with a reasonable distance estimate.

Acceptance Criteria:
- [ ] Given the Maps Distance Matrix API returns an error or times out (> 3s), sector centroid coordinates are used for distance estimation.
- [ ] Given fallback coordinates are used, the provider card shows "Approximate distance" and the price breakdown shows "Travel cost estimated."
- [ ] Given the fallback, booking flow continues without interruption.

*Ref: §6.9, §11 S7*

---

**US-E10-2: No Provider Available — Graceful Handling**
As a **customer**, I want a clear, helpful response when no provider is available rather than a dead end.

Acceptance Criteria:
- [ ] Given the Matching Agent returns zero results after all filters, the screen shows the next available slot across all providers, a waitlist option, and an alternate time suggestion.
- [ ] Given I choose an alternate time, the full booking flow resumes with no data re-entry.
- [ ] Given I choose the waitlist, I see my waitlist position and estimated wait time.

*Ref: §6.9, §11 S1*

---

**US-E10-3: Concurrent Slot Conflict — Invisible Recovery**
As a **customer**, I want that if two people tried to book the same slot simultaneously, my experience is uninterrupted — I simply see the next best option automatically.

Acceptance Criteria:
- [ ] Given a slot is taken by a concurrent request, I do not see an error message.
- [ ] Given auto-routing, the next ranked available provider's card is shown immediately with a note: "Provider just booked — here's the next best match."
- [ ] Given the routing, the new provider's price quote is recomputed and displayed.

*Ref: §6.4.1, §11 S4*

---

**US-E10-4: Payment Failure Fallback (Demo)**
As a **customer**, I want a clear instruction if payment confirmation fails so that the service is not lost due to a technical issue.

Acceptance Criteria:
- [ ] Given payment confirmation fails (demo mode toggle), the booking is not cancelled.
- [ ] Given failure, the customer sees: "Payment confirm nahi hui. Provider ko cash dein. Receipt ID: [KK-XXXX]."
- [ ] Given failure, the provider sees: "Payment unconfirmed — please collect cash."
- [ ] Given the failure, the Dispute Agent reasoning trace logs the failure event for future reference.

*Ref: §6.9, §11 S9*

---

### Epic and Story Summary

| Epic | Stories | Primary Persona | Key Competition Requirement |
|---|---|---|---|
| E1 — Multilingual Request | US-E1-1 to US-E1-4 | Customer | R1, R2, R10, R11 |
| E2 — Provider Matching | US-E2-1 to US-E2-4 | Customer | R3, R4, R5 |
| E3 — Dynamic Pricing | US-E3-1 to US-E3-4 | Customer, Provider | R6, R13 |
| E4 — Scheduling & Booking | US-E4-1 to US-E4-6 | Customer, Provider | R7, R12, R14 |
| E5 — Service Day Management | US-E5-1 to US-E5-3 | Customer, Provider | R15 |
| E6 — Dispute Resolution | US-E6-1 to US-E6-5 | Customer, Provider, Operator | R16 |
| E7 — Provider Optimization | US-E7-1 to US-E7-4 | Provider | R17 |
| E8 — Trust & Safety | US-E8-1 to US-E8-2 | Customer | Implicit in evaluation |
| E9 — Reasoning Transparency | US-E9-1 to US-E9-2 | Customer, Evaluator | R9, R19 |
| E10 — Robustness & Fallback | US-E10-1 to US-E10-4 | Customer | R18, R22 |


---

## 16. Design Specification

*All decisions in this section were resolved during the /plan-design-review pass on 2026-05-17. They are binding for Flutter implementation.*

### 16.1 Global Navigation Structure

**Customer App — Bottom Navigation (3 tabs):**
```
Root (Bottom Nav — always visible except during active booking flow):
├── [Home]     — Request new service (mic/text entry)
├── [Activity] — Active booking pinned top; past bookings below
└── [Profile]  — Account, settings

Request flow (full-screen, bottom nav hidden during flow):
Home → Conversational Feed → Provider Selection
     → Price Breakdown (bottom sheet) → Booking Confirmation
     → transitions to Activity tab on confirm
```

**Provider App — Bottom Navigation (3 tabs):**
```
Root (Bottom Nav):
├── [Jobs]     — Incoming requests + today's route-ordered schedule
├── [Earnings] — Today / Week / Month breakdown
└── [Profile]  — Availability toggle, CNIC status, ratings

Job Alert (overlay — full interrupt, no nav visible):
└── High-priority notification + in-app expandable banner
    Appears over any screen; dismisses to Jobs tab after accept/decline
```

### 16.2 Home Screen Hierarchy

Strict element priority (top to bottom):
1. **App bar** — KamKaaj logo (left) + current location sector (right, e.g. "G-13, Islamabad")
2. **Mic button** — Primary CTA, ~40% of screen height, centered. Label: "Apni zaroorat bolein"
3. **Text input** — Secondary, thin bar below mic. Placeholder: "Ya type karein..."
4. **Quick-service chips** — Tertiary row: AC Repair · Plumber · Electrician · Cleaning
5. **Bottom navigation bar**

Mic button is visually dominant. Text input does not compete. Quick chips are discovery, not primary.

### 16.3 Interaction State Specifications

| Screen | State | What the user sees |
|---|---|---|
| Home | Mic permission denied | System permission dialog shown first; if denied, mic button replaced by text-only input + toast: "Mic access nahi mila — type karein" |
| Activity tab | First-time (no bookings) | Warm empty state: illustration of a phone with a wrench, text "Abhi tak koi booking nahi — koi kaam karao!", primary CTA button "Naya kaam dhundein" linking to Home |
| Provider selection | Loading (ADK matching agent) | 3 skeleton provider cards (shimmer animation) with placeholder bars for name, rating, price. Label above: "Dhundh rahe hain..." |
| Booking confirmed | Between confirmation and en-route | Live status screen: provider avatar + name + rating + countdown to slot time + "Provider ko call karein" alias link |

### 16.4 Conversational UI Pattern

All agent interactions (Intent → Matching → Pricing → Booking → Follow-up → Dispute) use a **single unified chat-bubble feed**:

- **User input:** Right-aligned bubble (teal background, white text)
- **Agent response:** Left-aligned bubble (cream background, dark text)
- **Interactive cards:** Embedded within agent bubbles — not pop-ups, not modals. Examples:
  - Confirmation card: extracted fields with "Theek hai ✓" / "Badlo ✏" buttons
  - Provider selection: scrollable horizontal cards within the feed
  - Price breakdown: expandable card within the feed
  - Clarifying question: agent bubble with 2–3 tap-option chips below it

Booking confirmation triggers a full-screen celebration state before transitioning to Activity tab.

### 16.5 Urdu + Bidirectional Text Rendering

- **App direction:** LTR (standard Flutter `Directionality.ltr` for all chrome: nav, buttons, icons, layouts)
- **Urdu script strings:** Rendered RTL within their text containers using `TextDirection.rtl` in Flutter
- **Fonts:**
  - Latin / Roman Urdu: **Noto Sans** (supports all Latin characters used in Roman Urdu)
  - Urdu script (Naskh): **Noto Naskh Arabic** (simpler rendering than Nastaliq; reliable on Android 6.0+; supports all Urdu characters) — used for `breakdown_urdu` field, any Urdu-script agent responses, and Urdu-script notifications
- **Mixed-direction lines:** When a single string contains both Urdu script and numbers/Latin (e.g., "بیس فیس: ۵۰۰"), render the whole string RTL; numbers flip correctly under Unicode bidirectional algorithm
- **Do not flip the whole app layout** — only text containers with confirmed Urdu script content get `TextDirection.rtl`

### 16.6 Visual Design Tokens

**Color System (CSS/Flutter token names):**
```
--color-primary:     #1B6B6B   /* Deep teal — trustworthy, Pakistani morning */
--color-primary-dark:#134F4F   /* Pressed/active state */
--color-accent:      #E8A838   /* Warm amber — CTAs, pricing highlights, earnings */
--color-bg:          #F7F4EF   /* Warm cream background — easier on outdoor eyes */
--color-surface:     #FFFFFF   /* Card surfaces */
--color-text-primary:#1A1A1A   /* Near-black — high contrast */
--color-text-secondary:#5C5C5C /* Secondary text */
--color-error:       #C0392B   /* Error states */
--color-success:     #27AE60   /* Confirmed/completed states */
```

**Typography Scale:**
```
Display  (hero numbers, earnings): 48sp, Noto Sans Bold
Title    (screen headers):          22sp, Noto Sans SemiBold
Subtitle (provider name, section):  16sp, Noto Sans Medium
Body     (descriptions, reasoning): 14sp, Noto Sans Regular     ← minimum body size
Caption  (metadata, timestamps):    12sp, Noto Sans Regular     ← minimum caption size
Urdu     (Naskh content):           16sp, Noto Naskh Arabic   ← minimum for readability
```

**Spacing Scale:** 4dp base unit. Common increments: 4, 8, 12, 16, 24, 32, 48dp.

**Border Radius:** Cards: 12dp. Buttons: 8dp. Chips: 20dp (pill). Do NOT use the same radius on everything.

### 16.7 Core Component Definitions

**Provider Card:**
```
┌─────────────────────────────────────┐
│ [48dp Avatar] Usman Rana    ★ 4.6  │  ← Title + rating (primary)
│               Split AC Expert       │  ← Specialization (secondary)
│                                     │
│  ETA: 14 min  |  PKR 750–850       │  ← ETA + price (primary)
│  ────────────────────────────────   │
│  ✓ Highest on-time score in G-13   │  ← Recommendation reason (tertiary)
│                                     │
│  [        View Full Quote        ]  │  ← Amber CTA button, full width
└─────────────────────────────────────┘
Touch target: entire card tappable. Min height: 120dp.
```

**Price Breakdown Card (in-feed):**
```
Expandable card within chat feed. Collapsed: shows total range only.
Expanded: itemized list (base, distance, urgency, discounts), fairness note,
alternate offer if applicable. Language toggle: show Urdu or English breakdown.
```

**Button System:**
- Primary: Amber (#E8A838) fill, dark text, 48dp height, 8dp radius
- Secondary: Teal (#1B6B6B) outline, teal text, 48dp height, 8dp radius
- Destructive: Red (#C0392B) fill, white text (dispute / cancel actions)
- Text: No background, teal text (tertiary actions)

### 16.8 Accessibility Requirements

| Requirement | Customer App | Provider App |
|---|---|---|
| Touch target minimum | 48 × 48dp | 48 × 48dp |
| Body text contrast | WCAG AA (4.5:1) | WCAG AA (4.5:1) |
| Critical elements contrast | — | 7:1 minimum (job alert timer, earnings figures — outdoor legibility) |
| Minimum font size (body) | 14sp | 14sp |
| Minimum font size (caption) | 12sp | 12sp |
| Screen reader | Semantic labels on all interactive elements | Semantic labels on all interactive elements |
| Dynamic text sizing | Support Android font size accessibility setting up to 1.3× | Support Android font size accessibility setting up to 1.3× |
| Job alert (provider) | — | High-priority notification + in-app expandable banner; audio chime + vibration |

### 16.9 Provider Job Alert UX

When a new job is dispatched to a provider:

1. **Android notification:** `PRIORITY_HIGH` notification with chime sound + vibration pattern (long-short-long). Appears on lock screen.
2. **In-app banner (if app open):** Full-width expandable banner slides down from top of any screen. Shows: job type, sector, estimated earnings, time slot, and 90-second countdown timer.
3. **Banner actions:** Two large buttons: "✓ Qabool karein" (Accept — amber) and "✗ Nahin" (Decline — outlined red). 48dp height minimum; swipe-to-accept not used (too easy to accidentally trigger on entry-level devices).
4. **Timeout:** Banner auto-dismisses with a "Waqt khatam" message when countdown reaches 0.
5. **Mid-checklist scenario:** If provider is in active checklist, banner overlays the checklist; provider can decline without leaving the checklist flow.

### 16.10 Rating UI

Post-service feedback: **5-star rating** (standard Android star widget). Selected star color: amber (#E8A838). Unselected: grey outline. Optional text/voice review below. Sent 30 minutes after COMPLETED status.

Internal mapping to reputation: 5★ = strong positive, 4★ = positive, 3★ = neutral, 1–2★ = negative (triggers Follow-up Agent check within 24h).

---

## 17. Design NOT in Scope (Deferred)

The following design decisions were considered during /plan-design-review and explicitly deferred:

| Decision | Rationale for deferral |
|---|---|
| Full KamKaaj design system / component library | Requires dedicated /design-consultation session; foundation tokens in §16.6 are sufficient for MVP |
| Onboarding flow (first-launch screens) | Not in competition demo scope; providers are pre-verified in mock data |
| Offline mode indicator | Low priority for competition demo in controlled wifi environment |
| Dispute photo upload UX | Use Flutter's default image picker for prototype |
| Receipt screen layout | Plain confirmation screen acceptable for competition demo |
| Dark mode | Not feasible for competition timeline; outdoor contrast spec (§16.8) mitigates |
| iPad / tablet layout | Flutter target is Android phone only for prototype |
| Animations / micro-interactions | Defer to post-competition polish pass |

---

## 18. What Already Exists (Design Leverage)

- **DESIGN.md** — Prior design review with scores and MUST FIX / SHOULD FIX / NICE FIX items. All MUST FIX items are now resolved in §16.
- **WhatsApp familiarity** — Both Ayesha and Usman are heavy WhatsApp users. The chat-bubble feed pattern in §16.4 directly leverages this familiarity. No learning curve for the conversational UI.
- **Material Design 3** — Flutter's default Material 3 components (bottom nav, cards, buttons) are acceptable starting points IF overridden with the token system in §16.6. Do not use Material defaults unchanged.

---

## Appendix C: Engineering Review Findings

*Engineering review completed: May 17, 2026. Findings applied inline to PRD. Log below for traceability.*

### Architecture Findings

| ID | Finding | Severity | Resolution |
|---|---|---|---|
| A1 | Agent tool definitions (gemini_nlp, provider_db_query, etc.) are custom ADK FunctionTools, not built-ins | High | Each tool implemented as a Python/Node function registered with ADK FunctionTool; added to §5 preamble |
| A2 | Sequential Matching → Pricing pipeline has 1–2s parallelization opportunity | Low | Deferred post-hackathon; noted in §13.3 |
| A3 | Session conversation_history has no persistence spec | High | Specified: stored as Firestore `sessions/{id}` document; client sends session_id on each turn; server holds history |
| A4 | Cloud Scheduler missing from tech stack for Provider Agent 7AM trigger | High | Added to §4.2 |
| A5 | Real-time update mechanism (WebSocket vs Firestore listeners) unspecified | Medium | Committed to Firestore `snapshots()` listeners; added to §4.3 |
| A6 | Firebase Storage absent from tech stack despite photo upload requirements | High | Added to §4.2 |
| A7 | Google STT Roman Urdu language model unspecified | High | Specified `ur-PK` model + Roman Urdu transliteration post-processing in §4.2 |
| A8 | ADK orchestration mode (auto vs sequential) was undefined | Medium | Specified: SequentialAgent wrapping 7 LlmAgents; top-level orchestrator routes via explicit pipeline |

### API / Schema Findings

| ID | Finding | Severity | Resolution |
|---|---|---|---|
| S1 | No auth headers in API contracts | High | Added §7.0 with Firebase Auth token convention |
| S2 | No error response schema | High | Added error schema to §7.0 with typed error codes |
| S3 | `provider_status_log` as unbounded array | Medium | Added subcollection note to §8.2; prototype may use array, production must use subcollection |
| S4 | Missing `updated_at` in Booking schema | High | Added to §8.2 |
| S5 | No Firestore index definitions | High | Added §8.5 with 5 composite indexes |
| S6 | `actual_price` update path undefined | High | Added `actual_price` field to `/v1/provider/status-update` with auto-dispute trigger note |
| S7 | Auto-reschedule loses original provider audit trail | Medium | Added `original_provider_id` + `provider_reassigned_at` to §8.2 |
| S8 | Sequential booking ID generation is a Firestore anti-pattern | Medium | Changed to UUID-based format `KK-<8-char alphanum>` with note in §8.2 |

### Test Coverage Findings

| ID | Finding | Severity | Resolution |
|---|---|---|---|
| T1 | No test deliverables in §14 | High | Added Testing section to §14 |
| T2 | Appendix A NLU test set lacks expected confidence bands | Medium | Added Expected Confidence Band column to all 20 rows |
| T3 | No ADK mock strategy for development | High | ADK supports local runner (`adk run --local`); added to §14 test deliverables |
| T4 | Dispute boundary conditions unspecified | Medium | Added edge cases to §14 test deliverables |
| T5 | S4 concurrent booking test needs precise timing spec | Low | Noted in §14: two simultaneous HTTP calls, assert one `confirmed` and one typed error code |

### Performance / Cost Findings

| ID | Finding | Severity | Resolution |
|---|---|---|---|
| P1 | Maps Distance Matrix cost understated 4.5x | High | Corrected §13.2: up to 9 elements per match = ~$0.045; per-booking total corrected to ~$0.06 |
| P2 | Gemini model tier unspecified | High | Specified Gemini 1.5 Flash throughout §13.2 |
| P3 | ETA refresh Maps cost excluded from estimates | Medium | Added explicit exclusion note to §13.2 with cost-at-scale figures |
| P4 | 7AM provider batch burst unaddressed | Low | Added stagger note to §13.3 |
| P5 | Photo upload size limit unspecified | Medium | Added §13.4 Photo Upload Constraints |
| P6 | Full-flow latency target (P95 < 90s) confirmed achievable | — | No change needed |
