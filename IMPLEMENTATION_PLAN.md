# Implementation Plan

Phase 2 mapping. For each screen: what exists today, what it becomes, what is
reused, what is new, and what data it needs.

Build order follows Phase 5 of the brief: Overview first, because it sets the
metric hierarchy and visual language every later screen inherits.

## Legend

`REUSE` — existing component kept as-is · `EXTEND` — existing component gains a
capability · `NEW` — did not exist.

---

## 1. Overview

```
CURRENT   overview (vOverview) — funnel + north-star tiles
   ↓
V2        Impact Overview — snapshot, funnel, maturity, cohort progression,
          longitudinal, economic value, known/unknown, stories
   ↓
REUSE     funnelHTML, kk-metric tiles, confMeter, figure, kk-flag banner,
          survivalCard, drawer drill-downs
   ↓
NEW       ImpactSnapshot (8 cards w/ tooltips + verification split),
          MaturityProgression (segmented bar incl. paused/pivoted/shut down),
          CohortProgression table, ImpactTimeline (30/90/180/365),
          EconomicValue block, MeasurementHealth strip (internal only)
   ↓
DATA      sel.funnel(f), sel.snapshot(f), sel.maturity(f),
          sel.cohortProgression(), sel.longitudinal(f), sel.economicValue(f),
          sel.knowns(f), sel.unknowns(f), stories where featured
```

## 2. Builders

```
CURRENT   builders (vBuilders) — flat table, no filters
   ↓
V2        Builder Observatory — filterable directory
   ↓
REUSE     kk-table, kk-rowlink whole-row click, kk-linkcell, confMeter
   ↓
NEW       Columns for Current outcome + Latest check-in; outcome/verification
          filters; sortable headers
   ↓
DATA      sel.participants(f) joined to enrolments, products, outcomes, checkins
```

## 3. Builder profile

```
CURRENT   builder:<id> (vBuilder) — products + continuation
   ↓
V2        Identity · Products · Outcomes · Timeline · Evidence
   ↓
REUSE     product cards, levelPill, figure, confMeter
   ↓
NEW       OutcomeCard (economic/career, with attribution), builder Timeline
          (joined → completed → built → deployed → first users → first revenue
          → latest check-in), evidence list
   ↓
DATA      participant, enrolments, products, statusHistory, outcomes, evidence
```

## 4. Products

```
CURRENT   products (vProducts) — table, cohort + level chips
   ↓
V2        Product Observatory — full column set, sorting, richer filters
   ↓
REUSE     entire table incl. the row-click fix, filter chips, levelPips
   ↓
NEW       Active-users and Last-check-in columns, sortable headers,
          domain + verification + status filters, has_users / revenue presets
          reachable from Overview funnel nodes
   ↓
DATA      sel.products(f), sel.metric(productId, type), sel.lastCheckin(productId)
```

## 5. Product detail

```
CURRENT   product:<id> (vProduct) — ladder, metrics, timeline, evidence
   ↓
V2        + lifecycle incl. non-linear states, metric chart, event history
   ↓
REUSE     ladder, identity dl, metric grid, evidence list
   ↓
NEW       MetricChart (inline SVG sparkline, only when ≥3 points),
          ProductHistory event list (date · event · source · verification),
          paused/pivoted/shut-down treatment with reason
   ↓
DATA      product, statusHistory, metrics (time series), domainImpacts, evidence
```

## 6. Outcomes

```
CURRENT   (none)
   ↓
V2        Outcomes — Economic · Career · Product · Social/Domain
   ↓
REUSE     kk-metric, kk-panel, figure, confMeter
   ↓
NEW       OutcomeCard, domain-impact table w/ methodology column,
          attribution badges
   ↓
DATA      sel.outcomes(f) grouped by category, sel.domainImpacts(f)
```

## 7. Cohorts

```
CURRENT   (none — cohorts existed only as a filter)
   ↓
V2        Cohorts directory · Cohort detail (tabbed) · Cohort comparison
   ↓
REUSE     kk-panel cards, kk-table
   ↓
NEW       CohortCard (canonical figures + data-mode badge),
          cohort detail tabs, comparison table (no ranking language)
   ↓
DATA      cohorts + canonical block, enrolments, products, checkins, evidence
```

## 8. Check-ins

```
CURRENT   checkins (vCheckins) — window completion per cohort
   ↓
V2        + Due/Overdue/Completed queue, per-row detail, simulated submit
   ↓
REUSE     window cards, existing builder check-in form (vCheckinForm)
   ↓
NEW       CheckInStatus badge, check-in table, check-in detail drawer
   ↓
DATA      sel.checkins(f) joined to participant + product
```

## 9. Evidence

```
CURRENT   verify (vVerify) — queue with accept/reject/more
   ↓
V2        Evidence — Verification Queue tab + Evidence Library tab
   ↓
REUSE     queue rows, decide() actions, toast
   ↓
NEW       Tabs, Evidence Library, claim detail (source/evidence/submitted/
          status), rejected + needs-clarification states
   ↓
DATA      sel.evidence(f), sel.claims(f) joining metric → evidence → verification
```

## 10. Impact Stories

```
CURRENT   stories (vStories) — two narrative panels
   ↓
V2        Story library + story detail with evidence-graded language
   ↓
REUSE     panels
   ↓
NEW       ImpactStoryCard, story detail (Builder/Problem/Product/Journey/
          Result/Evidence), language that downgrades unverified claims
   ↓
DATA      stories joined to participant, product, metrics, evidence
```

## 11. Reports

```
CURRENT   (none)
   ↓
V2        Report builder + preview + mocked export/share
   ↓
REUSE     all metric blocks from Overview
   ↓
NEW       ReportBuilder controls, ReportPreview document layout
   ↓
DATA      the same selectors the Overview uses — no separate dataset
```

## 12. Data Quality

```
CURRENT   gaps (vGaps) — "what we don't know"
   ↓
V2        Data Quality — completeness + confidence per entity, gap register
   ↓
REUSE     the whole gaps concept and its copy
   ↓
NEW       DataQualityCard (complete/incomplete/unknown/conflicting),
          completeness vs. confidence bars, per-entity breakdown
   ↓
DATA      sel.dataQuality() computed over every entity
```

## 13. Funder View (Mode B)

```
CURRENT   funder (vFunder) — separate hand-written overview
   ↓
V2        Reach → Products → Adoption → Economic value → Outcomes →
          Domain impact → Longitudinal → Evidence
   ↓
REUSE     the same selectors as Mode A — no second dataset
   ↓
NEW       Funder nav (5 items), section ordering, operational tooling hidden
   ↓
DATA      identical to internal; only the presentation differs
```

---

## Route aliases

Old routes are kept working so nothing that was linked breaks:

| Old | New |
|---|---|
| `verify` | `evidence` |
| `gaps` | `data-quality` |
| `trust` | `evidence` (methodology panel) |
| `dictionary` | retained, linked from Data Quality and metric tooltips |
| `public` | retained as a third mode |

## Data layer

```
/data (conceptual — one file, sectioned)
  programmes · cohorts · participants · enrolments · products
  statusHistory · metrics · outcomes · checkins · evidence
  domainImpacts · stories · verifications
```

Every screen reads through `sel.*` selectors. No component computes a headline
number from a literal.
