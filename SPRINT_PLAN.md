# KamKaaj — 2-Day Build Plan
**3 builders + 1 integration lead | May 2026 | Google Antigravity Hackathon**

---

## Team Structure & Folder Ownership

| Person | Role | Owns |
|---|---|---|
| **P1** | Full Flutter App | `app/` (everything) |
| **P2** | ADK Core Pipeline | `adk/agents/intent/`, `adk/agents/matching/`, `adk/agents/pricing/`, `adk/pipeline.py`, `adk/main.py`, `adk/tools/`, `adk/mock_server.py` |
| **P3** | ADK Extended + Firebase + Data | `adk/agents/scheduling/`, `adk/agents/booking/`, `adk/agents/followup/`, `adk/agents/dispute/`, `adk/agents/provider_agent/`, `firebase/`, `data/` |
| **P4** | Contracts + Integration + QA | `contracts/`, `docs/` |

**Golden rule:** Never touch files outside your folder. Message the owner if you need something changed.

**P4 must finish Sprint 1 before anyone else starts.** Share the contracts ZIP immediately.

---

## Day 1 Timeline

| Time | P1 | P2 | P3 | P4 |
|---|---|---|---|---|
| 9–10am | WAIT for P4 contracts | WAIT for P4 contracts | WAIT for P4 contracts | **Sprint 1 — URGENT** |
| 10am–1pm | Sprint 1 | Sprint 1 | Sprint 1 | Sprint 2 |
| 1–2pm | Test Sprint 1 | Test Sprint 1 | Test Sprint 1 | — |
| 2–5pm | Sprint 2 | Sprint 2 | Sprint 2 | Sprint 3 |
| 5–6pm | Test Sprint 2 | Test Sprint 2 | Test Sprint 2 | — |
| 6–8pm | Sprint 3 | Sprint 3 | Sprint 3 | — |

## Day 2 Timeline

| Time | All |
|---|---|
| 9–10am | P4 assembles merged project |
| 10am–12pm | P1+P2+P3 fix integration bugs together |
| 12–3pm | Scenario testing (S1–S7 in order) |
| 3–4pm | Fix P0 bugs |
| 4–5pm | APK build + demo recording + submission |

---

---

# P4 — Contracts + Integration Lead

## P4 Sprint 1 — Contracts (Day 1, 9am–10am) ⚠️ EVERYONE WAITS FOR THIS

**Build:** All contract files + mock JSON files. Share the ZIP before 10am.

### Exact Antigravity Prompt — P4 Sprint 1

```
I am P4 on KamKaaj. I need to create the frozen contract files that everyone on the team will use.
Read docs/PRD.md section 7 (API Contracts) and section 8 (Data Schemas) carefully.

Create these files exactly matching the PRD. Do not invent fields — copy from the PRD verbatim.

1. contracts/api_schemas.json
   Include all 6 endpoints with full request and response schemas:
   - POST /v1/session/intent
   - POST /v1/session/match
   - POST /v1/session/book
   - POST /v1/dispute/raise
   - POST /v1/provider/job-response
   - POST /v1/provider/status-update
   Also include the shared error response schema from PRD §7.0.
   Format: { "endpoints": { "<method> <path>": { "request": {...}, "response": {...} } } }

2. contracts/db_schemas.json
   Include all Firestore collection schemas from PRD §8:
   - Provider (with all fields from PRD §8.1)
   - Booking (with all fields from PRD §8.2)
   - Session (with all fields)
   - JobRequest (with all fields)
   - DisputeCase (with all fields)
   Format: { "collections": { "<name>": { "fields": {...} } } }

3. app/assets/mock/intent_response.json
   A realistic example response for POST /v1/session/intent.
   Use this scenario: customer says "AC theek nahi, G-13, kal subah"
   confidence_score: 0.92, all fields filled, service_type: "AC_REPAIR"
   Include reasoning_trace with all fields from PRD §9.1 for the intent agent.

4. app/assets/mock/match_response.json
   A realistic example response for POST /v1/session/match.
   Include 3 providers: prov_001 (ranked 1st), prov_005 (2nd), prov_003 (3rd, with risk warning).
   Include reasoning_trace showing the 8-factor scoring.

5. app/assets/mock/price_response.json
   A realistic example for the pricing response.
   Service: AC repair. Base: PKR 2500. Show itemized breakdown.
   Include provider_earnings line (PRD §5.3).

6. app/assets/mock/booking_response.json
   A confirmed booking response.
   booking_id: "KK-A3F92B1D", status: "CONFIRMED"
   Include all fields a booking confirmation screen needs.

After creating all files, print a summary of what was created.
```

### Test after P4 Sprint 1

```bash
# Verify all files exist
ls contracts/
ls app/assets/mock/

# Validate JSON is valid
python3 -c "import json; json.load(open('contracts/api_schemas.json')); print('api_schemas OK')"
python3 -c "import json; json.load(open('contracts/db_schemas.json')); print('db_schemas OK')"
python3 -c "import json; json.load(open('app/assets/mock/intent_response.json')); print('intent mock OK')"
python3 -c "import json; json.load(open('app/assets/mock/match_response.json')); print('match mock OK')"
python3 -c "import json; json.load(open('app/assets/mock/price_response.json')); print('price mock OK')"
python3 -c "import json; json.load(open('app/assets/mock/booking_response.json')); print('booking mock OK')"
```

**Pass criteria:** All 6 JSON files valid, no parse errors.
**Then:** ZIP and share to group WhatsApp/Drive immediately. Ping team: "Contracts ready ✅"

---

## P4 Sprint 2 — Scenario Data + README (Day 1, 10am–6pm)

**Build:** S1–S9 scenario JSON files, seed script structure, README skeleton.

### Exact Antigravity Prompt — P4 Sprint 2

```
I am P4 on KamKaaj. Read docs/PRD.md sections 10, 11, and 8.
Read contracts/db_schemas.json.

I need to create the stress-test scenario seed data and project documentation.

1. data/scenarios/s1_no_provider.json
   Scenario: All providers in G-11 sector are busy (status: "BUSY" or availability: false).
   Expected result: waitlist offer is shown to customer.
   Create Firestore state: all G-11 AC_REPAIR providers have no available slots for tomorrow.

2. data/scenarios/s2_provider_cancels.json
   Scenario: prov_001 cancels after booking is CONFIRMED.
   Firestore state: booking KK-TEST-S2 exists, status CONFIRMED, provider_id prov_001.
   Expected: auto-reschedule to prov_005 within 60s.

3. data/scenarios/s3_misspelled_input.json
   Scenario input text: "ac kaam ni kar raha chahiye koi g elevan mein"
   Expected: confidence 0.65-0.84, confirmation card shown, "G-11" corrected from "g elevan"

4. data/scenarios/s4_concurrent_booking.json
   Scenario: Two booking requests hit simultaneously for prov_001 slot "2026-05-19 10:00".
   Expected: first returns CONFIRMED, second returns SLOT_CONFLICT error.

5. data/scenarios/s5_price_dispute.json
   Firestore state: completed booking, agreed_price PKR 2500, customer claiming PKR 2850 charged.
   Expected: Dispute Agent auto-resolves in customer favour, issues PKR 350 credit.

6. data/scenarios/s6_recency_penalty.json
   Firestore state: prov_005 has 2 reviews from last 14 days with rating 1 and 2.
   prov_005 overall_rating: 4.2, but recent_negative_count: 2.
   Expected: prov_005 ranked below a provider with 3.9 overall but no recent negatives.

7. data/scenarios/s7_maps_failure.json
   Scenario: Maps API returns timeout/error for all provider distance requests.
   Expected: fallback to sector centroid distances, UI shows "Approximate distance".

8. data/scenarios/s8_preference_conflict.json
   Customer preference: female_provider_only: true. Service: complex AC repair.
   Firestore state: no female AC repair providers available.
   Expected: Dispute/matching agent explains trade-off, offers override option.

9. data/scenarios/s9_payment_failure.json
   Scenario: payment_method: "CARD", card_declined: true.
   Expected: booking proceeds, receipt shows "Confirm cash payment with provider".

10. data/seed.py
    Script that reads data/providers.json and writes all 52 providers to Firestore.
    Also accepts --scenario flag: python seed.py --scenario s1 loads s1 Firestore state.
    Use firebase-admin SDK. Collection: "providers". Also seeds "bookings" for scenarios.
    Print progress: "Seeding provider 1/52..." etc.

11. docs/README.md
    Sections:
    - Project Overview (2 sentences)
    - Architecture Diagram (text/ASCII from PRD §4.1)
    - Setup Instructions (step by step: clone, flutter pub get, pip install, firebase setup, seed data)
    - Running Locally (commands to start backend + flutter app)
    - API Documentation (list all 6 endpoints with brief description)
    - Stress Test Scenarios (table: S1-S9, description, how to run)
    - Team (4 names, roles)
```

---

## P4 Sprint 3 — Integration Assembly (Day 2, 9am–10am)

**Build:** Assemble everyone's ZIP into one working project, fix all import paths.

### Exact Antigravity Prompt — P4 Sprint 3

```
I am P4 on KamKaaj leading Day 2 integration. I have collected ZIP files from P1, P2, P3.
The project folder now has all subfolders merged.

Read docs/PRD.md §4 and §7.
Read contracts/api_schemas.json.
Read adk/main.py (P2's file).
Read adk/pipeline.py (P2's file).
Read app/lib/services/api_service.dart (P1's file).

I need to wire everything together:

1. In app/lib/services/api_service.dart:
   Find the baseUrl constant. Change it from the mock URL to:
   const baseUrl = 'http://localhost:8080';
   (or the Cloud Run URL if P2 has deployed)

2. In adk/pipeline.py:
   P2 wired intent → matching → pricing.
   Now add P3's agents: after pricing, add scheduling → booking in the SequentialAgent.
   Import them from their respective agent.py files.

3. In adk/main.py:
   P2 has POST /v1/session/intent, /v1/session/match, /v1/session/book.
   Now add P3's routes:
   - POST /v1/provider/status-update → calls followup_agent
   - POST /v1/dispute/raise → calls dispute_agent
   - POST /v1/provider/job-response → calls provider_agent

4. Verify all Python imports resolve. Run:
   cd adk && python -c "import main" 
   Fix any ImportError by checking the actual file paths.

5. Verify Flutter imports resolve. Run:
   cd app && flutter analyze
   Fix any "Target of URI doesn't exist" errors.

Print a summary of every change made.
```

### Test after P4 Sprint 3

```bash
# Backend starts clean
cd adk && python main.py &
sleep 3

# Happy path end-to-end curl test
curl -s -X POST http://localhost:8080/v1/session/book \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer test-token" \
  -d '{
    "session_id": "test-001",
    "user_input": "AC theek nahi, G-13, kal subah",
    "location": {"sector": "G-13", "city": "Islamabad"},
    "user_id": "test-user"
  }' | python3 -m json.tool

# Flutter builds
cd ../app && flutter analyze
flutter build apk --debug
```

**Pass criteria:** `booking_id` appears in curl response. Flutter analyze shows 0 errors (warnings OK).

---

---

# P1 — Full Flutter App

## P1 Sprint 1 — Foundation (Day 1, 10am–1pm)

**Build:** Flutter project setup, theme, services, home screen.

### Exact Antigravity Prompt — P1 Sprint 1

```
I am P1 on KamKaaj. I own the entire app/ folder. No one else touches app/.
Read docs/PRD.md sections 6.1, 15, 16.5, 16.6.
Read contracts/api_schemas.json — understand all 6 endpoints.
Read contracts/db_schemas.json — understand Provider, Booking, Session schemas.

The app is a Flutter mobile app for Pakistani home services.
Customer flow: voice request → AI processes → provider list → price → booking → tracking → rating.
Provider flow: receive job alert → accept → en route → complete.

Build these files in order:

━━━ FILE 1: app/pubspec.yaml ━━━
Add these dependencies (keep everything else):
  firebase_core: ^2.24.0
  cloud_firestore: ^4.14.0
  firebase_auth: ^4.16.0
  firebase_messaging: ^14.7.6
  speech_to_text: ^6.6.0
  google_fonts: ^6.1.0
  shimmer: ^3.0.0
  cached_network_image: ^3.3.0
  http: ^1.1.0

━━━ FILE 2: app/lib/theme/app_theme.dart ━━━
class AppTheme {
  static const Color primary = Color(0xFF1B6B6B);
  static const Color background = Color(0xFFF7F4EF);
  static const Color accent = Color(0xFFE8A838);
  static const Color textDark = Color(0xFF1A1A1A);
  static const Color textLight = Color(0xFF6B6B6B);
  static const Color error = Color(0xFFD32F2F);
  static const Color success = Color(0xFF388E3C);

  static ThemeData light() {
    return ThemeData(
      primaryColor: primary,
      scaffoldBackgroundColor: background,
      colorScheme: ColorScheme.fromSeed(seedColor: primary, background: background),
      textTheme: GoogleFonts.notoSansTextTheme(),
      elevatedButtonTheme: ElevatedButtonThemeData(
        style: ElevatedButton.styleFrom(
          minimumSize: const Size(double.infinity, 48),
          backgroundColor: primary,
          foregroundColor: Colors.white,
        ),
      ),
      outlinedButtonTheme: OutlinedButtonThemeData(
        style: OutlinedButton.styleFrom(minimumSize: const Size(double.infinity, 48)),
      ),
    );
  }

  // Urdu text wrapper — use this for ALL Urdu strings
  static Widget urdu(String text, {double fontSize = 16, Color? color}) {
    return Directionality(
      textDirection: TextDirection.rtl,
      child: Text(
        text,
        style: TextStyle(
          fontFamily: 'NotoNaskhArabic',
          fontSize: fontSize,
          color: color ?? textDark,
        ),
      ),
    );
  }
}

━━━ FILE 3: app/lib/services/api_service.dart ━━━
Service that loads from app/assets/mock/*.json for now (real URL wired on Day 2).

class ApiService {
  static const String baseUrl = 'MOCK'; // Day 2: change to real URL

  Future<Map<String, dynamic>> postIntent(String userInput, String sector) async {
    // For now: load from assets/mock/intent_response.json
    final String data = await rootBundle.loadString('assets/mock/intent_response.json');
    return json.decode(data);
  }

  Future<Map<String, dynamic>> postMatch(Map<String, dynamic> intentResult) async {
    final String data = await rootBundle.loadString('assets/mock/match_response.json');
    return json.decode(data);
  }

  Future<Map<String, dynamic>> postPrice(Map<String, dynamic> matchResult) async {
    final String data = await rootBundle.loadString('assets/mock/price_response.json');
    return json.decode(data);
  }

  Future<Map<String, dynamic>> postBook(Map<String, dynamic> priceResult, String providerId) async {
    final String data = await rootBundle.loadString('assets/mock/booking_response.json');
    return json.decode(data);
  }

  Future<Map<String, dynamic>> raiseDispute(String bookingId, String issueType, String description) async {
    await Future.delayed(const Duration(milliseconds: 500));
    return {'status': 'RECEIVED', 'dispute_id': 'DISP-001', 'estimated_resolution': '2 hours'};
  }

  Future<void> updateProviderStatus(String bookingId, String status) async {
    await Future.delayed(const Duration(milliseconds: 300));
  }
}

━━━ FILE 4: app/lib/services/auth_service.dart ━━━
Firebase Phone Auth service:
- Future<void> signInWithPhone(String phoneNumber, Function(String) onCodeSent)
- Future<UserCredential> verifyOTP(String verificationId, String smsCode)
- User? getCurrentUser()
- Future<void> signOut()
- Store user type in Firestore users/{uid}/type on first login ("customer" or "provider")

━━━ FILE 5: app/lib/services/firestore_service.dart ━━━
Typed Firestore wrapper:
- Future<Map<String, dynamic>?> getBooking(String bookingId)
- Stream<Map<String, dynamic>?> listenToBooking(String bookingId)
- Future<List<Map<String, dynamic>>> getProviderSchedule(String providerId, String date)
- Stream<List<Map<String, dynamic>>> listenToJobAlerts(String providerId)
- Future<void> updateBookingStatus(String bookingId, String status, {double? actualPrice})

━━━ FILE 6: app/lib/customer/home_screen.dart ━━━
Main customer home screen:
- AppBar: "KamKaaj" in primary color, Urdu subtitle "گھریلو خدمات" using AppTheme.urdu()
- Centre: circular mic button, 120dp diameter, color AppTheme.primary
  Tap to start recording. Tap again or silence after 3s to stop.
  Show pulsing animation while recording.
  Transcribed text appears below mic as user speaks.
- Below mic: TextFormField for typed input (placeholder: "Ya yahan type karein...")
- Horizontal scroll row of 4 service chips: "AC Repair", "Plumbing", "Electrical", "Cleaning"
  Each chip: rounded, border AppTheme.primary, tap fills the text field with service name
- Submit button (min 48dp): "Khidmat Dhoondein" — calls ApiService.postIntent()
  On tap: show loading spinner, load mock response, navigate to ProviderListScreen
- Bottom navigation bar: Home / Activity / Profile (3 tabs, customer only)

Use speech_to_text package. Handle microphone permission request.
Show SnackBar if microphone permission denied.

━━━ FILE 7: app/main.dart ━━━
Initialize Firebase. MaterialApp with AppTheme.light(). Routes for all screens.
Default route: HomeScreen.
```

### Test after P1 Sprint 1

```bash
cd app
flutter pub get
flutter run
```

**Manual checks on emulator:**
- [ ] App launches without crash
- [ ] Home screen shows mic button (large, teal), text input, 4 service chips
- [ ] Tapping mic requests microphone permission
- [ ] Typing "AC Repair" and tapping submit shows a loading spinner then navigates (even if to blank screen)
- [ ] Bottom nav shows 3 tabs

---

## P1 Sprint 2 — Customer Flow (Day 1, 2pm–5pm)

**Build:** All customer screens from provider list through to booking confirmed.

### Exact Antigravity Prompt — P1 Sprint 2

```
I am P1 on KamKaaj continuing from Sprint 1. app/ is still all mine.
Read docs/PRD.md sections 6.1, 6.2, 6.3, 6.4, 15 (US-E1 through US-E4).
Read app/assets/mock/match_response.json — this is the data shape I will display.
Read app/assets/mock/price_response.json.
Read app/assets/mock/booking_response.json.
Read app/lib/theme/app_theme.dart.
Read app/lib/services/api_service.dart.

Build these screens. Each screen receives data via its constructor from the previous screen.

━━━ FILE 1: app/lib/customer/clarification_screen.dart ━━━
Shown when intent confidence is between 0.65 and 0.84 OR below 0.65.
Props: String question (from API response), Function(String answer) onAnswer

Layout:
- Title: "Ek sawal" (AppTheme.primary, 20sp)
- Question text (large, 18sp, prominent)
- Voice answer button (mic icon, same style as home screen)
- Text answer field
- "Jawab Dein" submit button
- Progress indicator: "Round 1 of 2" (max 2 clarification rounds)

On submit: call onAnswer(). Parent navigates to ProviderListScreen or asks again (max 2 rounds).
After 2 rounds with still-low confidence: show 3 service category cards as fallback.

━━━ FILE 2: app/lib/customer/provider_list_screen.dart ━━━
Props: Map<String, dynamic> matchResponse (from ApiService.postMatch())

Layout:
- AppBar: "Behtar Providers" + subtitle showing service type from response
- While loading: show 3 shimmer cards (use shimmer package)
- Provider list: 3 ProviderCard widgets built from matchResponse["providers"]
- Each ProviderCard:
  - Provider name (bold, 16sp)
  - Star rating (show actual stars) + rating number
  - On-time percentage: "91% on time" in green if >85%, yellow if 70-85%, red if <70%
  - ETA badge: "~25 min away"
  - Price range: "PKR 2,200 – 2,800"
  - Recommendation reason (italic, grey): first line from reasoning_trace
  - "Wajah Dekhein" (See reasoning) link → opens TraceViewerScreen
  - "Select" button (teal, full width, 48dp)
- Baseline comparison banner at bottom: "AI vs Distance-Only?" → opens BaselineComparisonScreen

On "Select": fetch price via ApiService.postPrice(), navigate to PriceBreakdownScreen.

━━━ FILE 3: app/lib/customer/price_breakdown_screen.dart ━━━
Props: Map<String, dynamic> priceResponse, Map<String, dynamic> selectedProvider

Layout:
- AppBar: "Qeemat ki Tafseel"
- Itemised rows from priceResponse["breakdown"]:
  - Each row: English label on left, Urdu label (AppTheme.urdu()) on right, PKR amount far right
  - Examples: "Base charge / بنیادی معاوضہ", "Parts / پرزے", "Labour / مزدوری"
- Divider
- Surge badge (orange banner): show only if priceResponse["surge_active"] == true
  Text: "Peak demand — +X% surge"
- Alternate timing offer (if present): "Book for [alternate_slot], save PKR [amount]" (tappable)
- Provider earnings line (green, smaller text): "Provider earns: PKR X"
- Total row (bold, large): "Kul / کُل  PKR X,XXX"
- "Book Karein" button → calls ApiService.postBook(), navigates to BookingConfirmationScreen

━━━ FILE 4: app/lib/customer/booking_confirmation_screen.dart ━━━
Props: Map<String, dynamic> bookingResponse

Layout:
- Green checkmark icon (64dp)
- "Booking Confirmed!" heading
- Urdu: AppTheme.urdu("بکنگ کامیاب") below heading
- Booking ID in monospace: bookingResponse["booking_id"]
- Provider info card: name, rating, photo placeholder (CircleAvatar with initials)
- Slot: formatted date + time
- Two reminder badges (pill chips): "1 day before" and "1 hour before"
- "Receipt Dekhein" button (outlined)
- "Home" button → pops to HomeScreen (clear stack)

━━━ FILE 5: app/lib/customer/tracking_screen.dart ━━━
Props: String bookingId

Layout:
- AppBar: "Service Track Karein"
- Status timeline (vertical): En Route → Arrived → In Progress → Completed
  Each step: icon + label + timestamp (if completed) or "Pending"
  Active step highlighted in AppTheme.primary
- Provider info row: name, phone icon (tappable, opens dialer)
- Live status updates via FirestoreService.listenToBooking(bookingId)
  Refresh the timeline when status changes
- When status == "COMPLETED": show "Rate Karein" button → navigates to RatingScreen

━━━ FILE 6: app/lib/customer/rating_screen.dart ━━━
Props: String bookingId, String providerName

Layout:
- "Apna Tajurba Batayein" heading
- Provider name subtitle
- 5-star rating row (interactive, tap to select)
- Comment text field (optional, placeholder: "Kuch aur bataana chahte hain?")
- "Submit" button → calls FirestoreService with rating + comment, shows thank you, pops to Home

━━━ FILE 7: app/lib/customer/dispute_screen.dart ━━━
Props: String bookingId

Layout:
- AppBar: "Masla Report Karein"
- Dropdown: Issue type (Price dispute / Provider no-show / Quality issue / Other)
- Description text field (multiline, required)
- "Submit" button → calls ApiService.raiseDispute(), shows confirmation with dispute_id
- After submit: show "Aapka masla 2 ghante mein hal hoga" (Urdu + English)

━━━ FILE 8: app/lib/customer/trace_viewer_screen.dart ━━━
Props: Map<String, dynamic> reasoningTrace

Layout:
- AppBar: "AI Reasoning Trace"
- One ExpansionTile per agent step in reasoningTrace["steps"]:
  - Title: agent name + duration_ms
  - Body: key decision points as bullet list
- "Raw JSON Download" button at bottom (copies JSON to clipboard, shows SnackBar)
```

### Test after P1 Sprint 2

```bash
cd app
flutter run
```

**Manual tap-through (the golden path):**
1. [ ] Home screen → type "AC Repair G-13" → tap submit
2. [ ] Shimmer loads for 1 second, then 3 provider cards appear
3. [ ] Each provider card shows name, stars, on-time %, ETA, price range, reason
4. [ ] Tap "Select" on first provider → price breakdown screen shows itemised rows
5. [ ] Tap "Book Karein" → booking confirmation screen shows booking ID
6. [ ] Tap "Home" → back to home screen (stack cleared)
7. [ ] Navigate to a booking from Activity tab → tracking screen with timeline
8. [ ] Tap "Rate Karein" → rating screen with 5 stars

---

## P1 Sprint 3 — Provider Screens + Extras (Day 1, 5pm–8pm)

**Build:** All provider-side screens and the remaining customer screens.

### Exact Antigravity Prompt — P1 Sprint 3

```
I am P1 on KamKaaj, Sprint 3. app/ is all mine.
Read docs/PRD.md sections 5.6, 5.8, 6.5, 15 (US-E5 through US-E9).
Read app/lib/theme/app_theme.dart.
Read app/lib/services/firestore_service.dart.

Build these provider-side screens and remaining customer screens.

━━━ FILE 1: app/lib/provider/job_alert_screen.dart ━━━
This is the most important provider screen. Full-screen job alert with 90s timer.
Props: Map<String, dynamic> jobData

Layout:
- Full screen background: AppTheme.primary (dark teal)
- "Naya Kaam!" heading (white, 28sp, bold)
- Circular countdown timer: 90 seconds, white circle, ticking down
  Use AnimationController. When reaches 0: auto-decline, pop screen.
- Job details card (white card on teal background):
  - Service type (bold)
  - Customer sector (NOT full address): "G-13, Islamabad"
  - Slot: formatted date + time
  - Complexity badge: "Basic" / "Intermediate" / "Complex"
  - Estimated earnings: "PKR X,XXX"
- TWO large buttons side by side (each 80dp tall):
  - DECLINE: red background, white text "Inkaar" — calls ApiService.updateProviderStatus(id, "DECLINED")
  - ACCEPT: green background, white text "Manzoor" — calls ApiService.updateProviderStatus(id, "ACCEPTED")
    On accept: navigate to ScheduleScreen

━━━ FILE 2: app/lib/provider/schedule_screen.dart ━━━
Props: String providerId, String date

Layout:
- AppBar: "Aaj ka Schedule" + date
- Load from FirestoreService.getProviderSchedule(providerId, date)
- Shimmer while loading
- Ordered list of jobs: time slot → customer sector → service type → status badge
- Travel ETA between consecutive jobs: "~15 min travel"
- Empty state: "Aaj koi kaam nahi" with illustration

━━━ FILE 3: app/lib/provider/status_update_screen.dart ━━━
Props: String bookingId, String customerSector

Layout:
- AppBar: "Job Status"
- Customer sector (not full address)
- Status button row (vertical, full width):
  - "En Route" button → enabled always → calls updateBookingStatus("EN_ROUTE")
  - "Pahunch Gaya" (Arrived) button → enabled after En Route
    Tapping opens arrival checklist (3 items). Must check all 3 before proceeding.
    Checklist items: "ID card checked", "Tools ready", "Customer confirmed"
  - "Kaam Shuru" (In Progress) → enabled after arrival checklist complete
  - "Mukammal" (Completed) → enabled after In Progress
    Tapping opens completion checklist (3 items) + "Photo Upload" button (placeholder, shows SnackBar)
- Each status change calls FirestoreService.updateBookingStatus()

━━━ FILE 4: app/lib/provider/earnings_screen.dart ━━━
Layout:
- AppBar: "Kamayi"
- 3 summary cards in a row: "Aaj" / "Is Hafte" / "Is Mahine"
  Each card: big PKR amount + small label
- "Aaj ki Expected Kamayi" row (projected)
- List of recent completed jobs: date → service → PKR amount

━━━ FILE 5: app/lib/provider/demand_forecast_screen.dart ━━━
Layout:
- AppBar: "Demand Forecast"
- List of sectors: each sector shows demand badge
  Normal = grey pill, Elevated = yellow pill, High = orange pill, Surge = red pill
- Advice text: "G-11 mein zyada demand hai aaj"

━━━ FILE 6: app/lib/customer/baseline_comparison_screen.dart ━━━
Read docs/PRD.md section 12 carefully before building this screen.

Props: Map<String, dynamic> matchResponse

Layout:
- AppBar: "AI vs Simple Ranking"
- Toggle at top: "Distance Only" vs "Agentic (AI)"
- Two columns side by side showing:
  - Distance-Only rank: prov_003 first (nearest, 0.8km), prov_001 second
  - Agentic rank: prov_001 first (score 0.87), prov_003 third (risk warning: 28% cancel)
- Below each provider: their top 2 factors shown
- Explanation card: "AI ne prov_003 ko rank mein neeche rakha kyunki..."

━━━ FILE 7: app/lib/customer/activity_screen.dart ━━━
Layout:
- AppBar: "Activity"
- TabBar: "Upcoming" | "Past"
- Upcoming: list of bookings with CONFIRMED or IN_PROGRESS status
- Past: list with COMPLETED or DISPUTED status
- Each item: service type, provider name, date, status badge (colored)
- Empty state for each tab

━━━ WIRE EVERYTHING: app/main.dart ━━━
Update routes to include all screens.
Add provider bottom nav: Jobs / Earnings / Profile.
For demo purposes: add a toggle in Profile screen to switch between Customer and Provider mode.
```

### Test after P1 Sprint 3

```bash
cd app
flutter run
```

**Manual checks:**
- [ ] Provider job alert screen: 90s timer counts down, Accept/Decline buttons work
- [ ] Status update screen: can only tap "Arrived" after "En Route", checklist gates progress
- [ ] Earnings screen shows 3 cards
- [ ] Baseline comparison: toggle switches between two rankings
- [ ] Activity tab shows upcoming/past tabs
- [ ] Profile screen has customer/provider mode toggle

---

---

# P2 — ADK Core Pipeline

## P2 Sprint 1 — Setup + Intent Agent + Mock Server (Day 1, 10am–1pm)

**Build:** Python project, shared tools, Intent agent, and mock server (P1 needs mock server by 2pm).

### Exact Antigravity Prompt — P2 Sprint 1

```
I am P2 on KamKaaj. I own adk/agents/intent/, adk/agents/matching/, adk/agents/pricing/,
adk/pipeline.py, adk/main.py, adk/tools/, adk/mock_server.py, adk/requirements.txt.
Read docs/PRD.md sections 4, 5.1, 7, 9.1.
Read contracts/api_schemas.json.
Read contracts/db_schemas.json.

━━━ FILE 1: adk/requirements.txt ━━━
google-adk>=0.4.0
google-cloud-firestore>=2.13.0
googlemaps>=4.10.0
fastapi>=0.104.0
uvicorn>=0.24.0
python-dotenv>=1.0.0
httpx>=0.25.0

━━━ FILE 2: adk/tools/shared_tools.py ━━━
FunctionTool implementations for use across agents.

def provider_db_query(service_type: str, sectors: list[str]) -> dict:
    """Read data/providers.json, filter by service_type and sectors, return matching providers list."""
    # Load from file, filter, return {"providers": [...], "total": N}
    
def firestore_write(collection: str, doc_id: str, data: dict) -> dict:
    """Write to Firestore using firebase-admin SDK. Return {"success": true, "doc_id": doc_id}"""
    
def firestore_read(collection: str, doc_id: str) -> dict:
    """Read from Firestore. Return {"found": true/false, "data": {...}}"""

def google_maps_distance_matrix(provider_locations: list[dict], job_location: dict) -> dict:
    """
    Call Google Maps Distance Matrix API.
    provider_locations: [{"provider_id": str, "lat": float, "lng": float}, ...]
    job_location: {"sector": str, "lat": float, "lng": float}
    
    If Maps API fails or times out (>2s): fall back to sector centroid lookup.
    Sector centroids (lat/lng):
      G-13: 33.6844, 73.0479
      G-11: 33.6938, 73.0551
      G-10: 33.7024, 73.0474
      F-8: 33.7180, 73.0523
    Return: {"distances": [{"provider_id": str, "distance_km": float, "duration_min": int, "is_approximate": bool}], "fallback_used": bool}
    """

━━━ FILE 3: adk/agents/intent/tools.py ━━━
These are the 4 FunctionTools for the Intent Agent:

def language_detector(text: str) -> dict:
    """Detect language mix. Return {"languages": ["roman_urdu"|"urdu"|"english"|"mixed"], "primary": str, "confidence": float}"""

def confidence_scorer(extracted_fields: dict) -> dict:
    """Score completeness of extraction. Return {"overall_confidence": float, "missing_fields": [str], "reason": str}"""
    # confidence < 0.65: ask clarifying question
    # 0.65-0.84: show confirmation card
    # >= 0.85: proceed directly

def complexity_classifier(service_type: str, fault_description: str) -> dict:
    """Classify job complexity. Return {"complexity": "basic"|"intermediate"|"complex", "rationale": str}"""

def clarifying_question_generator(missing_field: str, context: dict) -> dict:
    """Generate ONE clarifying question in Roman Urdu/English mix.
    Return {"question": str, "field_being_asked": str}
    Examples:
    - missing location: "Aap kis sector mein hain?"
    - missing time: "Kab chahiye — aaj ya kal?"
    - missing service detail: "AC split hai ya window unit?"
    Never ask multiple questions at once."""

━━━ FILE 4: adk/agents/intent/agent.py ━━━
Google ADK LlmAgent named "intent_agent".

System prompt (use this exactly):
"""
You are an expert at understanding Pakistani home service requests.
Customers speak in Roman Urdu, Urdu script, English, or any mix.

Your job: extract structured information from their request.
Fields to extract:
- service_type: one of AC_REPAIR, PLUMBING, ELECTRICAL, CLEANING, CARPENTRY, TUTORING
- location: {sector: str, city: str} — infer from context (e.g. "g elevan" = "G-11")
- urgency: "urgent" (today) | "scheduled" (specific time) | "flexible"
- preferred_time: ISO datetime if mentioned, else null
- budget_sensitivity: "low" | "mid" | "premium" — infer from language/context
- fault_description: cleaned description of the problem in English

Always call confidence_scorer after extraction.
If confidence < 0.65: call clarifying_question_generator for the most important missing field.
If confidence 0.65-0.84: return result with flag "needs_confirmation": true.
If confidence >= 0.85: return result with flag "confirmed": true.

IMPORTANT: Never ask more than one question. Be concise.
"""

Register tools: language_detector, confidence_scorer, complexity_classifier, clarifying_question_generator.
Input: matches POST /v1/session/intent request schema from contracts/api_schemas.json.
Output: must match the intent_result field in the response schema exactly.
Include reasoning_trace with fields: agent_name, steps (list of {tool_called, input_summary, output_summary}), duration_ms, confidence.

━━━ FILE 5: adk/agents/intent/test.py ━━━
Quick local test with 3 inputs:

test_cases = [
    {
        "input": "AC bilkul kaam nahi kar raha",
        "expect_confidence_below": 0.65,
        "expect_missing": ["location"]
    },
    {
        "input": "I need a plumber today in F-8",
        "expect_confidence_above": 0.85,
        "expect_service": "PLUMBING"
    },
    {
        "input": "ac kaam ni kar raha chahiye koi g elevan mein",
        "expect_location": "G-11",
        "expect_confidence_range": [0.65, 0.84]
    }
]

━━━ FILE 6: adk/mock_server.py ━━━
FastAPI app. When called, return canned JSON from contracts/*.json.
CRITICAL: This must be working by 2pm so P1 can test against it.

from fastapi import FastAPI
import json

app = FastAPI()

@app.post("/v1/session/intent")
async def intent(body: dict):
    return json.load(open("app/assets/mock/intent_response.json"))

@app.post("/v1/session/match")
async def match(body: dict):
    return json.load(open("app/assets/mock/match_response.json"))

@app.post("/v1/session/book")
async def book(body: dict):
    return json.load(open("app/assets/mock/booking_response.json"))

@app.post("/v1/dispute/raise")
async def dispute(body: dict):
    return {"dispute_id": "DISP-001", "status": "RECEIVED", "estimated_resolution": "2 hours"}

@app.post("/v1/provider/job-response")
async def job_response(body: dict):
    return {"status": "OK"}

@app.post("/v1/provider/status-update")
async def status_update(body: dict):
    return {"status": "OK"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8080)
```

### Test after P2 Sprint 1

```bash
cd adk

# Start mock server
python mock_server.py &
sleep 2

# Test mock server endpoints
curl -s -X POST http://localhost:8080/v1/session/intent \
  -H "Content-Type: application/json" \
  -d '{"user_input": "AC kharab hai", "session_id": "test"}' | python3 -m json.tool

curl -s -X POST http://localhost:8080/v1/session/match \
  -H "Content-Type: application/json" \
  -d '{"intent_result": {}, "session_id": "test"}' | python3 -m json.tool

# Test intent agent locally
python agents/intent/test.py

# Ping P1 — mock server is live at localhost:8080
```

**Pass criteria:** Both curls return valid JSON. Intent test.py prints PASS for all 3 cases. Share localhost:8080 with P1.

---

## P2 Sprint 2 — Matching + Pricing Agents (Day 1, 2pm–5pm)

**Build:** Matching agent with 8-factor scoring and Pricing agent with PRD formula.

### Exact Antigravity Prompt — P2 Sprint 2

```
I am P2 on KamKaaj, Sprint 2.
Read docs/PRD.md section 5.2 (Matching Agent) and 5.3 (Pricing Agent) carefully.
Read contracts/api_schemas.json (match and price endpoints).
Read adk/tools/shared_tools.py (functions I can reuse).
Read data/providers.json (the mock provider data P3 created — use it).

━━━ FILE 1: adk/agents/matching/tools.py ━━━
These 5 FunctionTools implement the 8-factor scoring from PRD §5.2:

def availability_checker(provider_ids: list[str], requested_slot: str) -> dict:
    """Check which providers are available for requested_slot (ISO datetime).
    Reads Firestore bookings collection. Returns {"available": [provider_ids], "busy": [provider_ids]}"""

def rating_analyzer(provider_id: str) -> dict:
    """Compute recency-adjusted rating. Recent reviews (last 14 days) weighted 2x.
    Return {"overall_rating": float, "recency_adjusted": float, "recent_negative_count": int}"""

def risk_scorer(provider_id: str) -> dict:
    """Compute risk score from cancellation_rate_pct and recent_negative_count.
    Return {"risk_level": "low"|"medium"|"high", "cancellation_rate": float, "flags": [str]}
    High risk if cancellation_rate > 20% OR recent_negative_count >= 2."""

━━━ FILE 2: adk/agents/matching/agent.py ━━━
Google ADK LlmAgent named "matching_agent".

System prompt:
"""
You are an expert provider matching system for KamKaaj.
Score each available provider across 8 factors:
1. distance_score: inverse of distance_km (closer = higher)
2. availability_score: 1 if available, 0 if not
3. rating_score: recency_adjusted_rating / 5.0
4. cancellation_score: 1 - (cancellation_rate_pct / 100)
5. completion_speed_score: based on avg_job_duration_min (faster = higher)
6. specialization_score: 1 if provider specializations match service_type detail, else 0.5
7. price_tier_score: based on customer budget_sensitivity matching provider price_tier
8. risk_score: 1 - risk_scorer result (low risk = high score)

Final score = weighted average. Weights from PRD §5.2.
Return top 3 providers with score + reason per factor.
Always show the reasoning. If a provider has risk flags, state them explicitly.
"""

Tools: provider_db_query, google_maps_distance_matrix, availability_checker, rating_analyzer, risk_scorer.
Include reasoning_trace in output.

━━━ FILE 3: adk/agents/pricing/tools.py ━━━

def demand_forecaster(service_type: str, sector: str, datetime_str: str) -> dict:
    """Estimate demand level. Return {"demand_level": "normal"|"elevated"|"high"|"surge", "demand_multiplier": float}
    Surge conditions from PRD §5.3: >3 requests same sector last hour = elevated, >6 = surge (1.3x max)."""

def surge_calculator(base_price: float, demand_level: str) -> dict:
    """Apply surge multiplier. Return {"final_price": float, "surge_active": bool, "surge_multiplier": float}
    Caps at 1.3x. Never apply surge without disclosure."""

def price_breakdown_formatter(service_type: str, complexity: str, parts_needed: bool) -> dict:
    """Return itemized price breakdown.
    Return {"items": [{"label_en": str, "label_ur": str, "amount": float}], "base_total": float,
            "provider_earnings": float, "platform_fee": float}
    Provider earns 75% of base_total. Platform fee is 25%."""

━━━ FILE 4: adk/agents/pricing/agent.py ━━━
Google ADK LlmAgent named "pricing_agent".

System prompt:
"""
You are KamKaaj's pricing agent. Calculate fair, transparent pricing.
Use the pricing formula from PRD §5.3.
Always:
- Show itemized breakdown (base, parts, labour)
- Show provider earnings separately (they earn 75% of base)
- Apply surge only if demand_forecaster returns "high" or "surge"
- If surge active, always disclose it prominently
- Offer alternate timing if surge is active (next available non-surge slot)
All PKR amounts rounded to nearest 50.
"""

Tools: demand_forecaster, surge_calculator, price_breakdown_formatter.
Include reasoning_trace in output.
```

### Test after P2 Sprint 2

```bash
cd adk

# Test matching agent
python -c "
from agents.matching.agent import matching_agent
import asyncio
result = asyncio.run(matching_agent.run({
    'intent_result': {
        'service_type': 'AC_REPAIR',
        'location': {'sector': 'G-13', 'city': 'Islamabad'},
        'preferred_time': '2026-05-19T10:00:00'
    },
    'session_id': 'test'
}))
print('Matching agent:', result['providers'][0]['provider_id'] if result.get('providers') else 'ERROR')
"

# Test pricing agent
python -c "
from agents.pricing.agent import pricing_agent
import asyncio
result = asyncio.run(pricing_agent.run({
    'service_type': 'AC_REPAIR',
    'complexity': 'intermediate',
    'sector': 'G-13',
    'session_id': 'test'
}))
print('Pricing agent, total:', result.get('total_price', 'ERROR'))
print('Surge active:', result.get('surge_active', False))
"
```

**Pass criteria:** Matching agent returns 3 providers. Pricing agent returns a total_price > 0.

---

## P2 Sprint 3 — Pipeline + FastAPI Server (Day 1, 5pm–8pm)

**Build:** Wire all 3 agents into SequentialAgent, build the real FastAPI server.

### Exact Antigravity Prompt — P2 Sprint 3

```
I am P2 on KamKaaj, Sprint 3.
Read docs/PRD.md sections 4, 7, 9.1.
Read contracts/api_schemas.json — I need every request/response shape exactly.
Read adk/agents/intent/agent.py, adk/agents/matching/agent.py, adk/agents/pricing/agent.py.

━━━ FILE 1: adk/pipeline.py ━━━
Wire intent → matching → pricing into a Google ADK SequentialAgent.
(P3's scheduling and booking agents will be added here on Day 2.)

from google.adk.agents import SequentialAgent
from agents.intent.agent import intent_agent
from agents.matching.agent import matching_agent
from agents.pricing.agent import pricing_agent

core_pipeline = SequentialAgent(
    name="kamkaaj_core",
    sub_agents=[intent_agent, matching_agent, pricing_agent]
)

Include a run_pipeline(session_id, user_input, location, user_id) async function that:
1. Runs core_pipeline
2. Collects all reasoning traces from each agent
3. Returns a combined response with all traces under "reasoning_trace.steps"

━━━ FILE 2: adk/main.py ━━━
FastAPI production server (not mock — this runs the real agents).

from fastapi import FastAPI, Header, HTTPException
from pipeline import run_pipeline, core_pipeline

app = FastAPI(title="KamKaaj ADK API")

def verify_auth(authorization: str = Header(None)):
    if not authorization or not authorization.startswith("Bearer "):
        raise HTTPException(status_code=401, detail={"error": "UNAUTHORIZED", "message": "Token required"})

@app.post("/v1/session/intent")
async def intent_endpoint(body: dict, auth = Depends(verify_auth)):
    # Run only intent_agent, return intent_result + reasoning_trace
    
@app.post("/v1/session/match")  
async def match_endpoint(body: dict, auth = Depends(verify_auth)):
    # Run intent → matching, return providers + reasoning_trace
    
@app.post("/v1/session/book")
async def book_endpoint(body: dict, auth = Depends(verify_auth)):
    # Run full pipeline (intent → matching → pricing)
    # Return booking_id, confirmed_provider, price_breakdown, reasoning_trace
    # Note: scheduling and booking agents added Day 2 by P4

All error responses use this exact shape (from PRD §7.0):
{"error": {"code": str, "message": str, "details": {}}}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8080, reload=True)
```

### Test after P2 Sprint 3

```bash
cd adk

# Kill mock server if running
pkill -f mock_server.py

# Start real server
python main.py &
sleep 3

# Full happy path test
curl -s -X POST http://localhost:8080/v1/session/book \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer test-token" \
  -d '{
    "session_id": "sprint3-test",
    "user_input": "AC theek nahi, G-13, kal subah",
    "location": {"sector": "G-13", "city": "Islamabad"},
    "user_id": "test-user-001"
  }' | python3 -m json.tool

# Test auth rejection
curl -s -X POST http://localhost:8080/v1/session/book \
  -H "Content-Type: application/json" \
  -d '{"session_id": "test"}' | python3 -m json.tool
```

**Pass criteria:** Book endpoint returns `providers` list + `total_price`. Auth rejection returns 401 with error schema.

---

---

# P3 — ADK Extended Agents + Firebase + Data

## P3 Sprint 1 — Provider Data + Firebase (Day 1, 10am–1pm)

**Build:** All 52 provider records, Firebase rules and indexes. Share providers.json with P2 by 2pm.

### Exact Antigravity Prompt — P3 Sprint 1

```
I am P3 on KamKaaj. I own adk/agents/scheduling/, adk/agents/booking/, adk/agents/followup/,
adk/agents/dispute/, adk/agents/provider_agent/, firebase/, data/.
Read docs/PRD.md sections 8.1, 8.5, 10, 11.
Read contracts/db_schemas.json — Provider schema is in there.

━━━ FILE 1: data/providers.json ━━━
Generate a JSON array of exactly 52 providers matching the Provider schema.
This is critical — do NOT use placeholders. Generate all 52 records fully.

Distribution requirements:
- 30 in Islamabad sectors: G-9, G-10, G-11, G-13, F-7, F-8, F-10, I-8, I-9
- 22 in Rawalpindi: Saddar, Satellite Town, Bahria Town Phase 1, Phase 2, Phase 3, Phase 4, DHA Phase 1, Phase 2
- Service split: AC_REPAIR×15, PLUMBING×10, ELECTRICAL×10, CLEANING×8, CARPENTRY×7, TUTORING×2
- Rating: bell curve, mean 4.1, SD 0.6, range 2.8–4.9
- on_time_pct: mean 82, SD 12, range 40–98
- cancellation_rate_pct: mean 11, SD 8, range 2–35
- price_tier: 30% "low", 50% "mid", 20% "premium"
- Pakistani names: mix of male/female (about 80% male for trades, 60% female for cleaning/tutoring)

REQUIRED providers (must exist exactly as specified):
{
  "provider_id": "prov_001",
  "name": "Usman Rana",
  "service_type": "AC_REPAIR",
  "sector": "G-13",
  "city": "Islamabad",
  "rating": 4.6,
  "on_time_pct": 91,
  "cancellation_rate_pct": 8,
  "price_tier": "mid",
  "specializations": ["Split AC", "Inverter AC", "Daikin", "Gree"],
  "lat": 33.6844, "lng": 73.0479
}
{
  "provider_id": "prov_003",
  "service_type": "AC_REPAIR",
  "sector": "G-13",
  "rating": 3.8,
  "cancellation_rate_pct": 28,
  "recent_negative_reviews": 3,
  "last_negative_review_date": "2026-05-17"
}
{
  "provider_id": "prov_005",
  "service_type": "AC_REPAIR",
  "rating": 4.2,
  "cancellation_rate_pct": 9,
  "recent_negative_count": 2,
  "last_negative_review_date": "2026-05-16"
}
{
  "provider_id": "prov_007",
  "rating": 3.1,
  "cancellation_rate_pct": 28,
  "risk_flag": "HIGH_RISK"
}

Each provider must have these fields:
provider_id, name, phone, service_type, sector, city, lat, lng,
rating, on_time_pct, cancellation_rate_pct, price_tier, specializations (array),
avg_job_duration_min, is_available, price_range_low, price_range_high,
gender, years_experience, risk_flag (null for most)

━━━ FILE 2: firebase/firestore.rules ━━━
security_rules_version = '2'
Rules:
- users/{userId}: read/write only if request.auth.uid == userId
- bookings/{bookingId}: 
  - read: customer_id matches auth OR provider_id matches auth
  - write: only authenticated, only update allowed fields (status, actual_price, rating)
- providers/{providerId}:
  - read: any authenticated user (matching agents need to query this)
  - write: only the provider themselves OR admin
- sessions/{sessionId}: read/write only if user_id matches auth

━━━ FILE 3: firebase/firestore.indexes.json ━━━
Create all 5 composite indexes from PRD §8.5:
1. bookings: [provider_id ASC, status ASC, slot_start ASC]
2. bookings: [customer_id ASC, created_at DESC]
3. providers: [service_type ASC, sector ASC, is_available ASC, rating DESC]
4. providers: [service_type ASC, city ASC, price_tier ASC]
5. bookings: [provider_id ASC, slot_start ASC, slot_end ASC]

Format must be valid Firestore indexes JSON.
```

### Test after P3 Sprint 1

```bash
# Validate providers.json
python3 -c "
import json
data = json.load(open('data/providers.json'))
print(f'Total providers: {len(data)}')
assert len(data) == 52, f'Expected 52, got {len(data)}'

# Check required providers exist
ids = [p['provider_id'] for p in data]
for req in ['prov_001', 'prov_003', 'prov_005', 'prov_007']:
    assert req in ids, f'Missing {req}'
    
# Check prov_001 specifics
p1 = next(p for p in data if p['provider_id'] == 'prov_001')
assert p1['name'] == 'Usman Rana', f'prov_001 name wrong: {p1[\"name\"]}'
assert p1['rating'] == 4.6, f'prov_001 rating wrong: {p1[\"rating\"]}'

# Check distribution
from collections import Counter
services = Counter(p['service_type'] for p in data)
print('Service distribution:', dict(services))
print('All required providers present ✅')
"

# Validate Firestore files
python3 -c "import json; json.load(open('firebase/firestore.indexes.json')); print('indexes.json OK ✅')"
python3 -c "
data = open('firebase/firestore.rules').read()
assert 'request.auth' in data, 'No auth checks found!'
print('firestore.rules has auth checks ✅')
"
```

**Pass criteria:** 52 providers, all 4 required providers present, valid JSON everywhere. Share `data/providers.json` with P2 immediately.

---

## P3 Sprint 2 — Scheduling + Booking Agents (Day 1, 2pm–5pm)

**Build:** The two agents that convert a booking request into a confirmed, atomic reservation.

### Exact Antigravity Prompt — P3 Sprint 2

```
I am P3 on KamKaaj, Sprint 2.
Read docs/PRD.md sections 5.4 (Scheduling Agent) and 5.5 (Booking Agent).
Read contracts/api_schemas.json (book endpoint).
Read contracts/db_schemas.json (Booking schema).
Read adk/tools/shared_tools.py (firestore_read, firestore_write available).

━━━ FILE 1: adk/agents/scheduling/tools.py ━━━

def calendar_reader(provider_id: str, date: str) -> dict:
    """Read provider's booked slots for date from Firestore.
    Return {"booked_slots": ["10:00-12:00", "14:00-16:00"], "available_slots": ["08:00-10:00", "12:00-14:00"]}"""

def slot_blocker(provider_id: str, slot_start: str, slot_end: str, session_id: str) -> dict:
    """Atomically block a slot using Firestore transaction.
    If slot already taken: return {"success": false, "error": "SLOT_CONFLICT"}
    If successfully blocked: return {"success": true, "reservation_id": str}
    Use Firestore transaction to prevent race conditions (concurrent bookings)."""

def conflict_detector(provider_id: str, slot_start: str, slot_end: str) -> dict:
    """Check if slot overlaps with existing bookings. Return {"conflict": bool, "conflicting_booking_id": str|null}"""

def waitlist_manager(service_type: str, sector: str, slot_start: str, user_id: str) -> dict:
    """Add user to waitlist when no providers available.
    Return {"waitlist_position": int, "estimated_wait_hours": float, "waitlist_id": str}"""

def auto_reschedule_engine(cancelled_booking_id: str) -> dict:
    """When a provider cancels, find next best provider for same slot.
    Return {"new_provider_id": str, "same_slot": bool, "new_slot": str|null, "notification_sent": bool}"""

━━━ FILE 2: adk/agents/scheduling/agent.py ━━━
Google ADK LlmAgent named "scheduling_agent".

System prompt:
"""
You are KamKaaj's scheduling agent. Your job is to find and lock a time slot.
Process:
1. Read provider's calendar using calendar_reader
2. Check for conflicts using conflict_detector
3. If available: atomically block with slot_blocker
4. If SLOT_CONFLICT returned: try next available slot or return SLOT_CONFLICT error
5. If no providers available for any slot: offer waitlist using waitlist_manager
6. If a booking gets cancelled: trigger auto_reschedule_engine immediately

Critical: slot_blocker uses a Firestore transaction — it is atomic. Trust it.
If it returns SLOT_CONFLICT, do not retry the same slot.
"""

Tools: calendar_reader, slot_blocker, conflict_detector, waitlist_manager, auto_reschedule_engine.
Input: provider_id, slot_start, slot_end, session_id.
Output: {reservation_id, slot_confirmed, waitlisted: bool} or error.

━━━ FILE 3: adk/agents/booking/tools.py ━━━

def booking_id_generator() -> dict:
    """Generate a unique booking ID. Format: KK-<8 uppercase alphanumeric>.
    Example: KK-A3F92B1D
    Return {"booking_id": str}"""

def notification_dispatcher(user_id: str, provider_id: str, message_type: str, data: dict) -> dict:
    """Send FCM push notification via Firebase Cloud Messaging.
    message_type: "BOOKING_CONFIRMED" | "JOB_ALERT" | "REMINDER" | "STATUS_UPDATE" | "AUTO_RESCHEDULED"
    Return {"sent": bool, "notification_id": str}"""

def reminder_scheduler(booking_id: str, slot_start: str) -> dict:
    """Schedule two reminders: 24h before and 1h before slot_start.
    Return {"reminders": [{"trigger_at": str, "type": "24h"|"1h"}]}"""

━━━ FILE 4: adk/agents/booking/agent.py ━━━
Google ADK LlmAgent named "booking_agent".

System prompt:
"""
You are KamKaaj's booking confirmation agent.
Once scheduling has confirmed a slot:
1. Generate a booking ID
2. Write the complete booking document to Firestore (bookings collection)
3. Send JOB_ALERT notification to the provider
4. Send BOOKING_CONFIRMED notification to the customer
5. Schedule reminders (24h and 1h before)
6. Return the complete booking confirmation

The booking document must have ALL fields from the Booking schema in contracts/db_schemas.json.
"""

Tools: booking_id_generator, firestore_write (from shared_tools), notification_dispatcher, reminder_scheduler.
```

### Test after P3 Sprint 2

```bash
cd adk

# Test scheduling agent
python -c "
from agents.scheduling.agent import scheduling_agent
import asyncio
result = asyncio.run(scheduling_agent.run({
    'provider_id': 'prov_001',
    'slot_start': '2026-05-19T10:00:00',
    'slot_end': '2026-05-19T12:00:00',
    'session_id': 'sched-test-001'
}))
print('Scheduling result:', result.get('reservation_id', result.get('error', 'ERROR')))
"

# Test booking agent
python -c "
from agents.booking.agent import booking_agent
import asyncio
result = asyncio.run(booking_agent.run({
    'provider_id': 'prov_001',
    'customer_id': 'test-customer',
    'reservation_id': 'RES-001',
    'price_breakdown': {'total': 2500},
    'session_id': 'book-test-001'
}))
bid = result.get('booking_id', '')
print('Booking ID:', bid)
print('Looks like KK- format:', bid.startswith('KK-'))
"
```

**Pass criteria:** Scheduling returns a reservation_id. Booking ID starts with "KK-".

---

## P3 Sprint 3 — Follow-up + Dispute + Provider Agent + Seed (Day 1, 5pm–8pm)

**Build:** Service-day lifecycle, dispute resolution, provider optimisation, and seed script.

### Exact Antigravity Prompt — P3 Sprint 3

```
I am P3 on KamKaaj, Sprint 3.
Read docs/PRD.md sections 5.6 (Follow-up), 5.7 (Dispute), 5.8 (Provider Agent).
Read contracts/db_schemas.json.
Read data/providers.json (for understanding provider data shape).

━━━ FILE 1: adk/agents/followup/agent.py ━━━
Google ADK LlmAgent named "followup_agent".
Manages the service-day lifecycle: EN_ROUTE → ARRIVED → IN_PROGRESS → COMPLETED.

Tools to implement in adk/agents/followup/tools.py:
- provider_status_updater(booking_id, new_status) → writes to Firestore bookings/{bookingId}
- eta_calculator(provider_id, job_location) → uses google_maps or sector centroid, returns {"eta_minutes": int}
- checklist_dispatcher(booking_id, checklist_type: "arrival"|"completion") → returns checklist items
- feedback_collector(booking_id, customer_rating, comment) → writes to Firestore, updates provider rating

System prompt:
"""
You manage the service execution lifecycle.
When provider updates status: validate the transition is legal (e.g. can't skip to COMPLETED from EN_ROUTE).
Valid transitions: CONFIRMED → EN_ROUTE → ARRIVED → IN_PROGRESS → COMPLETED
On EN_ROUTE: send customer ETA notification.
On ARRIVED: dispatch arrival checklist. Do not allow IN_PROGRESS until checklist complete.
On COMPLETED: dispatch completion checklist, then trigger feedback_collector after 10 min delay.
"""

━━━ FILE 2: adk/agents/dispute/agent.py ━━━
Google ADK LlmAgent named "dispute_agent".

Tools to implement in adk/agents/dispute/tools.py:
- booking_evidence_retriever(booking_id) → returns booking doc + photos + agreed_price + actual_price
- no_show_detector(booking_id) → checks if provider arrived (EN_ROUTE logged), returns {"no_show": bool}
- refund_calculator(agreed_price, claimed_price, evidence_strength) → returns {"refund_amount": float, "reason": str}
- compensation_issuer(user_id, amount, reason) → writes credit to Firestore wallet, returns {"credit_issued": float}
- blacklist_evaluator(provider_id) → checks if dispute count > threshold, returns {"blacklist": bool, "reason": str}
- escalation_router(dispute_id, reason) → marks for human review if can't auto-resolve

System prompt:
"""
You auto-resolve disputes fairly and quickly (target: under 2 hours).
Dispute types and resolution rules from PRD §5.7:
- Price dispute: customer says charged > agreed price → verify evidence → if valid, issue credit for difference
- No-show: provider never arrived → full refund + PKR 200 compensation
- Quality: subjective → if rating < 2 stars + photo evidence → 30% refund
- If dispute is filed > 48h after completion → reject (out of window)
- If same provider has 3+ disputes in 30 days → flag for blacklist review
Always explain the resolution in Roman Urdu/English mix.
"""

━━━ FILE 3: adk/agents/provider_agent/agent.py ━━━
Google ADK LlmAgent named "provider_agent".

Tools in adk/agents/provider_agent/tools.py:
- demand_forecaster(sectors: list, service_type: str, date: str) → demand levels per sector
- route_optimizer(provider_id, jobs: list) → optimal job order + travel sequence
- job_dispatcher(provider_id, job_id) → sends job alert to provider via FCM
- auto_reassign_engine(job_id, reason) → finds replacement provider if current declines

System prompt:
"""
You optimise the provider's day and manage job dispatch.
For each provider: order jobs by travel efficiency, forecast demand in their area, 
dispatch incoming jobs with the 90s timer alert.
If provider declines 3 jobs in a row: flag for low-activity review.
"""

━━━ FILE 4: data/seed.py ━━━
import firebase_admin
from firebase_admin import firestore
import json, argparse, sys

def seed_providers():
    db = firestore.client()
    providers = json.load(open('data/providers.json'))
    for i, p in enumerate(providers):
        db.collection('providers').document(p['provider_id']).set(p)
        print(f"Seeding provider {i+1}/{len(providers)}: {p['name']}", end='\r')
    print(f"\nSeeded {len(providers)} providers ✅")

def seed_scenario(scenario_name):
    db = firestore.client()
    data = json.load(open(f'data/scenarios/{scenario_name}.json'))
    for collection, docs in data.items():
        for doc_id, doc_data in docs.items():
            db.collection(collection).document(doc_id).set(doc_data)
    print(f"Scenario {scenario_name} seeded ✅")

if __name__ == '__main__':
    parser = argparse.ArgumentParser()
    parser.add_argument('--scenario', help='Scenario to seed (e.g. s1)')
    args = parser.parse_args()
    
    firebase_admin.initialize_app()
    if args.scenario:
        seed_scenario(args.scenario)
    else:
        seed_providers()
```

### Test after P3 Sprint 3

```bash
cd adk

# Test dispute agent with S5 scenario data
python -c "
from agents.dispute.agent import dispute_agent
import asyncio
result = asyncio.run(dispute_agent.run({
    'booking_id': 'KK-TEST-S5',
    'issue_type': 'PRICE_DISPUTE',
    'claimed_amount': 2850,
    'agreed_amount': 2500,
    'customer_id': 'test-cust'
}))
print('Dispute result:', result.get('resolution', 'ERROR'))
print('Refund amount:', result.get('refund_amount', 0))
assert result.get('refund_amount', 0) == 350, 'Wrong refund amount'
print('Dispute test PASSED ✅')
"

# Test seed script (dry run — no Firebase needed for JSON validation)
python3 -c "
import json
for s in ['s1','s2','s3','s4','s5','s6','s7','s8','s9']:
    data = json.load(open(f'data/scenarios/{s}_*.json'.replace('*', s.split('s')[1])))
    print(f'Scenario {s}: {list(data.keys())} ✅')
" 2>/dev/null || echo "Checking scenario files..."
ls data/scenarios/
```

**Pass criteria:** Dispute agent returns refund_amount = 350 for S5 scenario. All 9 scenario JSON files exist.

---

---

# Day 2 — Integration + Scenarios + Submit

## Integration Order (Person D leads, 9am–12pm)

```bash
# Step 1: Merge — Person D combines all ZIPs into one folder
# Everyone sends: ZIP of their folder(s) to shared Drive by 9am

# Step 2: Install everything
cd app && flutter pub get
cd ../adk && pip install -r requirements.txt

# Step 3: Wire pipeline.py with P3's agents
# Edit adk/pipeline.py — add after pricing:
# from agents.scheduling.agent import scheduling_agent
# from agents.booking.agent import booking_agent
# sub_agents=[intent_agent, matching_agent, pricing_agent, scheduling_agent, booking_agent]

# Step 4: Wire main.py with P3's routes (3 new endpoints)
# Step 5: Update Flutter api_service.dart baseUrl to 'http://localhost:8080'

# Step 6: Full integration test
cd adk && python main.py &
sleep 3
curl -s -X POST http://localhost:8080/v1/session/book \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer test-token" \
  -d '{"session_id":"int-test","user_input":"AC repair G-13 tomorrow morning","location":{"sector":"G-13","city":"Islamabad"},"user_id":"test"}' \
  | python3 -m json.tool

# Expected: booking_id starting with "KK-" in the response
```

## Scenario Test Scripts (12pm–3pm)

```bash
# S3 — Misspelled input
curl -s -X POST http://localhost:8080/v1/session/intent \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer test-token" \
  -d '{"session_id":"s3-test","user_input":"ac kaam ni kar raha chahiye koi g elevan mein","user_id":"test"}' \
  | python3 -c "import json,sys; r=json.load(sys.stdin); print('Confidence:', r.get('confidence_score')); print('Location:', r.get('intent_result',{}).get('location')); print('PASS ✅' if 0.65 <= r.get('confidence_score',0) <= 0.84 else 'FAIL ❌')"

# S1 — No provider available
python data/seed.py --scenario s1
curl -s -X POST http://localhost:8080/v1/session/book \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer test-token" \
  -d '{"session_id":"s1-test","user_input":"AC repair G-11 today","location":{"sector":"G-11","city":"Islamabad"},"user_id":"test"}' \
  | python3 -c "import json,sys; r=json.load(sys.stdin); print('Waitlisted:', r.get('waitlisted')); print('PASS ✅' if r.get('waitlisted') else 'FAIL ❌')"

# S5 — Price dispute
python data/seed.py --scenario s5
curl -s -X POST http://localhost:8080/v1/dispute/raise \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer test-token" \
  -d '{"booking_id":"KK-TEST-S5","issue_type":"PRICE_DISPUTE","claimed_amount":2850,"session_id":"s5-test","user_id":"test"}' \
  | python3 -c "import json,sys; r=json.load(sys.stdin); print('Refund:', r.get('refund_amount')); print('PASS ✅' if r.get('refund_amount')==350 else 'FAIL ❌')"

# S7 — Maps API failure (mock it)
export DISABLE_MAPS_API=true
curl -s -X POST http://localhost:8080/v1/session/match \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer test-token" \
  -d '{"intent_result":{"service_type":"AC_REPAIR","location":{"sector":"G-13","city":"Islamabad"}},"session_id":"s7-test","user_id":"test"}' \
  | python3 -c "import json,sys; r=json.load(sys.stdin); fb=any(p.get('is_approximate') for p in r.get('providers',[])); print('Fallback used:', fb); print('PASS ✅' if fb else 'FAIL ❌')"
unset DISABLE_MAPS_API

# S4 — Concurrent booking conflict (run two requests simultaneously)
python3 -c "
import asyncio, httpx

async def book():
    async with httpx.AsyncClient() as c:
        return await c.post('http://localhost:8080/v1/session/book',
            headers={'Authorization': 'Bearer test-token', 'Content-Type': 'application/json'},
            json={'session_id': 's4-test', 'user_input': 'AC repair G-13 tomorrow 10am',
                  'location': {'sector':'G-13','city':'Islamabad'}, 'user_id': 'test',
                  'force_provider': 'prov_001', 'force_slot': '2026-05-19T10:00:00'})

async def main():
    r1, r2 = await asyncio.gather(book(), book())
    statuses = [r1.json().get('status'), r2.json().get('status')]
    errors = [r1.json().get('error',{}).get('code'), r2.json().get('error',{}).get('code')]
    print('Statuses:', statuses)
    print('Errors:', errors)
    ok = ('CONFIRMED' in statuses or 'confirmed' in str(statuses)) and 'SLOT_CONFLICT' in str(errors)
    print('PASS ✅' if ok else 'FAIL ❌ — expected one CONFIRMED and one SLOT_CONFLICT')

asyncio.run(main())
"
```

## APK Build + Demo (3pm–5pm)

```bash
# Build release APK
cd app
flutter build apk --release
# APK location: app/build/app/outputs/flutter-apk/app-release.apk

# Install on physical device (connect via USB)
flutter install

# Submission checklist
echo "=== Submission Checklist ==="
ls -la app/build/app/outputs/flutter-apk/app-release.apk && echo "✅ APK exists"
ls -la docs/README.md && echo "✅ README exists"
python3 -c "import json; json.load(open('contracts/api_schemas.json')); print('✅ contracts valid')"
python3 -c "import json; d=json.load(open('data/providers.json')); print(f'✅ {len(d)} providers')"
ls data/scenarios/ | wc -l | xargs -I{} echo "✅ {} scenario files"
```

---

*KamKaaj 2-Day Sprint Plan — May 2026*
