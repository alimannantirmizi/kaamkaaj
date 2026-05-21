# KamKaaj — Sprint Summary

## What We're Building
An AI-powered home services app for Pakistan. Customers voice-request a service (e.g. "AC kharab hai, G-13"), 7 AI agents handle intent → matching → pricing → scheduling → booking. Provider receives a job alert. Both sides track the service live.

---

## The Team (2 days, parallel)

| | Person | Builds | Folder |
|---|---|---|---|
| P1 | Flutter Lead | Entire mobile app (customer + provider screens) | `app/` |
| P2 | ADK Core | Intent + Matching + Pricing agents + API server | `adk/agents/intent,matching,pricing/` + `adk/main.py` |
| P3 | ADK Extended | Scheduling + Booking + Dispute + Follow-up agents + Firebase + mock data | `adk/agents/scheduling–provider_agent/` + `firebase/` + `data/` |
| P4 | Integration Lead | Contracts (frozen API + DB schemas), scenario data, Day 2 merge + QA | `contracts/` + `docs/` |

**No GitHub.** Each person builds in their own folder, ZIPs at end of Day 1, P4 merges on Day 2 morning.

---

## Day 1 — Parallel Build

**9–10am:** P4 only — creates `contracts/api_schemas.json`, `contracts/db_schemas.json`, and 4 mock JSON files. Shares immediately. Everyone else waits.

**10am–8pm:** All 4 work independently, zero overlap:
- P1 builds all Flutter screens against mock JSON files (no backend needed)
- P2 builds Intent → Matching → Pricing agents + mock server (live at localhost:8080 by 2pm)
- P3 builds Scheduling → Booking → Dispute agents + 52 provider records + Firebase rules
- P4 builds S1–S9 scenario seed data + README

Each person follows 3 mini-sprints with test checkpoints in between (see SPRINT_PLAN.md for exact steps and copy-paste Antigravity prompts).

---

## Day 2 — Integrate + Test + Ship

**9–10am:** P4 assembles one project from everyone's ZIPs, wires Flutter to real API, adds P3's agents to the pipeline.

**10am–12pm:** All 4 fix integration bugs together (Flutter ↔ backend).

**12–3pm:** Run 7 stress scenarios in order (S3, S1, S4, S5, S7, S2, S6) with copy-paste curl test commands.

**3–5pm:** Build release APK → install on physical device → record demo video → submit.

---

## How Antigravity Is Used

Every sprint starts with a copy-paste prompt that:
1. Declares who you are and what you own
2. Points to the exact PRD sections to read
3. Lists every file to create with exact specs
4. Gives rules (no edits outside your folder)

See SPRINT_PLAN.md for every prompt.

---

## Key Dependencies (in order)

1. P4 contracts → everyone (Day 1, 9am)
2. P3 providers.json → P2 matching agent (Day 1, 2pm)
3. P2 mock server → P1 can test against real-shaped data (Day 1, 2pm)
4. Day 2 merge → integration testing → APK

