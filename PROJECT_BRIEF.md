# KamKaaj — Project Brief
### AI-Powered Service Orchestrator for Pakistan's Informal Economy
**Version:** 1.0 | **Date:** May 17, 2026 | **Prepared for:** Team Review & Approval

---

## 1. Executive Summary

**KamKaaj** (کام کاج — "work done") is an AI-native mobile application that transforms how Pakistan's informal service economy connects, books, and executes work. Built on **Google Antigravity** as the core agentic orchestrator, KamKaaj handles the entire service lifecycle — from a voice note in Roman Urdu to provider matching, dynamic pricing, booking, live tracking, feedback, and dispute resolution — without requiring either party to navigate complex interfaces or speak formal English.

**Competition:** Google Antigravity Hackathon — **Challenge 2: AI Service Orchestrator for Informal Economy**

**Core Proposition:** A single conversational interface — voice or text, any language mix — that orchestrates the entire job: find the right person, agree on a fair price, book the slot, follow up, and resolve any issues. Antigravity agents handle the reasoning; humans just get the job done.

---

## 2. Problem Statement

### The Market Reality

Pakistan's informal service sector employs an estimated **30–40 million workers** — plumbers, electricians, AC technicians, tutors, beauticians, drivers, mechanics, and home-service providers. This is not a niche; it is the backbone of urban service delivery.

### The Broken Status Quo

| Pain Point | Customer Experience | Worker Experience |
|---|---|---|
| **Discovery** | WhatsApp groups, word-of-mouth, untrusted strangers | No visibility beyond existing referral network |
| **Trust** | No verification, no reviews, no accountability | Unverified credentials, prone to low-balling |
| **Pricing** | Opaque, negotiated on the spot, easily exploited | Race to the bottom, underpaid for complexity |
| **Scheduling** | Double-booked, no-shows, zero reminders | Idle time, missed opportunities, poor route planning |
| **Follow-up** | Zero; no recourse for bad service | No reputation building mechanism |
| **Language** | Platforms built for English/formal Urdu | Cannot navigate app-heavy solutions |

### Root Causes
1. **Literacy barrier** — Many workers and semi-literate customers can't navigate form-heavy apps
2. **Voice-first behavior** — Natural preference for spoken/chat communication
3. **Trust deficit** — No credential verification, no reputation infrastructure
4. **Coordination overhead** — Every booking is a manual, multi-step negotiation
5. **Zero intelligence** — Existing workarounds (WhatsApp, OLX) have no matching, scheduling, or pricing intelligence

### The Opportunity
The gap is not lack of demand — it's lack of infrastructure. KamKaaj supplies that infrastructure through an agentic system that works in the languages and interaction modes people already use.

---

## 3. Solution Overview

### What KamKaaj Does

KamKaaj is an **agentic service orchestration platform** where Google Antigravity acts as the intelligent backbone. Users express what they need in any language or format; agents handle everything else.

```
User Says → [Antigravity: Intent Agent] → [Antigravity: Matching Agent]
         → [Antigravity: Pricing Agent] → [Antigravity: Scheduling Agent]
         → [Antigravity: Booking Agent] → [Antigravity: Follow-up Agent]
         → [Antigravity: Dispute Agent] → Resolution
```

Every agent action is traceable, explainable, and logged — satisfying competition requirements for reasoning traces.

### The Experience in One Sentence
A customer says "AC bilkul kaam nahi kar raha, kal subah G-13 mein technician chahiye, budget zyada nahi hai" — and within 60 seconds has a confirmed booking, a fair price breakdown, and a reminder scheduled. No form-filling. No phone calls.

---

## 4. Target Users (Personas)

### Persona 1: Ayesha — The Urban Household Manager
- **Age:** 35 | **City:** Islamabad, F-10 | **Device:** Midrange Android
- **Situation:** Manages home services for a family of 5. Husband travels frequently. Deals with maintenance issues independently.
- **Language:** Mix of Urdu and English (code-switching naturally)
- **Behavior:** Uses WhatsApp heavily; calls referred numbers; has been overcharged multiple times
- **Goal:** Find a trustworthy, fairly priced technician fast, without negotiating
- **Frustration:** "I don't know if this person is actually qualified or if the price is fair"
- **KamKaaj Value:** Voice request in Roman Urdu → verified, rated provider → transparent price → confirmed booking in under 2 minutes

### Persona 2: Usman — The AC Technician (Provider)
- **Age:** 28 | **City:** Rawalpindi | **Device:** Entry-level Android
- **Situation:** 4 years of experience; works through word-of-mouth and a local ustaz's referrals. Earns PKR 40,000–60,000/month with high variability.
- **Language:** Urdu; limited English literacy
- **Behavior:** Available on WhatsApp; idle 30–40% of working hours; no route planning
- **Goal:** Steady bookings, fair pay, build reputation beyond his local circle
- **Frustration:** "Kaam milta hai kabhi kabhi, khali baithna parta hai zyada waqt"
- **KamKaaj Value:** Dashboard with incoming jobs, optimized schedule, transparent earnings, CNIC-based verification badge

### Persona 3: Tariq — The Small Business Owner
- **Age:** 42 | **City:** Lahore | **Device:** iPhone (but WhatsApp-first)
- **Situation:** Runs a small retail shop; needs recurring maintenance (electrical, plumbing, cleaning). Has had bad experiences with unverified workers.
- **Language:** Urdu/English mix; moderately tech-literate
- **Goal:** Reliable, recurring service relationships; business receipts for expense tracking
- **Frustration:** "Every time I need someone I start from scratch"
- **KamKaaj Value:** Saved preferred providers, recurring booking, receipt generation, priority queue

### Persona 4: Zara — The Young Professional
- **Age:** 26 | **City:** Karachi | **Device:** iPhone, tech-savvy
- **Situation:** Rents an apartment; first time managing her own home. Doesn't know local workers.
- **Language:** Primarily English with occasional Urdu
- **Goal:** Quick, safe, rated service; doesn't want to negotiate
- **Frustration:** Afraid to let unverified strangers into her home
- **KamKaaj Value:** CNIC-verified workers, live location tracking during service, post-service safety rating

---

## 5. User Journey

### Customer Journey: "AC Repair in G-13"

```
1. REQUEST
   └─ User opens KamKaaj → taps mic or types
      "AC bilkul kaam nahi kar raha, kal subah G-13 mein technician chahiye, budget zyada nahi"
      [Voice input supported; multilingual NLU via Antigravity Intent Agent]

2. UNDERSTANDING (Antigravity: Intent Agent)
   └─ Extracts: Service=AC Repair, Severity=High, Location=G-13 Islamabad,
      Time=Tomorrow Morning, Budget=Price-Sensitive
   └─ Confidence score displayed; low confidence triggers clarifying question
   └─ Agent reasoning trace logged: "Identified 'kaam nahi' as malfunction indicator,
      'budget zyada nahi' as price sensitivity flag"

3. MATCHING (Antigravity: Matching Agent)
   └─ Queries provider pool with 8+ matching factors:
      distance/travel time, availability, AC specialization rating,
      on-time score, review recency, cancellation rate, price tier, capacity
   └─ Ranks providers; recommends Provider A (not nearest but highest reliability + AC specialization)
   └─ Trace shown: "Provider B is 1.2km closer but has 23% cancellation rate and
      2 recent negative AC reviews. Provider A selected."

4. PRICING (Antigravity: Pricing Agent)
   └─ Dynamic quote generated:
      Base visit fee: PKR 500
      + Distance adjustment: PKR 150
      + Urgency (next morning): PKR 200
      - Budget flag discount: -PKR 100
      = Total: PKR 750 (± PKR 100 based on fault complexity)
   └─ Breakdown shown in plain language (Urdu + English)
   └─ Alternative: "Agar 2 din baad ho sakta hai, PKR 600 mein ho jaye ga"

5. BOOKING (Antigravity: Booking Agent)
   └─ User confirms → Slot blocked on provider calendar
   └─ Double-booking check runs → Calendar updated
   └─ Confirmation: SMS/WhatsApp-style notification to both parties
   └─ Receipt ID generated; mock DB/spreadsheet updated
   └─ Reminder scheduled: 8 PM tonight + 7 AM tomorrow

6. SERVICE DAY (Antigravity: Follow-up Agent)
   └─ Provider marked "En route" → Customer notified
   └─ Live ETA displayed
   └─ Service completion checklist triggered for provider
   └─ Photo/video evidence placeholder available

7. FEEDBACK & REPUTATION (Antigravity: Quality Agent)
   └─ Post-service rating prompt sent to customer
   └─ Provider reputation score updated algorithmically
   └─ Review sentiment analyzed; matching weight updated for future jobs
   └─ Earning logged to provider dashboard

8. DISPUTE (if triggered) (Antigravity: Dispute Agent)
   └─ Customer reports issue → Agent reviews booking evidence, pricing, photos
   └─ Automated resolution attempted (refund/discount/re-service offer)
   └─ Escalation to human mediation if unresolved
   └─ Blacklist flag triggered if provider confirmed at fault
```

### Provider Journey: "Earning Optimization"

```
1. ONBOARDING
   └─ CNIC number + selfie verification (identity check)
   └─ Mobile number OTP
   └─ Skills declaration + certification upload (optional)
   └─ Service area + availability preferences set

2. DAILY DASHBOARD
   └─ Incoming job requests shown with estimated earnings
   └─ Antigravity suggests optimal time slots based on demand forecast
   └─ Route-optimized schedule shown if multiple jobs

3. JOB ACCEPTANCE
   └─ Accept/decline within 90 seconds
   └─ Auto-reassign triggers if declined or timeout

4. JOB EXECUTION
   └─ Navigation link provided
   └─ Checklist on arrival + completion
   └─ Earnings confirmed in real time

5. GROWTH
   └─ Reputation score visible; badges for reliability, specialization
   └─ Weekly earnings summary
   └─ Demand forecasts: "Kal G-11 mein AC requests zyada hain — wahan available raho"
```

---

## 6. Core Feature Set

### Must-Have (MVP for Competition)

| Feature | Description | Antigravity Agent |
|---|---|---|
| **Multilingual NLU** | Urdu, Roman Urdu, English, code-switching; confidence scoring | Intent Agent |
| **Voice Input** | Speech-to-text request initiation | Intent Agent |
| **Provider Matching** | 8+ factor ranking algorithm | Matching Agent |
| **Dynamic Pricing** | Demand, urgency, distance, complexity, loyalty | Pricing Agent |
| **Scheduling Intelligence** | Conflict prevention, travel buffers, waitlist, auto-reschedule | Scheduling Agent |
| **Booking Simulation** | Confirmation, calendar update, notification, receipt | Booking Agent |
| **Service Quality Loop** | En-route update, checklist, feedback, rating update | Follow-up Agent |
| **Dispute Resolution** | No-show, quality complaint, price disagreement, escalation | Dispute Agent |
| **Worker Load Balancing** | Fair job distribution, earning optimization, demand forecast | Provider Agent |
| **Reasoning Traces** | All agent decisions logged and viewable in-app | All Agents |
| **CNIC Verification** | Worker identity verification at onboarding | Trust Layer |
| **Fallback Handling** | No provider available, API failure, low confidence | All Agents |

### High-Impact Additions (if time permits)

| Feature | Description |
|---|---|
| **Confidence Score UI** | Visual indicator when NLU is unsure; clarifying questions |
| **Surge Pricing Indicator** | Show demand heat and explain price adjustments |
| **Preferred Provider** | Save and priority-book trusted workers |
| **Recurring Bookings** | Scheduled maintenance (monthly AC service, etc.) |
| **Receipt Generation** | PDF receipt for business expense tracking |
| **Provider Badges** | Verified, Top-Rated, AC Specialist, etc. |
| **Demand Forecast Map** | Show providers where demand is high tomorrow |

---

## 7. Antigravity Architecture

### Agent Topology

```
┌─────────────────────────────────────────────────────┐
│                  KamKaaj Mobile App                  │
│          (Flutter — iOS & Android)                   │
└──────────────────────┬──────────────────────────────┘
                       │ API
┌──────────────────────▼──────────────────────────────┐
│              Google Antigravity Orchestrator          │
│                                                       │
│  ┌─────────────┐  ┌──────────────┐  ┌─────────────┐ │
│  │ Intent Agent│  │Matching Agent│  │Pricing Agent│ │
│  │ (NLU/NLP)   │  │(Multi-factor)│  │(Dynamic)    │ │
│  └──────┬──────┘  └──────┬───────┘  └──────┬──────┘ │
│         │                │                  │        │
│  ┌──────▼──────┐  ┌──────▼───────┐  ┌──────▼──────┐ │
│  │Scheduling   │  │ Booking      │  │ Follow-up   │ │
│  │   Agent     │  │   Agent      │  │   Agent     │ │
│  └──────┬──────┘  └──────┬───────┘  └──────┬──────┘ │
│         │                │                  │        │
│  ┌──────▼──────┐  ┌──────▼───────┐          │        │
│  │ Dispute     │  │ Provider     │          │        │
│  │   Agent     │  │   Agent      │◄─────────┘        │
│  └─────────────┘  └──────────────┘                   │
└──────────────────────┬──────────────────────────────┘
                       │
        ┌──────────────┼──────────────┐
        │              │              │
┌───────▼──────┐ ┌─────▼─────┐ ┌─────▼──────┐
│ Google Maps/ │ │ Provider  │ │ Gemini LLM │
│ Places API   │ │ Mock DB   │ │ (language) │
└──────────────┘ └───────────┘ └────────────┘
```

### Technology Stack

| Layer | Technology |
|---|---|
| **Agentic Orchestration** | Google Antigravity |
| **Language Model** | Gemini (via Antigravity) |
| **Mobile App** | Flutter (cross-platform iOS + Android) |
| **Maps & Location** | Google Maps / Places API |
| **Provider Data** | Mock dataset (realistic Pakistani service worker data) |
| **Voice Input** | Google Speech-to-Text |
| **Notifications** | Firebase Cloud Messaging (simulated SMS/WhatsApp) |
| **Backend** | Firebase / Google Cloud (lightweight) |
| **Trace Logging** | Antigravity native trace export + in-app viewer |

---

## 8. Competition Requirements Mapping

| Requirement | KamKaaj Approach |
|---|---|
| Working mobile prototype | Flutter app, iOS + Android |
| 3–5 min demo video | Full E2E walkthrough: voice request → booking → dispute |
| Antigravity reasoning traces | In-app trace viewer + exported logs for judges |
| README / documentation | Architecture, schema, APIs, assumptions, cost/latency |
| Baseline comparison | Show rule-based distance-only matching vs. KamKaaj 8-factor matching |
| Robustness evidence | No-show scenario, low-confidence language, API failure fallback |
| Cost and scalability note | Per-operation cost estimate; 10x/100x discussion |
| Antigravity as main orchestrator | All workflows controlled by Antigravity agents |
| Multilingual handling | Urdu + Roman Urdu + English + code-switching demonstrated |
| 6+ matching factors | 8 factors: distance, availability, rating, review recency, on-time score, specialization, price, cancellation rate |
| Scheduling intelligence | Double-booking prevention, travel buffers, waitlist, auto-reschedule |
| Dynamic pricing | Demand + urgency + distance + complexity + loyalty |
| Dispute workflow | No-show, quality complaint, price dispute, escalation |
| Worker optimization | Load balancing, demand forecast, earning optimization |

---

## 9. Evaluation Criteria Alignment

| Criterion | Weight | KamKaaj Strength |
|---|---|---|
| **Antigravity Integration** | 20% | All 7 agents built on Antigravity; reasoning traces exportable and viewable in-app |
| **Matching & Decision Quality** | 25% | 8-factor ranking; agent explains every provider recommendation in plain Urdu/English |
| **Multilingual Robustness** | 15% | Voice + text; Urdu/Roman Urdu/English/code-switch; confidence scoring; clarifying questions |
| **Scheduling, Pricing, Workflow** | 15% | Scheduling conflict prevention, dynamic pricing breakdown, full booking simulation |
| **Dispute & Reliability** | 15% | Automated dispute resolution; 5 stress-test scenarios demonstrated |
| **Innovation & UX** | 10% | Voice-first in local language; CNIC verification; provider earnings optimization; reasoning trace UI |

---

## 10. Stress-Test Scenarios (for Demo)

| Scenario | How KamKaaj Handles It |
|---|---|
| No provider available in requested window | Agent suggests next available slot, offers waitlist, notifies when slot opens |
| Provider cancels after confirmation | Auto-reschedule agent triggers; finds next-best provider; customer notified in 60s |
| Misspelled / ambiguous input | Confidence score displayed; agent asks targeted clarifying question |
| Two users request same provider simultaneously | Scheduling agent blocks second booking; offers alternative provider |
| Customer disputes price after service | Dispute agent reviews booking evidence; offers partial refund or re-service |
| Provider has high rating but recent negative reviews | Matching agent flags recency penalty; ranking adjusted; trace shows reasoning |
| Google Maps API failure | Fallback to stored location data; distance estimated; user notified of approximation |

---

## 11. Trust & Safety Framework

### Worker Verification (CNIC-Based)
- **CNIC number capture** at onboarding (mandatory)
- **Selfie verification** matched against CNIC photo (simulated for prototype)
- **Mobile OTP** for contact verification
- **Verification badge** displayed on provider profile
- **Skill declaration** with optional certification upload

### Platform Safety
- Customer can share live service session with a trusted contact
- Post-service safety rating (separate from quality rating)
- Automatic blacklist trigger on confirmed fraud or assault report
- All disputes escalated after 48 hours to human mediation

### Data Privacy
- CNIC data encrypted at rest; not exposed in API responses
- Location shared only during active service window
- Mock data used for all demo; no real personal data in prototype

---

## 12. Key Differentiators

| vs. Existing Alternatives | KamKaaj Advantage |
|---|---|
| **vs. WhatsApp/Phone calls** | Structured booking, verified workers, pricing transparency, receipt |
| **vs. OLX / local listing apps** | Intelligent matching, not just listings; scheduling; dispute resolution |
| **vs. Urban Company / Homepro** | Works in local language/voice; designed for Pakistan's informal market dynamics; Antigravity-native |
| **vs. Simple rule-based apps** | Agentic reasoning that adapts to context, explains decisions, handles edge cases |

---

## 13. Assumptions & Constraints

### Assumptions
- Providers own a basic Android phone with WhatsApp
- Customers have 3G/4G access; app works on 2G with degraded experience
- Mock provider dataset represents realistic Islamabad/Rawalpindi/Lahore service workers
- Payment is cash-on-service (digital payment integration is out of scope for prototype)
- CNIC verification is simulated (real NADRA API integration noted as future work)

### Constraints
- Demo uses mock provider data; no real booking occurs
- Maps API usage capped at free tier for prototype
- SMS/WhatsApp notifications are simulated in-app; no real message delivery
- Prototype targets Islamabad geography; scaling to other cities is architecture-ready but not demoed

---

## 14. Success Metrics (for Evaluation)

| Metric | Target |
|---|---|
| Intent extraction accuracy | > 90% on test set of 20 multilingual inputs |
| Provider match relevance | Top-3 recommendation accepted in demo scenarios |
| Booking completion | < 3 minutes from voice request to confirmed booking |
| Dispute resolution | Automated resolution demonstrated for 3/5 dispute types |
| Reasoning trace coverage | 100% of agent decisions logged with rationale |
| Stress test pass rate | 5/7 scenarios handled with graceful fallback |

---

## 15. Deliverables Checklist

- [ ] Flutter mobile app (iOS + Android) — functional prototype
- [ ] Google Antigravity agent implementation (7 agents)
- [ ] Multilingual NLU demo (5+ test scenarios)
- [ ] Provider mock dataset (50+ realistic entries)
- [ ] 3–5 minute demo video (E2E workflow)
- [ ] Antigravity trace/log export (readable by judges)
- [ ] README with architecture, schema, APIs, cost, limitations
- [ ] Baseline comparison document (rule-based vs. agentic)
- [ ] 7 stress-test scenario demonstrations
- [ ] CNIC verification flow (simulated)

---

## 16. Next Steps

| Phase | Action | Owner | Timeline |
|---|---|---|---|
| **Approval** | Share brief with team; get sign-off on scope | PM | Day 1 |
| **Competitor Benchmarking** | Analyst benchmarks Urban Company, Homepro, OLX, TaskRabbit | Analyst | Day 1–2 |
| **PRD** | Full PRD with API contracts, data schemas, agent prompts, UI wireframes | PM + Tech Lead | Day 2–3 |
| **Provider Dataset** | Build mock dataset (50 providers, realistic Pakistani data) | Data | Day 2 |
| **Antigravity Setup** | Configure agents, test intent extraction | Backend | Day 2–3 |
| **Flutter Scaffold** | App shell, navigation, voice input | Mobile | Day 2–3 |
| **Agent Integration** | Connect all 7 agents to mobile app | Backend + Mobile | Day 3–5 |
| **Demo Prep** | Record video, prepare trace exports, write README | All | Day 5–6 |
| **Submission** | Final QA, submission package | PM | Day 7 |

---

## Appendix A: Naming & Branding Notes

**App Name:** KamKaaj (کام کاج)
- "Kaam" = work/task; "Kaaj" = purpose/work (Urdu/Hindi)
- Communicates: "your work, handled"
- Works in Urdu and English markets
- Tagline candidate: *"Kaam bolo, ho jaye"* (Say the work, it gets done)

---

## Appendix B: For the Analyst — Competitor Benchmarking Brief

Please benchmark the following against KamKaaj's feature set:

1. **Urban Company** (India/Pakistan) — structured home services
2. **Homepro.pk** — Pakistan home services
3. **Rozee.pk / OLX** — informal worker discovery
4. **TaskRabbit** (US) — gig-economy service matching
5. **Handyman.pk** — local competitor
6. **Bykea** (Pakistan) — delivery + services app

**Dimensions to benchmark:**
- Multilingual / vernacular support
- Matching intelligence (factors used)
- Pricing transparency
- Dispute resolution mechanism
- Worker verification / trust signals
- Scheduling capabilities
- AI / agentic features (if any)
- Voice input support
- Pakistan-market fit

---

*KamKaaj Project Brief v1.0 — Prepared for Google Antigravity Hackathon, Challenge 2*
*Confidential — Team Use Only*
