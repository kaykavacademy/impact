# Decision Log

Architectural and data decisions taken while upgrading the demo, in the format
the brief asked for: decision, reason, alternative considered, impact. Anything
here that affects future production engineering is flagged.

---

## D1 — Canonical cohort sizes restored; the inflated seed removed

**Decision.** Cohort 01 is 32 participants and Cohort 02 is 8. The generated
seed layer that had inflated them to 78 and 72 (150 total, 47 synthetic
builders) was deleted.

**Reason.** All three specs make these canonical, and UI Spec §60 Scenarios 1–2
make them acceptance tests. The strongest rule in the spec set is *"Never invent
demo data in a way that contradicts canonical source facts."* The inflated
figures violated it directly.

**This reverses an explicit earlier instruction.** In previous sessions the
owner asked for the totals to read 150 and for enrolment to be spread evenly
across cohorts, so the dashboard would not look empty to stakeholders. That
instruction and this specification cannot both be satisfied, so the conflict is
recorded here rather than silently resolved.

**Alternative considered.** Keep the inflated numbers in a clearly-labelled
third "demo cohort" so head-counts stay high. Rejected: it would still have put
a fabricated participant count into the People Trained card, and the funnel
would have been dominated by records with no provenance.

**Impact.** The dashboard now shows 40 enrolments rather than 150. It is kept
populated by depth instead of headcount — 21 products, 13 evidence items, 80
check-in records, metric time series, builder outcomes and domain impact. Every
screen still fills. If the owner wants the larger numbers back, this is the one
decision to revisit, and doing so will fail the canonical acceptance tests.

---

## D2 — "150+ people trained" shown as a claim, never as a count

**Decision.** `DB.reachClaim` holds the 150+ figure with `dataMode:
'unreconciled'`. It appears as supporting text on the People Trained card and in
the report, and is excluded from every computed total.

**Reason.** UI Spec §11 and §57 both want a 150+ card. Data Model §33 says the
claim is not yet substantiated and the platform "should not manufacture a
precise historical number." Showing it as a labelled claim satisfies both.

**Alternative considered.** Print 150+ as the headline. Rejected — it would make
the most prominent number on the dashboard the one number with no records
behind it.

**Impact.** Data Quality carries a "reconciliation gap" panel. Production will
need a real reconciliation before this figure can be reported.

---

## D3 — Vanilla single-file architecture kept

**Decision.** No framework, bundler or TypeScript was introduced. The upgrade is
still one `index.html`.

**Reason.** Brief Phase 3 says not to rewrite because another architecture might
be theoretically cleaner. The existing delegated-event, full-re-render model is
adequate at this data volume and has zero build friction for a prototype.

**Alternative considered.** Port to React + Vite to match a likely production
stack. Rejected for this phase; `PRODUCTION_HANDOFF.md` maps how the layers port.

**Impact.** No `npm install` to run the demo. The data and selector layers were
written to port cleanly regardless.

---

## D4 — A selector layer sits between data and views

**Decision.** Every screen reads through `sel.*`. No view computes a headline
from a literal or reaches into `DB` directly for an aggregate.

**Reason.** Brief Phase 6 and UI Spec §53. It is also what makes the eventual
API swap a single-layer change.

**Impact.** `sel.funnel()`, `sel.economicValue()`, `sel.survival()` and the rest
become the API contract. See the handoff note.

---

## D5 — The funnel counts the furthest stage a product ever reached

**Decision.** `sel.peakRank()` drives the funnel; `sel.currentStatus()` drives
the maturity distribution.

**Reason.** Framework §30 Rule 8 says a product can move backwards. A product
that reached users and later paused genuinely reached those users — removing it
from "products with users" would rewrite history. But "where products are today"
must show it as paused.

**Alternative considered.** Use current status everywhere. Rejected: the funnel
would shrink whenever a product paused, which misrepresents what happened.

**Impact.** Two different numbers legitimately describe the same product. Both
are labelled. Production must preserve status history, not just current status.

---

## D6 — Metrics are a time series keyed by type, not columns on the product

**Decision.** `DB.metrics` is a flat list of `{productId, type, value,
periodEnd, source, verification, evidenceId}`.

**Reason.** Data Model §12 requires it, and a civic tool and a SaaS product do
not share a metric set. It also makes charts and growth possible.

**Impact.** Adding a metric type needs no schema change. `METRIC_META` is the
only place a new type is registered.

---

## D7 — A self-check harness instead of a test toolchain

**Decision.** No linter, typechecker or test runner was added. Correctness was
verified by driving the real page in a headless browser and asserting the
canonical figures, plus a JS parse check.

**Reason.** Brief Phase 13 says to run lint/typecheck/tests/build; none exist,
and adding a toolchain contradicts Phase 3. The acceptance criteria that matter
here are about rendered output and data integrity, which a compile step would
not have caught anyway.

**Alternative considered.** Add Vitest + ESLint. Rejected for this phase;
recommended at production in the handoff.

**Impact.** No CI. Re-run the QA scripts manually after data edits.

---

## D8 — Evidence is a first-class entity and verdicts cascade

**Decision.** `DB.evidence` is separate from metrics; metrics cite it by
`evidenceId`. Accepting or rejecting evidence updates every metric citing it.

**Reason.** Data Model §19 and §43 separate fact, claim, evidence and
verification. It also means the demo can show a rejection *weakening* a number,
which is the behaviour a funder should be able to interrogate.

**Impact.** Rejecting evidence drops the claim to `SELF_REPORTED` rather than
deleting it. A production audit trail should record who changed what and when —
not modelled here.

---

## D9 — Cohort 01 has 12 shipped products, five of them unnamed

**Decision.** Seven named products from the source, plus five records that
shipped without a name on file.

**Reason.** The canonical figure is 12 and the source names 7. Inventing five
product names would fabricate canonical-looking facts.

**Impact.** Five rows render as "Unnamed product (PR08)" with "record
incomplete". Data Quality counts them as missing names. This is intentional —
the gap is the point.

---

## D10 — Programme revenue modelled as a separate record with a null value

**Decision.** `DB.programmeRevenue` exists at top level, holds no amount, and is
never read by `sel.economicValue()`.

**Reason.** Framework §30 Rule 5. Modelling it explicitly demonstrates the
separation; leaving the amount null avoids inventing KayKav's finances.

**Impact.** Production must keep programme income in a different table from
participant economic impact, not merely a different column.

---

## D11 — Jobs created is `null`, not `0`

**Decision.** The economic-value selector returns `jobs: null`, and the UI shows
`—  Not yet measured`.

**Reason.** KayKav has not asked the question. Unknown ≠ zero (UI Spec §9).

**Impact.** A funder sees an honest gap instead of a discouraging zero.

---

## D12 — Old routes aliased rather than removed

**Decision.** `verify → evidence`, `gaps → data-quality`, `trust → evidence`
(methodology tab), `dictionary → data-quality`.

**Reason.** Brief Phase 4 asks to preserve routes where practical. The demo is
already deployed at `dashdemo.kaykav.academy`.

**Impact.** Nothing previously linked breaks. Routing is still in-memory; there
are no URLs to bookmark, which production should change.
