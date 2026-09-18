# Dashboard Gaps

What the prototype exposed. This is the most useful output of the exercise: the
demo exists to find these before engineers build the real system.

## 1. Data-model gaps

| # | Gap | Why it matters |
|---|---|---|
| G1 | **No audit trail.** A metric can change but nothing records who changed it, when, or from what. | Data Model §46 requires it. Right now a verified figure could silently become a different verified figure. |
| G2 | **No `Organisation` entity.** `ORGANISATIONS_SERVED` is an integer, so "6 firms" cannot be joined to which firms. | Blocks any claim about repeat customers or sector concentration. |
| G3 | **Revenue has no currency handling.** Amounts are stored as USD numbers. The source cites ₦ and £ figures. | Mixed-currency totals are currently impossible to compute honestly. |
| G4 | **Attribution exists on builder outcomes but not on product revenue.** | A builder's product might earn money only partly because of KayKav; there is nowhere to say so. |
| G5 | **No consent enforcement.** `stories.consent` exists but nothing checks it before rendering a name. | Publishing a participant's name needs a hard gate, not a field. |
| ~~G6~~ | ~~Check-in responses are not stored.~~ **Closed.** The check-in flow now writes `responses` onto the check-in record, and creates the metric, status, evidence and outcome rows the answers imply. | — |
| G7 | **No `period_start` on metrics.** Only `period_end` is populated. | "MAU for August" and "MAU as of August" are different claims. |
| G8 | **Products cannot be linked.** A pivot creates a new product with no pointer to its predecessor. | Ideas Journal pivoted, but the successor is invisible. |

## 2. UI gaps

| # | Gap |
|---|---|
| U1 | **No URLs.** Routing is in-memory, so no screen can be linked, bookmarked, or sent to a funder. The single most-requested thing a report reader will want. |
| U2 | **No loading or error states.** Data is synchronous and local, so there is nothing to load. Skeletons are specified (§41) and will be needed once an API exists. |
| U3 | **No bulk actions in the verification queue.** A reviewer with 40 items would want to act on several at once. |
| U4 | **Charts are single-metric sparklines.** No comparison, no overlay, no axis labels beyond endpoints. |
| U5 | **Filter state is not preserved across a mode switch**, so moving to Funder View resets scope. |
| U6 | **Mobile navigation scrolls horizontally rather than collapsing into a drawer.** Usable, not ideal; the spec allows simplification here. |
| U7 | **No print stylesheet** for the report preview, which is the screen most likely to be printed. |

## 3. Metric-definition gaps

| # | Question that has no agreed answer |
|---|---|
| M1 | **What makes a product "Active"?** Framework §6 proposes "one meaningful interaction in 30 days" but "meaningful" is per-product and undefined. The demo uses status history, which is a proxy. |
| M2 | **What is the traction threshold?** §6 says it depends on category and gives no rule. Currently set by hand per product. |
| M3 | **Is a paused product counted in survival?** The demo says no (it must be currently on the ladder *and* have reached Validated). Reasonable, not agreed. |
| M4 | **Does a shut-down product still count as "shipped"?** The demo says yes, because it did ship. This makes "12 shipped" stable over time, but a funder may read it as "12 live today". |
| M5 | **Do multiple products by one builder count once or many in the funnel?** Products count individually; builders are deduplicated. Not stated anywhere. |
| M6 | **What is "economic value"?** The demo totals verified product revenue only, excluding self-reported builder income. Defensible, but it means the headline understates. |

## 4. Evidence gaps

| # | Gap |
|---|---|
| E1 | **Evidence has no expiry.** A screenshot from June is treated the same as one from last week. `expires_at` is in the Data Model and unimplemented. |
| E2 | **No re-verification cadence.** Once verified, a figure stays verified forever. |
| E3 | **A rejected claim has no resolution path.** Rejecting drops it to self-reported and nothing schedules a follow-up. |
| E4 | **No evidence for absence.** "This builder reported no outcome" is recorded, but there is no way to evidence that it was genuinely asked and answered. |
| E5 | **Connected data is simulated.** Nothing actually syncs; `CONNECTED_DATA` is a label. |

### New gaps the check-in flow exposed

| # | Gap | Why it matters |
|---|---|---|
| G9 | **A self-report cannot demote a product, by design.** Reporting "still building" on an Active product is noted but not written, because a form answer should not silently walk a product back down the ladder. Explicit stops (paused, pivoted, shut down) *are* written. This asymmetry is defensible but unratified. | A genuine regression reported by a builder is currently invisible until a reviewer acts. |
| G10 | **"Actively growing" maps to Active, not Traction.** Traction needs sustained evidence (Framework §6), which one form answer cannot establish. | Products may sit at Active when they deserve Traction, until someone reviews. |
| G11 | **Re-submission is not modelled.** A completed window cannot be reopened or corrected. | Data Model §46 wants an audit trail of corrections. |
| G12 | **Evidence is claimed, not uploaded.** The builder picks a type; no file is attached. | Real capture needs storage and a virus/size policy. |

## 5. Engineering questions

1. Does routing move to real URLs with deep links, and does that imply a router library or hand-rolled `history.pushState`?
2. Where does verification actually happen — in-app review, or an external workflow that writes back?
3. Who owns the reconciliation of the 150+ claim, and does the platform store the reconciled number or keep deriving it?
4. Should the demo's synthetic Cohort 01 names (Tola A., Rukayat B. …) be replaced with real records at import, and what happens to product records already linked to them?
5. Multi-currency: store minor units plus ISO code and convert at read time, or normalise at write?
6. Do funders get accounts, or is the Funder View a shareable link with a token?

## 6. Unresolved decisions

- **D1 stands against an explicit owner instruction.** The canonical 32/8 figures are restored and the 150-total seed removed. This needs an explicit owner call before the demo is next shown.
- **The seventh Cohort 01 country is still unidentified.** The partner site claims 7; the record lists 6. Carried over from `RECONCILIATION.md` and still open.
- **Oneflute** is named as a live product on the partner site and has no record here.
- **Is DEB in scope for the prototype at all?** Modelled as a planned programme with no outcomes, purely so multi-programme UI can be shown.
