# Reconciliation — prototype seed data vs. published KayKav claims

Compares the programme facts seeded into `index.html` against what KayKav
already says publicly, so the prototype does not contradict material a partner
may have already read.

- **Date of reconciliation:** 16 September 2026
- **Prototype reconciled:** `index.html` at the root of this repo
- **Scope:** `DB.cohorts` (enrolled, start, end, demoDay, countries, continents,
  sessions), `DB.products` (name, aka, country, cohortId — plus `url`, see note
  below), and the two hardcoded prose spots (funder view "3 continents"; builder
  view "due on 6 September" / "5 November 2026").

Everything under `metrics:` is placeholder outcome data and was **not** touched.
No figure in this document has been copied into a `metrics:` field.

## Sources

| # | Source | How it was read |
|---|---|---|
| S | KayKav Academy — Domain Expert Builders partner site | Source repo `kaykavacademy/KayKavPartners`, `index.html` at commit `96fd765` (2026-09-10). Page footer dates the content "September 2026". |

Two notes on the sources, both of which limit what this document can conclude:

1. **The deployed site could not be fetched.** `partner.kaykav.academy` and the
   Vercel deployment are both blocked by this environment's network egress proxy
   (`CONNECT tunnel failed, response 403`; general web egress is closed). The
   claims below were therefore read from the site's **source repository** on
   branch `main`, which is what the `…-git-main-…` deployment builds from. If
   `main` and the live site have drifted, this document reflects `main`.
2. **The decks and one-pagers were not supplied**, so they were not read. The
   partner site's own README states *"All copy is drawn from the Partner Pitch
   deck and the Partnership Proposal"* — the site is downstream of those
   documents, not independent of them. This has a direct consequence for
   category (b) below.

## Claim-by-claim comparison

"Silent" means the source makes no claim on that point; per the brief the
prototype was left alone and the silence recorded.

| # | Claim | Source value | Where in source | Prototype value | Agree? |
|---|---|---|---|---|---|
| 1 | Students across the cohorts | 150 | §04 Proof, `data-count="150"` | 40 (32 + 8) | **No** — (a) |
| 2 | Countries | 7 | §04 Proof, `data-count="7"` | 6 (Nigeria, US, UK, Canada, Sweden, Ireland) | **No** — (a) |
| 3 | Continents | 3 | §04 Proof, "Countries, 3 continents" | 3 (`bnp01.continents`) | Yes |
| 4 | Continents (funder prose) | 3 | as above | "3 continents", `index.html:1305` | Yes |
| 5 | Live products across the cohorts | 70+ | §04 Proof, `data-count="70"` | 17 deployed of 18 records | **No** — (c) |
| 6 | Students learning to solve problems with AI | 1000+ | §04 Proof, `data-count="1000"` | no equivalent field | **No** — (c) |
| 7 | Number of BNP cohorts run | Two ("demonstrated, twice"; "Two cohorts have taught us") | In summary; §11 | 2 (`bnp01`, `bnp02`) | Yes |
| 8 | BNP programme length | Four weeks ("deploy real software solutions in four weeks") | In summary | "Four-week cohort" (`programmes[bnp].desc`) | Yes |
| 9 | DEB programme length | Six weeks ("6-week live cohorts"; Week 0 – Week 6) | Hero; §06 | not modelled (`deb01` unrun, `sessions:0`) | Silent-equivalent |
| 10 | DEB status | Not yet run (pitched as a proposal; "show you a third time") | throughout | `deb01` "Not yet run"; funder prose "Domain Expert Builders has not yet run" | Yes |
| 11 | Programme name | "Built, Not Prompted" | §04 | `Built, Not Prompted` | Yes |
| 12 | Programme name | "Domain Expert Builders" | §05 | `Domain Expert Builders` | Yes |
| 13 | Cohort start / end / demo-day dates | **Silent** — no cohort dates anywhere | — | bnp01 2026-04-30→05-30; bnp02 2026-07-01→08-07 | Left as is |
| 14 | Session counts | **Silent** — "every session" but no count | §08 | 9 per BNP cohort | Left as is |
| 15 | Cohort names | **Silent** | — | BNP Cohort 01/02, DEB Cohort 01 | Left as is |
| 16 | Builder-view dates ("due on 6 September", "5 November 2026") | **Silent** | — | `index.html:1429`, `:1432` | Left as is |
| 17 | Product — Precision | `tryprecisioncare.com`, "Care coordination product", "health workflow tool" | §04 | `precision-ops.com`, Professional services, "Scoping and estimate builder for a small consulting practice" | **No** — (a), held |
| 18 | Product — DraftDesk | `draftdesk.online`, "Drafting and document workspace" | §04 | `draftdesk.legal`, Legal | **No** — (a), fixed |
| 19 | Product — Wandar | `wandar.co`, "Travel itinerary planning" | §04 | `wandar.travel`, Travel | **No** — (a), fixed |
| 20 | Product — DDT | "DDT" / "Don't Destroy the Structure", non-destructive testing | §04 | name `DDT Structure`, `aka` empty | **No** — (a), fixed |
| 21 | Product — Oneflute | `theoneflute.vercel.app`, "AI-assisted PR and creator marketing platform" | §04 | absent from `DB.products` | **No** — (a), held |
| 22 | DDT commercial outcome | "his employer subscribed for a year" | §04 | `p05.metrics.paying` and `.revenue` both `gap()` (unknown) | **No** — (c) |
| 23 | Precision delivery claim | "shipped by a non-engineer in four weeks" | §04 | `p04` level 2, `users: gap('No check-in returned')` | **No** — (c) |
| 24 | All named products live | "every one is on a real URL today" | §04 | 4 of 5 present and deployed; Oneflute absent | **No** — (c) |
| 25 | Other prototype products (Nestly, BitePlan, Ideas Journal, Herd, Shelfy, ClinicQueue, Storyseed, SiteLog, PayRoute) | **Silent** — site lists "Selected products", not an exhaustive set | §04 | 9 named records | Left as is |

## (a) Prototype is wrong — source is authoritative

Four of these were applied. Three are **held**, because closing them would mean
inventing a value the source does not supply, which the brief forbids.

| # | Item | Change | Applied? |
|---|---|---|---|
| 19 | Wandar URL | `wandar.travel` → `wandar.co` | **Applied** |
| 18 | DraftDesk URL | `draftdesk.legal` → `draftdesk.online` | **Applied** |
| 20 | DDT name | `name: 'DDT Structure'` → `name: 'DDT'`, `aka: 'Don't Destroy the Structure'` | **Applied** |
| 1 | Enrolment 150 vs 40 | — | **Held** |
| 2 | 7 countries vs 6 | — | **Held** |
| 17 | Precision identity | — | **Held** |
| 21 | Oneflute missing | — | **Held** |

Why the four are held:

- **Enrolment (150 vs 40).** The source gives only a cross-cohort total, with no
  per-cohort split, so there is no non-invented way to update `bnp01.enrolled`
  and `bnp02.enrolled`. The scope also differs: the prototype's 32 + 8 is
  described in the seed data as programme records with "20 participant records
  still to be backfilled", whereas "150 students across the cohorts" may count a
  wider population than the two BNP cohorts the prototype models — the same
  section separately cites "1000+ students learning to solve problems with AI",
  so the site clearly counts more than one population. **Someone has to decide
  what 150 covers, and what the per-cohort split is.**
- **Countries (7 vs 6).** The source states the count but never lists the
  countries, so the seventh cannot be identified. The prototype's list is
  explicit and drives the aggregate. **Someone has to name the missing country.**
- **Precision.** The name matches but nothing else does: the source has a care
  coordination / health product at `tryprecisioncare.com`; the prototype has a
  consulting scoping tool at `precision-ops.com`. Correcting only the URL would
  produce an incoherent record (a health URL on a consulting description), and
  correcting the description and domain as well goes beyond the fields in scope
  — and would be wrong outright if these are two different products that share a
  name. **Someone has to confirm whether these are the same product.**
- **Oneflute.** The source names it as live on a real URL; the prototype has no
  such record. Adding one would require inventing a `builderId`, `cohortId`,
  `level` and history, none of which the source provides.

Note on scope: `url` is not among the `DB.products` fields listed in the brief
(name, aka, country, cohortId). The two URL corrections were applied anyway
because the source states product URLs explicitly and a wrong URL is a plain
factual error — but they are called out here so they can be reverted if that
overstepped.

## (b) Sources disagree with each other

**Empty — but not because everything agrees.** Only one source was supplied, and
two documents are needed for this category to have any content.

This is worth reading as a gap rather than a clean bill of health. The decks and
one-pagers named in the original brief were never provided, and the partner
site's README says its copy is drawn *from* the Partner Pitch deck and the
Partnership Proposal — so the site is a rendering of those documents, not an
independent check on them. A genuine deck-vs-site comparison has not been done.

## (c) Source claims an outcome the prototype records as unknown

None of these were applied. They are outcome claims, and every one of them lands
on a `metrics:` field the brief puts out of bounds. They are listed so the
difference is visible, not so it can be closed.

| # | Source claim | Prototype record |
|---|---|---|
| 5 | "70+ live products across the cohorts" | 17 of 18 product records reached a live URL |
| 6 | "1000+ students learning to solve problems with AI" | no equivalent metric exists |
| 22 | DDT: "his employer subscribed for a year" | `p05.metrics.paying` and `.revenue` are both `gap()` — unknown, unverified |
| 23 | Precision: "shipped by a non-engineer in four weeks" | `p04` is level 2 with `users: gap('No check-in returned')` |
| 24 | "every one is on a real URL today" (5 named products) | 4 present and deployed; Oneflute has no record at all |

Item 22 is the sharpest: the site states a paying commercial outcome for DDT,
while the prototype's verification queue treats DDT's revenue as never
collected. Both cannot be right. Resolving it means producing the evidence and
running it through the queue, not editing the seed value.

## Incidental observation (not source-driven)

`bnp02` runs 2026-07-01 → 2026-08-07, which is five weeks and two days, while
the programme is described in the same file as a "Four-week cohort". The source
is silent on cohort dates, so nothing was changed. Flagging it only because it is
an internal inconsistency in the seed data that a reader may notice.
