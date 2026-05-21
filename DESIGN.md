# KamKaaj Design Review

This is a comprehensive design review of the `KamKaaj/PRD.md` based on the `plan-design-review` workflow.

## Step 0A — Initial Design Rating

| Dimension | Score | What a 10 looks like for THIS plan |
|---|---|---|
| Information architecture | 7/10 | Clear navigation structure (e.g., tabs, menus) showing how users move between current bookings, past history, and the home screen. |
| User flow clarity | 8/10 | Flows are well-documented sequentially, but a 10 would include screen-by-screen state transitions. |
| Visual hierarchy | 4/10 | Explicit definitions of primary vs secondary information on key screens (e.g., on the Provider Card, what is the largest text? What color is the CTA?). |
| Interaction model | 6/10 | Clear interaction paradigms defined (e.g., bottom sheets for clarifying questions, full-screen for booking confirmation, swipe vs tap gestures). |
| Error states & edge cases | 9/10 | Very strong coverage in the PRD (Robustness section). A 10 would include visual descriptions of the fallback UI. |
| Accessibility | 3/10 | Defined color contrast ratios, minimum touch target sizes (e.g., 48x48dp), and support for dynamic text sizing and screen readers. |
| Consistency with existing patterns | 5/10 | Mentions "WhatsApp-style" but lacks a defined, internal design language for KamKaaj's own UI elements. |

## Step 0B — Design System Check

*   **Does a design system or component library exist?** No. This is a greenfield project, and the PRD does not define one.
*   **Flag:** A consistent visual language (color tokens, typography, spacing scale, component library) must be established before shipping new UI surfaces.

## Step 0C — Existing Design Leverage

*   **Existing components:** None yet.
*   **Established patterns:** The PRD mentions "WhatsApp-style UI" for messages and standard map views.
*   **To build new:** We genuinely need to build a design system foundation (colors, typography, spacing) and core reusable components (Provider Cards, Price Breakdown Bottom Sheets, Status Trackers).

---

## Step 0D & 0E — Detailed Critique & Fixes

### 1. Information Architecture (7/10)
*   **Problem:** The PRD outlines agent logic beautifully but fails to establish a global app navigation structure. Users need to know how to jump from an active booking to their history or profile.
*   **Fix:** Implement a standard Bottom Navigation Bar for both Customer and Provider apps to anchor the experience.
*   **Mockup:**
```text
┌─────────────────────────────┐
│                             │
│     [ Main Content ]        │
│                             │
│                             │
│ ─────────────────────────── │
│  [Home]  [Activity] [Profile]│
└─────────────────────────────┘
```

### 2. Visual Hierarchy (4/10)
*   **Problem:** Provider recommendation cards contain 6+ data points, leading to a cluttered UI. It is not clear what the user should prioritize reading.
*   **Fix:** Define a strict typographic scale. Primary: Price and Provider Name. Secondary: Rating & ETA. Tertiary: Recommendation reasoning. Use color solely for the CTA.
*   **Mockup (Provider Card):**
```text
┌─────────────────────────────────┐
│  [Avatar] Usman Rana     ★ 4.8  │
│  Split AC Expert                │
│                                 │
│  ETA: 14 min  |  PKR 750-850    │
│  ─────────────────────────────  │
│  ✓ Highest on-time score in G-13│
│                                 │
│  [      View Full Quote      ]  │
└─────────────────────────────────┘
```

### 3. Interaction Model (6/10)
*   **Problem:** The Intent Agent flow dictates "show extraction card" or "ask clarifying question", but the interaction mechanism is undefined.
*   **Fix:** Use a persistent conversational feed (chat interface) with inline interactive widgets for the Intent phase. This feels natural and preserves the context of the user's initial voice/text prompt. For pricing and checkout, use a modal bottom sheet that overlays the chat.

### 4. Accessibility (3/10)
*   **Problem:** Missing requirements for touch target sizes, color contrast (crucial for providers working outdoors), and dynamic type support.
*   **Fix:** Enforce WCAG AA contrast (minimum 4.5:1 for text). Mandate that all tap targets (buttons, list items, checkboxes) must be at least 48x48dp. 

### 5. Consistency with existing patterns (5/10)
*   **Problem:** The PRD relies on analogies ("WhatsApp-style", "PDF-style"). This leads to a fragmented interface when interpreted by different engineers.
*   **Fix:** Define a KamKaaj Design System. Standardize buttons (Primary, Secondary, Text) and typography scales (Display, Title, Body, Caption).

---

## Step 0F — Key Screens / Flows to Define

### Customer App
1.  **Home / Request Screen**
    *   **Entry point:** App launch.
    *   **Primary action:** Voice or text input field to state a problem.
    *   **Exit points:** Activity tab, Profile tab.
    *   **Edge cases:** Microphone permission denied, offline state.
2.  **Conversational Feed & Clarification (Inline)**
    *   **Entry point:** Submitting an ambiguous request.
    *   **Primary action:** Answer clarifying question / tap an option card.
    *   **Exit points:** Cancel request.
    *   **Edge cases:** Two rounds of failure → show static tap cards.
3.  **Provider Selection Screen**
    *   **Entry point:** Intent successfully understood.
    *   **Primary action:** Tap a provider to view quote.
    *   **Exit points:** Back to edit request.
    *   **Edge cases:** No providers available (shows Waitlist view).
4.  **Price Breakdown & Booking Confirmation (Bottom Sheet)**
    *   **Entry point:** Tapping a provider.
    *   **Primary action:** Swipe-to-confirm booking.
    *   **Exit points:** Close sheet, view alternate timing offer.
    *   **Edge cases:** Surge pricing (requires explicit acceptance check/badge).
5.  **Live Booking Tracker**
    *   **Entry point:** Booking confirmed and provider is en-route.
    *   **Primary action:** View ETA / Call provider.
    *   **Exit points:** Report Issue (Dispute), Cancel booking.
    *   **Edge cases:** Provider late, Maps API failure (fallback to static text).

### Provider App
1.  **Incoming Job Request (Alert)**
    *   **Entry point:** Dispatched by Matching Agent.
    *   **Primary action:** Swipe to Accept within 90s.
    *   **Exit points:** Decline.
    *   **Edge cases:** Time runs out (auto-declined and routed to next).
2.  **Provider Daily Dashboard**
    *   **Entry point:** App launch.
    *   **Primary action:** Start navigation to the next job.
    *   **Exit points:** View earnings, toggle availability.
    *   **Edge cases:** No jobs for the day, offline.
3.  **Service Execution & Checklist**
    *   **Entry point:** Provider marks status as ARRIVED.
    *   **Primary action:** Upload photo, check off items.
    *   **Exit points:** Cannot proceed to COMPLETED without finishing checklist.
    *   **Edge cases:** Camera permission denied, poor connection for upload.

---

## Output Format

### MUST FIX (blocks shipping)
1.  **Global App Structure:** Define the core navigation paradigm (e.g., Bottom Navigation Bar) for both Customer and Provider apps to prevent users from getting stuck in dead-end flows.
2.  **Provider Request Interaction:** Determine exactly how the 90-second incoming job request appears to the provider. It must be a high-priority, full-screen takeover with a loud chime to ensure it wakes the phone and isn't missed.

### SHOULD FIX (degrades UX)
3.  **Conversational UI Paradigm:** Standardize the Intent gathering phase into a conversational feed with rich interactive cards rather than abstract separate "screens." This makes the multi-lingual interaction feel seamless.
4.  **Card Visual Hierarchy:** Implement the typography and visual hierarchy for the Provider Card (as mocked up) to ensure price, rating, and ETA are easily scannable at a glance.
5.  **Accessibility Baseline:** Specify 48x48dp minimum touch targets and WCAG AA contrast (specifically for outdoor legibility for providers).

### NICE TO FIX (polish)
6.  **KamKaaj Design System:** Formalize color palettes, typography scales, and core components into a shared library to avoid relying on "WhatsApp-style" approximations. This will ensure the app feels premium and cohesive.

***
*Status: Review Complete. Awaiting resolution of MUST FIX items before design sign-off.*
