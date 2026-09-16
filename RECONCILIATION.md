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

Everything under `metrics:` is placeholder outcome data. The first pass changed
nothing there. The second pass recorded exactly one outcome — DDT's paying
customer — after the owner confirmed they had verified it, and left the amount
as a gap because no source states one. Nothing else under `metrics:` has been
altered, and no figure has been lifted from the site into a `metrics:` field
on the strength of the site alone.

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
| 1 | Students across the cohorts | 150 | §04 Proof, `data-count="150"` | 150 (142 + 8) — **corrected 16 Sep**, was 40 | Yes |
| 2 | Countries | 7 | §04 Proof, `data-count="7"` | 6 (Nigeria, US, UK, Canada, Sweden, Ireland) | **No** — (a), still held |
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
| 17 | Product — Precision | `tryprecisioncare.com`, "Care coordination product", "health workflow tool" | §04 | `tryprecisioncare.com`, Healthcare, "A health workflow tool for care coordination." — **corrected 16 Sep** | Yes |
| 18 | Product — DraftDesk | `draftdesk.online`, "Drafting and document workspace" | §04 | `draftdesk.legal`, Legal | **No** — (a), fixed |
| 19 | Product — Wandar | `wandar.co`, "Travel itinerary planning" | §04 | `wandar.travel`, Travel | **No** — (a), fixed |
| 20 | Product — DDT | "DDT" / "Don't Destroy the Structure", non-destructive testing | §04 | name `DDT Structure`, `aka` empty | **No** — (a), fixed |
| 21 | Product — Oneflute | `theoneflute.vercel.app`, "AI-assisted PR and creator marketing platform" | §04 | absent from `DB.products` | **No** — (a), held |
| 22 | DDT commercial outcome | "his employer subscribed for a year" | §04 | `paying: 1` at confidence 2 (KayKav verified), level raised to 6 Revenue — **recorded 16 Sep**; `revenue` amount still a gap | Partly |
| 23 | Precision delivery claim | "shipped by a non-engineer in four weeks" | §04 | `p04` level 2, `users: gap('No check-in returned')` | **No** — (c) |
| 24 | All named products live | "every one is on a real URL today" | §04 | 4 of 5 present and deployed; Oneflute absent | **No** — (c) |
| 25 | Other prototype products (Nestly, BitePlan, Ideas Journal, Herd, Shelfy, ClinicQueue, Storyseed, SiteLog, PayRoute) | **Silent** — site lists "Selected products", not an exhaustive set | §04 | 9 named records | Left as is |

## (a) Prototype is wrong — source is authoritative

Five of these have been applied. Two remain **held**, because closing them would
mean inventing a value no source supplies.

| # | Item | Change | Applied? |
|---|---|---|---|
| 19 | Wandar URL | `wandar.travel` → `wandar.co` | **Applied** |
| 18 | DraftDesk URL | `draftdesk.legal` → `draftdesk.online` | **Applied** |
| 20 | DDT name | `name: 'DDT Structure'` → `name: 'DDT'`, `aka: 'Don't Destroy the Structure'` | **Applied** |
| 1 | Enrolment 150 vs 40 | `bnp01.enrolled` 32 → 142, giving 142 + 8 = 150 | **Applied**, on an assumption — see below |
| 17 | Precision identity | url → `tryprecisioncare.com`, domain → Healthcare, desc → "A health workflow tool for care coordination." | **Applied** |
| 2 | 7 countries vs 6 | — | **Held** |
| 21 | Oneflute missing | — | **Held** |

**Enrolment — the assumption behind the split.** The owner confirmed the total
should read 150 to match the site. The site gives only a cross-cohort total and
no per-cohort split, so one had to be chosen. `bnp02` was left at 8: it has eight
named builder records, all marked recorded, and is described in the seed data as
"the first cohort capturing impact data from day one". The entire remainder was
therefore assigned to `bnp01`, which is the cohort carrying the backfill gap:

- `bnp01.enrolled` 32 → **142**, `recorded` unchanged at 12
- `bnp01.note` updated from "20 participant records still to be backfilled" to
  **130**, which is what 142 − 12 now implies

The consequence is deliberate and visible: the prototype now shows 150 trained
against 20 builder records, so "What we don't know" reports 130 missing
participant records. That is the tool behaving as intended — the gap is stated
rather than smoothed — but it is a much larger gap than before, and if 150
actually spans more than the two BNP cohorts the prototype models, the split
above is the thing to revisit first.

Why the two remaining items are held:

- **Countries (7 vs 6).** The owner identified the missing country as Sweden,
  but Sweden is already in `bnp01.countries` and already counted in the six
  (it is the country on the Ideas Journal record). Adding it again changes
  nothing, and naming a different country would be invention. The source states
  the number 7 but never lists the countries, so the seventh is still
  unidentified. **This one still needs a name.**
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

One of these (item 22) has since been verified by the owner and recorded. The
rest remain open: they are outcome claims, and closing them means producing
evidence, not editing a seed value.

| # | Source claim | Prototype record | Status |
|---|---|---|---|
| 5 | "70+ live products across the cohorts" | 17 of 18 product records reached a live URL | Open |
| 6 | "1000+ students learning to solve problems with AI" | no equivalent metric exists | Open |
| 22 | DDT: "his employer subscribed for a year" | `paying: 1` at confidence 2 (KayKav verified); product raised to level 6, Revenue | **Resolved** |
| 23 | Precision: "shipped by a non-engineer in four weeks" | `p04` is level 2 with `users: gap('No check-in returned')` | Open |
| 24 | "every one is on a real URL today" (5 named products) | 4 present and deployed; Oneflute has no record at all | Open |

**How item 22 was closed.** The owner confirmed they had verified the DDT
subscription, so it was entered at confidence 2, "KayKav verified — a KayKav
reviewer has checked the evidence and accepted it", and the product moved from
level 5 to level 6 (Revenue: "at least one real customer has paid"). The funnel's
revenue-generating count therefore moves from 1 to 2.

What was deliberately **not** recorded is the amount. The source says only that
the employer "subscribed for a year" and gives no figure, so `revenue` and `mrr`
remain gaps, now carrying the reason "Subscription confirmed, amount not
disclosed" rather than the default "Not collected yet". A revenue number would
have had to be invented, and the one rule that has held throughout this
reconciliation is that a gap is preferable to a fabricated figure.

## Revision log

**Pass 1 — 16 September 2026.** Compared all 25 claims. Applied three product
corrections (Wandar URL, DraftDesk URL, DDT name and aka). Held four items that
could not be settled without inventing a value.

**Pass 2 — 16 September 2026.** The owner resolved three of the four held items:

| Item | Owner's instruction | Outcome |
|---|---|---|
| Enrolment | Set the total to 150 to match the site | Applied as 142 + 8; the split is an assumption, documented under (a) |
| Precision | Correct it to what the source shows | Applied — URL, domain and description |
| DDT | "I have verified it" | Applied — `paying: 1` at confidence 2, level raised to 6; amount left as a gap |
| Countries | The missing country is Sweden | **Not applied** — Sweden is already in the list and already counted; the seventh country is still unidentified |

One item, Oneflute, was not raised and remains held.

## Incidental observation (not source-driven)

`bnp02` runs 2026-07-01 → 2026-08-07, which is five weeks and two days, while
the programme is described in the same file as a "Four-week cohort". The source
is silent on cohort dates, so nothing was changed. Flagging it only because it is
an internal inconsistency in the seed data that a reader may notice.
