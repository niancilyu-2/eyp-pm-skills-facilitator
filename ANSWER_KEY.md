# Answer key

Single source for the seeded scenarios and expected numbers. The facilitator guide points here
rather than repeating them.

## Primary dataset: `workshop/mock-data/` (Umbraco)

**The seeded gap.** Umbraco has personalization basics (core segments plus an add-on for
analytics/AB fundamentals), but marketers can't run an experiment end-to-end they trust: no
campaign workspace, stopped variants can reappear, segment previews aren't trusted. The roadmap
deck covers adjacent work and omits this (slide 7). WF1 should surface it, WF2 prototypes the
campaign studio, WF3 plans it against the real codebase.

**Expected numbers per evidence leg:**
| File | Expected |
|------|----------|
| Customer Call Notes | 16 calls, mixed asks and counter-signals |
| Churn and Expansion Workbook | 41 rows incl. planted duplicates (HarborOne UK, BrightCart EU); cluster = 27 rows / $2.18M at-risk raw ($2.47M all-reason); deduped = 25 accounts / $2.12M |
| Support Ticket Log | 41 tickets; ~29–31 on the cluster (definition-dependent); 16 escalations overall, 14 strict-cluster |
| G2/Capterra Review Export | 49 entries; 33 on the cluster themes |
| Public Issue Tracker Export | 20 issues; votes concentrate on segment/experiment items |
| Community Forum Digest | 7 threads; replies concentrate on the cluster |

**What a good run has to do:** cluster four sub-themes (experiment workflow, segment lifecycle,
preview trust, campaign-page perf) into one theme; dedupe the planted duplicates before summing
and say so; cite the roadmap omission; set aside the decoys — the $118k security-owned account
(different buying center), AI-content asks (already on the roadmap), headless ops, approvals.

## Second dataset: `workshop/sample-data/` (Tideflow)

Fictional collaboration/docs app. Seeded gap: native time-tracking + capacity planning
(competitors Cadence and Orbit are stronger); the roadmap omits it.
- Expected: the two churn.csv reason labels ("No time tracking", "No capacity planning") cluster
  into one theme, ~$770k ARR across 4 accounts, surfaced as the validated gap.
- Traps: "search" has 2 accounts behind it but is already on the roadmap; the counter-signal
  interviews (Saffron wants search; Orchard is small, wants mobile) shouldn't move the ranking.

## WF3 reference figures

From `sample-outputs/ESTIMATE.md` and `SPEC.md` (full evidence trail in the files, pinned to
Umbraco-CMS `ba6ec7ab`): P50 ≈ 91 pd / P90 ≈ 107 pd; budget envelope $150k–$180k, task-cost
floor $82k–$96k.
