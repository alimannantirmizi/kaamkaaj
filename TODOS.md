# KamKaaj — TODOs

## Design

### TODO-D1: Flutter ThemeData from design tokens
**What:** Create `lib/theme/app_theme.dart` with Flutter `ThemeData` populated from all tokens in PRD §16.6.
**Why:** Prevents engineers from hardcoding `#1B6B6B` or `14.sp` across files. Single source of truth for colors, fonts, spacing.
**Pros:** Consistent UI across all screens; easy to iterate on colors pre-demo.
**Cons:** ~15 min upfront; small but real coordination cost.
**Context:** PRD §16.6 defines the full token set. This TODO just scaffolds it into Flutter code.
**Blocks:** Nothing. **Blocked by:** Nothing.

### TODO-D2: Urdu semantic labels for screen reader support
**What:** Write `Semantics(label: '...')` strings in Roman Urdu for all interactive elements: mic button, nav tabs, provider card, accept/decline buttons, checklist items.
**Why:** Without this, a screen reader user hears "button" or English labels in a Urdu-first app. Critical for production; not blocking for competition demo.
**Pros:** Accessible to visually impaired users; demonstrates inclusive design in competition submission.
**Cons:** ~1 hour to enumerate and test all labels.
**Context:** PRD §16.8 requires semantic labels. This TODO produces the actual label strings.
**Blocks:** Nothing. **Blocked by:** Flutter scaffold.
