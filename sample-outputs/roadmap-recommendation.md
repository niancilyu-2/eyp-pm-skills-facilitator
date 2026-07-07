# Roadmap Recommendation — Personalization Campaign Studio
**Produced by a real `pm-ideation` run** on `workshop/mock-data/` (all 10 inputs ingested and
computed, 2026-07-06). Kept as a pre-staged workshop fallback and a reference for what "good"
output looks like. The underlying data is synthetic (see `../mock-data/README.md`).

---

## Exec summary
The dominant, multi-source pattern is **not** "add personalization" — basics exist (core segments +
an add-on). It is: **marketers cannot run a trustworthy personalization experiment end-to-end.**
Four sub-themes cluster into one gap — no campaign workspace (idea → variant → preview → results →
winner), leaky segment lifecycle (stopped variants reappear), untrusted segment previews, and
campaign-page editor performance. Cluster: **27 account-rows / $2.18M ARR at risk raw; $2.12M
across 25 accounts after deduping the CRM child-accounts** — ~7× every other reason combined
($293k). Recommend building the **Personalization Campaign Studio** for **Now**. Confidence:
**High (85%)** — six independent evidence legs; main caveat is that reason codes are single-coded.

## Problem statement
**One-line:** Marketing teams on Umbraco run personalization tests through spreadsheets,
screenshots, and developer tickets — so they can't trust or prove results, and take their
experimentation budgets to Optimizely.

**I am** a growth/lifecycle marketer at an enterprise Umbraco customer `[Call CALL-001, CALL-004]`
· **trying to** run segment-targeted content tests and prove which version won `[CALL-001: "know
what to test, see each segment, and prove the winner"]` · **but** planning lives in spreadsheets,
previews need screenshots, results need manual analytics joins, and stopped variants can resurface
`[CALL-002; SUP-30177/30161]` · **because** there is no integrated campaign workspace and segment
lifecycle isn't enforced — [Inference] from the workaround pattern across tickets and reviews ·
**which makes me feel** unable to defend results to my CMO `[CALL-004]` — and for us: **$873k
already churned** (5 cluster accounts + 1 other) `[workbook]`.

**Success criteria**
| Metric | Baseline | Target | Timeline |
|--------|----------|--------|----------|
| Cluster ARR at risk | $2.12M deduped `[workbook]` | < $700k | 2 quarters |
| Escalated cluster tickets | 14 open `[ticket log]` | ≤ 4 | 2 quarters |
| Guardrail: editor performance on campaign pages | current p95 [Assumption: measure pre-build] | no regression | ongoing |

## Evidence & signal strength
| Theme (clustered) | Independent legs | Strength |
|---|---|---|
| **No trustworthy end-to-end experiment flow** (workspace + lifecycle + preview + perf) | calls (11 of 16) · churn workbook 27 rows/$2.18M · tickets 29/41 strict-cluster (31 if campaign-perf areas count); 16 escalations overall, 14 inside the strict cluster · reviews 33/49 · public issues (top 8 by votes) · forum (top 5 threads) | **VALIDATED — 6 legs** |
| AI content drafting | 5 reviews · 2 calls · $0 at-risk | WEAK — and **already planned** `[deck, Q1 2027 AI Author Assistance]` |
| Headless/API ops | 5 reviews · 2 accounts/$100k | WEAK |
| Security/deploy evidence | 1 account ($118k ARR, $24k modeled) | WEAK — different buying center `[workbook: CISO-owned]` |

**Dedup note:** HarborOne UK and BrightCart EU are CRM child/duplicate rows ($58k combined) —
excluded from the deduped figure; flagged rather than silently summed. **Who's NOT asking:** 8
renewed/stable accounts (media, legal, museums, parks…) — the gap is concentrated in
commerce/regulated enterprise segments.

## Competitive & roadmap gap
Optimizely's public positioning is the integrated loop — experimentation, personalization,
measurement in one platform `[competitor inventory]`; it is cited by **19 of 41** tracked accounts
`[workbook]`. Umbraco's own inventory confirms basics exist (segments, add-on analytics/AB
fundamentals) but no campaign workspace `[feature inventory]`. The roadmap covers **adjacent** work
(editor UX, cloud/API, approvals discovery, AI drafting, perf discovery) and states the omission:
no committed or candidate item covers marketer-facing campaign experimentation or segment lifecycle
tooling `[deck, slide 7]`.

## Recommendation (transparent RICE)
**Build the Personalization Campaign Studio → NOW.** The direct customer value: marketers get a
trustworthy idea → variant → winner loop without spreadsheets, screenshots, or developer tickets —
reclaiming the 10–12 QA hours per campaign `[SUP-30399]` and giving regulated teams provable
lifecycle control `[CALL-002]`. That unmet value shows up commercially as the $2.12M at-risk
cluster. MVP = campaign workspace (create → per-segment preview → results → promote/stop) **plus**
lifecycle enforcement (stopped-stays-stopped) — the trust half is why regulated accounts churned.
| Factor | Sourced input | Value | Rationale |
|--------|---------------|-------|-----------|
| Reach | 25 deduped accounts now; pattern spans 6 verticals `[workbook]` | high | plus expansion-blocked logos |
| Impact | customer: trusted test loop, 10–12 QA hrs/campaign reclaimed `[SUP-30399]`, provable compliance `[CALL-002]` · business: $2.12M at risk + $873k lost `[workbook]` | 3 | customer value leads; ~7× next theme commercially |
| Confidence | 6 independent legs | 85% (High) | reason codes single-coded — see red-team |
| Effort | TBD — `pm-plan-and-estimate` | ~3 (placeholder) | real segment seams exist in core |

**Displaces:** nothing committed — slide 7 confirms white space; AI drafting (Q1 2027) stays, and
later layers the studio's AI test-ideation asks onto it.

## Red-team / counter-case
- **Strongest objection:** "This is two problems — tech debt (segment trust bugs) and a new product
  (campaign studio) — don't bundle them." **Response:** partially accepted. They fail together in
  every churn story (nobody adopts a studio whose previews lie), so sequence them in one program:
  trust fixes are milestone 1, studio on top. The recommendation is the program, not one feature.
- **Disconfirming evidence found:** 8 renewed accounts never raised it; Finch ($118k) renewed on
  security grounds — big ARR ≠ this gap; AI-content asks are loud but carry $0 at-risk.
- **Bias checks:** churn reason codes are CS single-coded [Assumption]; review-site evidence is
  self-selected (unhappy users review more) and some entries lack the verified flag — treated as
  directional, weighted below churn $.

## Assumptions & open questions
- [Assumption] Reason codes reflect primary cause. [Assumption] Add-on analytics can feed a results
  readout. [Open] Would trust-fixes alone retain the at-risk regulated accounts, or is the studio
  the retention driver? [Open] Guardrail p95 baseline.

## What to validate next
1. Walk HarborOne (compliance blocker) and Northstar-like retail accounts through the studio +
   lifecycle-enforcement scope — does it reverse the evaluation?
2. Sales to confirm reason codes on the 5 cluster-churned logos against exit notes.
3. Confirm the add-on's event/analytics integration surface before WF3 commits a results milestone.

## Evidence appendix
- Workbook `Renewal Tracker` (41 rows): cluster 27 rows/$2,181,000 at-risk raw → 25/$2,123,000
  deduped; all-reason at-risk $2,474,000; churned ARR $873,000. Duplicates: HarborOne UK ($25k),
  BrightCart EU ($33k).
- Ticket log: 41 tickets; cluster 29 by strict area-tags (31 incl. campaign-perf areas); 16
  escalations overall (14 in the strict cluster — definition matters, so it is stated); churned accounts' tickets closed *unresolved* (SUP-30112, 30253, 30361).
- Review export: 49 entries; cluster themes 33 (workflow 9, preview 8, lifecycle 8, perf 8); lowest
  average ratings sit on preview/lifecycle themes.
- Public issues: top votes ISS-1042 (214, blank segment values), ISS-1088 (198, variant reappears),
  ISS-1121 (175, segment preview links), ISS-1155 (169, Block Grid perf).
- Forum digest: top threads mirror the same four sub-themes (41/37/33/28/24 replies).
- Calls: CALL-001/002/003/004/005/006 + 5 more raise cluster asks; CALL-015 (headless) and the
  security/AI-content calls are the counter-signals.
- Deck: slides 2–6 adjacent work; slide 7 the omission.
