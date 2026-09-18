# Production Handoff

What should survive from this prototype, what must be replaced, and what is
still unresolved. Read alongside `DASHBOARD_GAPS.md`.

## 1. What should survive into production

### The selector layer — the most valuable artefact here

`sel.*` in `index.html` is the whole contract between data and interface. Every
screen reads through it; no component computes an aggregate itself. Each
selector maps to an endpoint or a query:

| Selector | Becomes |
|---|---|
| `sel.funnel(filters)` | `GET /impact/funnel?programme=&cohort=&period=` |
| `sel.snapshot` / `sel.usersReached` | `GET /impact/snapshot` |
| `sel.maturity(filters)` | `GET /products/maturity` |
| `sel.survival(cohortId, window)` | `GET /cohorts/:id/survival` |
| `sel.economicValue(filters)` | `GET /impact/economic` |
| `sel.health(filters)` | `GET /ops/health` |
| `sel.dataQuality()` | `GET /ops/data-quality` |
| `sel.products` / `sel.participants` | `GET /products`, `GET /participants` |
| `sel.series(productId, type)` | `GET /products/:id/metrics?type=` |

Keep the definitions exactly. They encode agreed semantics — particularly
`peakRank` vs `currentStatus` (D5), which is easy to get wrong.

### Data structures that should become API contracts

The thirteen entities map 1:1 to the tables in Data Model §44. Worth carrying
over verbatim:

- **`statusHistory`** — append-only, never overwrite a product's status. Time to
  deploy, time to revenue and survival all derive from it.
- **`metrics`** — `{productId, type, value, periodEnd, source, verification,
  evidenceId}`. Do not collapse into columns on `products`.
- **`evidence`** with cascading verdicts — verifying evidence must update every
  metric citing it (see `applyVerdict`).
- **`outcomes`** — carries `attribution`, which prevents inflated claims.
- **`domainImpacts`** — the `methodology` free-text field is load-bearing. It is
  the difference between "10,000 hours saved" and an interrogable claim.

### Components worth porting

`vbadge` (verification with text, never colour alone), `mval` (the single place
the unknown-vs-zero rule lives), `statusPill` / `statusPips`, `metricChart`,
`maturityBar`, `filterBar`, `emptyState`, the drawer, and the whole design-token
CSS block. The tokens already cover light and dark and meet contrast.

### Rules encoded in code, not just docs

1. `mval()` renders `— Not yet measured` when there is no record, and `0` only
   when zero was measured.
2. `sel.economicValue()` never reads `DB.programmeRevenue`.
3. `sel.storyClaim()` refuses to write a self-reported figure as fact.
4. `jobs` returns `null`, never `0`.

## 2. Mock data that must be replaced

| Layer | Status |
|---|---|
| Cohort sizes, countries, continents, C01's 12 shipped | **Canonical** — keep |
| Programme and cohort dates, session counts | **Canonical** — keep |
| Cohort 02 participant initials | **Real people** — replace with proper records under consent |
| Cohort 01 participant names (Tola A. …) | **Invented placeholders** — replace at import |
| All metric values, evidence, outcomes, domain impact, stories | **Illustrative** — replace entirely |
| `reachClaim` (150+) | **Unreconciled claim** — resolve before it is reported |

Nothing downstream of a cohort's canonical figures is real. The banner on every
screen says so, and it should stay until the data does.

## 3. Screens requiring backend support

| Screen | Needs |
|---|---|
| Evidence / verification queue | Write path, reviewer identity, audit trail, file storage |
| Check-ins | Scheduling, outbound email/SMS, response storage, and a transactional write path — `submitCheckin()` currently mutates six collections in sequence with no rollback if one step fails |
| Reports | Server-side rendering for PDF; the preview is HTML only |
| Data Quality | Scheduled recomputation; expensive to derive per request at scale |
| Product detail | Connected-data sync (Paystack, Stripe, PostHog) behind `CONNECTED_DATA` |
| Search | Fine client-side at 21 products; needs an index beyond a few hundred |
| Any shareable view | Real URLs — see U1, the single biggest missing capability |

## 4. Explicitly not built (per UI Spec §61)

Authentication, billing, real analytics integrations, automated evidence
ingestion, AI-generated impact claims, RBAC, PDF infrastructure, email
notification, funder account management, causal-impact analysis.

The Funder View is a UI toggle, not a permission boundary. **It hides
operational tooling; it does not secure it.** Production needs real
authorisation before an external user sees any of it.

## 5. Unresolved assumptions a production team will inherit

1. **The funnel counts peak stage, not current stage.** If the business disagrees,
   `sel.peakRank` is the one place to change, but every headline moves.
2. **"12 shipped" is historical, not current.** Four of those twelve are now
   paused, pivoted or shut down. If a funder reads it as "12 live today", the
   label is wrong, not the number.
3. **Survival requires reaching Validated**, so a deployed product that never
   found a user is excluded from the numerator. Reasonable, unratified.
4. **Self-reported values are included in totals** but reported separately. An
   alternative is to exclude them entirely from headlines.
5. **One participant record per person per cohort.** The model supports multiple
   enrolments; no data exercises it, so the UI is untested for it.
6. **`TODAY` is hardcoded** (`2026-09-17`) so the demo is stable. Production
   derives check-in state from the real clock — which will change what is
   overdue on the first run.

## 6. Running and re-verifying

```
python3 -m http.server 8000     # then open http://localhost:8000
```

No build. After changing data, re-run the QA scripts in the scratchpad, or at
minimum confirm in the console:

```js
sel.cohort('bnp01').canonical            // {participants:32, continents:3, productsShipped:12}
sel.funnel({cohort:'bnp01', ...})        // deployed must equal 12
```

If either drifts, canonical facts have been broken and the demo should not be
shown until it is fixed.
