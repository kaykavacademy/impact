# KayKav Builder Impact — demo dashboard

An interactive prototype of the KayKav Impact Platform, built to validate the
information architecture and metric hierarchy **before** production engineering
starts. It is not the production system and holds no real impact data.

The question it is designed to answer:

> What happened to the people KayKav trained, what did they build, did anyone
> actually use it, and did any of it create measurable value?

## Run it

```
python3 -m http.server 8000
# open http://localhost:8000
```

No build step, no dependencies, no backend. One `index.html`.

## What you are looking at

Four modes, switched from the header:

| Mode | Who it is for |
|---|---|
| **Internal Observatory** | KayKav staff. Full operational tooling. |
| **Funder View** | External read-only. Same data, impact-first, no operational tooling. |
| **Public** | Curated, privacy-safe. |
| **Builder** | What a participant sees when a check-in is due — a working questionnaire that writes back into the data. |

Screens: Overview · Outcomes · Impact Stories · Builders · Products · Cohorts ·
Check-ins · Evidence · Data Quality · Reports, plus builder, product, cohort and
story detail views.

## The thing to test

Traceability. Every headline should walk down to the record behind it:

```
Headline number → Cohort → Builder → Product → Metric → Evidence
```

Click any snapshot card or funnel stage and you should land on the filtered
records, not a dead end.

The other thing worth exercising is the **check-in** in Builder mode. It is the
only participant-facing write path: the answers create metric, status history,
evidence and outcome records, and a review step shows exactly what will be
written and at what verification strength before anything is saved. Skipping a
question writes nothing at all — it does not write a zero.

## Which figures are real

| Category | What it covers |
|---|---|
| **Canonical** | BNP Cohort 01: 32 students, 6 countries, 3 continents, 12 live products shipped, 4 weeks. BNP Cohort 02: 8 students, started 1 July 2026. Programme and cohort dates. The seven named Cohort 01 products. |
| **Illustrative** | Every user count, revenue figure, outcome, evidence item, domain-impact measure and story. Created to exercise the interface. |
| **Unknown** | Anything shown as `— Not yet measured`. Genuinely not collected. |
| **Unreconciled** | The "150+ people trained" claim. Shown as a claim, excluded from all totals, pending reconciliation against programme records. |

Nothing downstream of a cohort's canonical figures is a measurement. A banner on
every screen says so, and it should stay until the data is real.

## Principles the prototype enforces in code

- **Unknown is never zero.** A missing measurement renders `— Not yet measured`.
  A measured zero renders `0` with its verification level.
- **Nothing is silently promoted to fact.** Every figure carries one of:
  Self-reported · Evidence submitted · KayKav verified · Connected data ·
  Needs clarification · Rejected. Always as words, never colour alone.
- **Built is not success.** The funnel narrows from trained → built → deployed →
  users → traction → revenue → business, and the narrowing is the point.
- **Products move backwards.** Paused, pivoted and shut-down products stay
  visible. A dashboard where everything succeeds is a marketing page.
- **Programme revenue is never participant impact.** KayKav's own income is a
  separate record and enters no impact total.

## Mock data architecture

Thirteen normalised entities with real foreign keys, following the Master Data
Model:

```
programmes → cohorts → participants → enrolments
                            ↓
                        products → statusHistory
                                 → metrics → evidence → verification
                                 → domainImpacts
                            ↓
                        outcomes · checkins · stories
```

Screens never read `DB` directly. Everything goes through the `sel.*` selector
layer, so swapping the mock data for an API means reimplementing one layer.

## Known limitations

- **No URLs.** Routing is in-memory, so no screen can be linked or bookmarked.
- Funder View is a UI toggle, not a security boundary.
- Export and share are mocked.
- Connected-data sync is simulated.
- `TODAY` is pinned to 2026-09-17 so check-in states stay stable.

Full list in `DASHBOARD_GAPS.md`.

## Documents

| File | Contents |
|---|---|
| `IMPLEMENTATION_AUDIT.md` | What existed before the upgrade, and what had to change |
| `IMPLEMENTATION_PLAN.md` | Screen-by-screen mapping: reused, new, data required |
| `DECISIONS.md` | Every architectural and data decision, with alternatives |
| `DASHBOARD_GAPS.md` | What the prototype exposed — the main output of the exercise |
| `PRODUCTION_HANDOFF.md` | What survives, what must be replaced, what is unresolved |
| `RECONCILIATION.md` | Prototype figures vs. published KayKav claims |
