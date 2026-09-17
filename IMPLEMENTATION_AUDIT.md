# Implementation Audit

Inspection of the existing KayKav Builder Impact demo, carried out before any
code was changed, per Phase 1 of the upgrade brief.

## 1. Current architecture

| Aspect | Finding |
|---|---|
| Framework | **None.** A single `index.html` (1,861 lines) of hand-written HTML, CSS and vanilla JS. |
| Build tooling | **None.** No `package.json`, `tsconfig.json`, bundler, linter, test runner or typechecker. |
| Deployment | Static file. Served by GitHub Pages (not yet enabled) and by the owner at `dashdemo.kaykav.academy`. |
| Rendering | Full re-render. `render()` builds an HTML string and assigns it to `#app.innerHTML`. |
| Routing | In-memory only. `state.route = {name, id}`; no URL, no history, no deep links. |
| Events | One delegated `click` listener on `document`, dispatching on `data-act` / `data-arg`. Survives re-render because it is bound to `document`, not to rendered nodes. |
| State | A single module-scoped `state` object: `{mode, route, actingAs, filters, checkin}`. |

This architecture is small, fast and completely adequate for a prototype. The
brief's Phase 3 instruction not to rewrite for theoretical cleanliness applies
directly: **it was kept.** No framework was introduced.

## 2. Existing screens

Two audience modes existed (`internal`, `funder`) plus `public` and `builder`.

| Route | View fn | Notes |
|---|---|---|
| `overview` | `vOverview` | Funnel, north-star tiles, seeded-data banner |
| `products` | `vProducts` | Product table, cohort/level filter chips |
| `product:<id>` | `vProduct` | Identity, ladder, metrics, timeline, evidence |
| `builders` | `vBuilders` | Builder table |
| `builder:<id>` | `vBuilder` | Builder profile |
| `checkins` | `vCheckins` | Check-in windows per cohort |
| `verify` | `vVerify` | Verification queue with accept/reject/more |
| `gaps` | `vGaps` | "What we don't know" |
| `dictionary` | `vDictionary` | Metric definitions |
| `stories` | `vStories` | Impact stories |
| `funder` | `vFunder` | Funder overview |
| `trust` | `vTrust` | How numbers are checked |
| `public` | `vPublic` | Public hero + gallery |
| builder mode | `vBuilderHome` | Check-in flow, `vCheckinForm`, `vCheckinDone` |

## 3. Existing components worth preserving

These are good and were reused rather than rebuilt:

- `confMeter(conf)` — the four-pip verification strength meter
- `figure(metric, opts)` — renders a value **or** a visible gap, never a zero
- `levelPips(level)` / `levelPill(level)` — maturity position
- `openDrawer` / `closeDrawer` / `toast` — side panel and feedback
- `.kk-table`, `.kk-panel`, `.kk-chip`, `.kk-metric`, `.kk-linkcell`, `.kk-rowlink`, `.kk-flag`, `.kk-empty`, `.kk-gapcell`
- The full design-token CSS block (lines 14–306): Fraunces/Inter, blue `#295898`, gold `#B18C54`, and a complete dark-mode token set

## 4. Existing data model

A single `DB` literal with **denormalised** arrays:

```
programmes[2]  cohorts[3]  builders[20]  products[18]
checkins[8]    builderCheckins[6]  queue[4]  stories[2]  noProduct[2]
```

Metrics were nested **inside** each product as a fixed six-key object
(`users, mau, paying, revenue, mrr, orgs`), each `{value, conf, source, date}`
or a `gap(why)`. Status was a single `level` integer plus a `history` map.

### Weaknesses found

1. **Metrics were a fixed shape.** A civic tool and a SaaS product were forced
   into the same six keys. The Master Data Model requires an open metric-type
   list per product.
2. **No time series.** Metrics held one current value. `timeline[]` existed
   separately and was not connected to the metric records.
3. **No evidence entity.** Evidence was a free-text string on a metric and a
   separate `queue[]`. Evidence could not be shared by two claims, and a
   rejected claim had nowhere to live.
4. **No builder outcomes.** Dimension B of the framework (what happened to the
   *person*) was represented only as `continuing: yes|no|unknown`.
5. **No domain impact entity.** `impact[]` was an untyped label/value pair with
   no methodology field.
6. **Status could not move backwards.** `level` was monotonic; PAUSED,
   PIVOTED and SHUT_DOWN were unrepresentable — the framework requires them.
7. **Participants and enrolments were fused.** One `builders[]` row carried both
   identity and cohort membership, so a participant could not join two
   programmes.

## 5. Existing strengths (explicitly preserved)

The prototype already got the hardest conceptual thing right, and this was kept
intact:

- **Unknown ≠ zero.** `gap(why)` renders `—` with a reason, never `0`.
- **A verification ladder** already existed (`conf` 0–3) and was surfaced in the UI.
- **Gaps are a feature.** A whole screen was devoted to what is not known.
- **The funnel already narrows**, and the copy says the narrowing is expected.
- Editorial visual language that reads as institutional rather than SaaS.

## 6. Required changes

| # | Change | Driver |
|---|---|---|
| R1 | Normalise the data layer into 13 entities with real foreign keys | Data Model §3 |
| R2 | Add a selector layer so no component hardcodes a metric | UI Spec §44, §53 |
| R3 | Restore canonical cohort figures (C01 32/6/3/12, C02 8) | UI Spec §2 — **see conflict C1** |
| R4 | Rebuild navigation to the 10-screen IA | Brief Phase 4 |
| R5 | Add Outcomes, Cohorts, Reports, Data Quality screens | UI Spec §26–38 |
| R6 | Add non-linear product states (paused, pivoted, shut down) | Framework §30 Rule 8 |
| R7 | Add rejected evidence and needs-clarification states | UI Spec §32 |
| R8 | Add a global filter bar driving every analytical screen | UI Spec §7 |
| R9 | Separate builder outcomes from product metrics | Framework §8 |
| R10 | Add domain impact with a `methodology` field | Data Model §21 |
| R11 | Add revenue attribution (direct/partial/associated/unknown) | Data Model §15 |
| R12 | Keep KayKav programme revenue out of participant impact | Framework §30 Rule 5 |

## 7. Potential conflicts

### C1 — Canonical cohort sizes vs. the current seed (BLOCKING, resolved)

The specs state C01 = **32** participants and C02 = **8**, and make them
acceptance tests (UI Spec §60 Scenarios 1–2). The current demo holds
C01 = **78** and C02 = **72**, totalling 150, with 47 generated builders.

Those inflated figures were introduced in earlier sessions at the owner's
explicit instruction, to stop the dashboard looking empty. They now directly
violate the strongest rule in the spec set: *"Never invent demo data in a way
that contradicts canonical source facts."*

Resolved in `DECISIONS.md` (D1) in favour of the specs. The dashboard is kept
populated by richer per-record depth, not by inflated head-counts.

### C2 — "150+ people trained" vs. "do not manufacture a number"

UI Spec §11 and §57 both want a `150+ PEOPLE TRAINED` card. Data Model §33 says
the 150+ claim is **not yet substantiated** and the platform "should not
manufacture a precise historical number."

Resolved in `DECISIONS.md` (D2): the card shows both — the 150+ claim, labelled
as unreconciled, above the 40 participant records actually held.

### C3 — Funnel ordering

Framework §3 puts `PRODUCTS WITH TRACTION` between users and revenue. UI Spec
§12 lists the same. The existing demo had an extra `Active` stage. Kept, since
Framework §5 defines Level 4 Active and Level 5 Traction as distinct.

### C4 — No test/lint/build to run

Brief Phase 13 says to run lint, typecheck, tests and build. None exist, and
introducing a toolchain would contradict Phase 3. Resolved in `DECISIONS.md`
(D7): a dependency-free self-check harness was added instead.

## 8. Questions and ambiguities

Recorded rather than escalated; each has a documented reversible decision.

1. **Does "150+" cover BNP only, or all KayKav activity?** Unresolved at source.
   Treated as programme-wide and unreconciled (D2).
2. **How many of C01's 32 participants have records?** Not established. 14 are
   modelled as held, 18 as awaiting backfill, which matches the source's own
   statement that C01 records are incomplete.
3. **Which 12 C01 products shipped?** The source names 7. The other 5 are
   modelled as shipped-but-unnamed records, preserving the canonical 12 without
   inventing product names.
4. **Is DEB in scope?** It has not run. Modelled as a programme with a planned
   cohort and no outcomes, so multi-programme UI can be demonstrated without
   inventing results.
5. **Does Cohort 02 have countries beyond Nigeria?** Not established. Left as
   Nigeria only.
