# Answer key — seeded scenarios and expected numbers

## Primary dataset: `workshop/mock-data/` (Umbraco scenario)

**The seeded gap.** Mock company Umbraco (open-source .NET CMS). Personalization *basics* exist
(core segments + an add-on for analytics/AB fundamentals) — but marketers cannot run a trustworthy
experiment end-to-end: no integrated campaign workspace (idea → variant → per-segment preview →
results → winner), segment lifecycle is leaky (stopped variants reappear), and segment previews
aren't trusted. The roadmap deck covers adjacent work but omits this — the gap `pm-ideation`
should surface. WF2 then prototypes the personalization campaign studio; WF3 plans it against the
real OSS codebase (which contains genuine segment seams).

**Expected numbers per evidence leg:**
| File | Expected |
|------|----------|
| Customer Call Notes | 16 calls; mixed asks + counter-signals |
| Churn and Expansion Workbook | 41 rows incl. duplicates/child rows; cluster = 27 accts / $2.18M at-risk raw ($2.47M all-reason); deduped = 25 accts / $2.12M |
| Support Ticket Log | 41 tickets; ~29–31 on the cluster (definition-dependent); 16 escalations overall (14 strict-cluster) |
| G2/Capterra Review Export | 49 entries; 33 on the cluster themes |
| Public Issue Tracker Export | 20 issues; votes concentrate on segment/experiment items |
| Community Forum Digest | 7 threads; replies concentrate on the cluster |

**Designed-in tests:** the gap must be *clustered* from sub-themes (experiment workflow + segment
lifecycle + preview trust + campaign-page perf); duplicate accounts must be deduped before
summing; decoys exist (security-owned high-ARR account, AI-content asks, headless ops, approvals)
and one "wanted-but-already-planned" trap (AI drafting is on the roadmap). Six independent
evidence legs back the seeded answer.

## Second dataset: `workshop/sample-data/` (Tideflow scenario)

Fictional collaboration/docs app. The seeded gap is **native time-tracking + capacity planning**
(competitors Cadence and Orbit are stronger); the roadmap omits it.

- Expected: time-tracking/capacity surfaced as the VALIDATED gap — ~$770k ARR across 4 accounts
  (churn.csv reasons "No time tracking" + "No capacity planning" cluster into one theme).
- Traps: "search" is asked for by 2 accounts **but is already on the roadmap**; counter-signal
  interviews (Saffron wants search; Orchard is small, wants mobile) shouldn't move the ranking.

## Where the WF3 numbers come from
`sample-outputs/ESTIMATE.md` and `SPEC.md` carry the full evidence trail (commands + outputs,
analog ledger, burn-rate reconciliation) — the reference figures are P50 ≈ 91 pd / P90 ≈ 107 pd,
budget envelope $150k–$180k vs. task floor $82k–$96k, at Umbraco-CMS `ba6ec7ab`.
