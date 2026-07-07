# Personalization Campaign Studio — Effort & Cost Estimate
**⚠ Needs engineering validation** — an input to that conversation, not a commitment.
Produced by a real `pm-plan-and-estimate` run, revised 2026-07-07 after engineering review,
against Umbraco-CMS @ `ba6ec7ab`, using `workshop/team-capacity.md`.

## 1. Headline
- **Effort:** buffered **P50 ≈ 91 pd / P90 ≈ 107 pd** (correlation-adjusted bound ≈ 111 — see §2).
- **Calendar:** ~**3.3–3.8 sprints** → fits the 8-week freeze **at P50 only**, and only with the
  FE-pairing and design-lead-time mitigations in place; P90 consumes essentially the whole runway.
- **Cost:** budget envelope **$150k–$180k** (top-down burn); task-cost floor $82k–$96k (bottom-up).
  The ~$60–85k gap is the design/QA/coordination the task list doesn't enumerate. **Confidence: Medium.**

## 2. Effort by bucket (PERT — one three-point per bucket, stated per the granularity rule)
| Bucket | O | M | P | P50 | P90 |
|--------|---|---|---|-----|-----|
| Engineering (raw — incl. assignment engine M2) | 36 | 58 | 96 | 61 | 74 |
| Engineering (+20% buffer, scales both percentiles) | | | | 73 | 89 |
| Operational (perf/invalidation/events infra) | 6 | 10 | 18 | 11 | 13 |
| Rollout (flag/QA/docs) | 4 | 7 | 12 | 7 | 9 |
| **Total (buffered)** | | | | **91** | **107** (√Σσ²) / **≈111** (correlated bound) |

Granularity: bucket-level three-points (fewer independence assumptions). The √Σσ² roll-up assumes
buckets fail independently; since they share the team and the freeze, the correlated bound is
reported alongside it.

## 3. Capacity & runway (from team-capacity.md — every constrained role)
4 engineers × 7 effective pd/sprint = 28 pd/sprint, −3.5 pd PTO. P50 91 pd ≈ 3.3 sprints; P90
107 pd ≈ 3.8 sprints against a 4-sprint freeze.
**Critical-path warnings:**
- Studio UI (~24 pd) flows through the **single frontend engineer** ≈ 3.4 sprints solo — pairing
  the full-stack engineer on UI from sprint 1 is required — and that engineer is also on the
  critical backend path, so the mitigation is itself thinly staffed.
- The **50% designer** must deliver specs for screens 1–2 before sprint 1 or the FE is blocked — the
  prototype shows direction but is not a design spec.
- The **50% QA** owns the caching-enabled correctness suite for the feature's core guarantee —
  thin enough to be a P90 driver; the test matrix must exist on paper before M3 starts.

## 4. Dollar cost — bottom-up vs. top-down, reconciled
| Method | Approach | Result | What it prices |
|--------|----------|--------|----------------|
| A — bottom-up | 91–107 pd × $900/day blended [Assumption: loaded rate] | **$82k–$96k** | the tasks enumerated |
| B — top-down | 5.0 FTE (4 eng + 0.5 design + 0.5 QA) × 10 d/sprint × $900 = $45k/sprint × 3.4–4.0 sprints | **$153k–$180k** | the team the window occupies |

**Reconciliation:** B − A ≈ $60k–$85k = the designer and QA the task list doesn't itemize,
coordination with the core team, and <100% utilization. **Budget envelope: $150k–$180k. Task-cost
floor: $82k–$96k.** *(Value context, not a cost check: the envelope is ~8% of the $2.12M at-risk
cluster it addresses.)*

## 5. Two-perspective debate
- **AI-accelerationist:** the seam exists with a tiny reference surface (5 files); the webhook
  slice gives UI parity to copy; low end is real for M1/M4.
- **Skeptical SRE:** three 🔴 unknowns — serving-policy breadth (a correctness promise across every
  resolve path), the unverified analytics surface, and a seam core has already marked obsolete.
  Any one eats the P50 slack.
- **Crux:** M3's design review + M5's spike. **Landing: Medium confidence**; the two spikes and the
  core-team conversation are cheap insurance and all precede sprint 1.

## 6. Why these numbers
Measured the affected subsystems in the actual repo (Core ≈ 48.7k LOC cs; backoffice client ≈
39.6k LOC — commands in SPEC.md), confirmed the seam exists and what does NOT exist (assignment
engine, publish-path segment handling — the verified absences drove milestone 2 into scope),
decomposed into seven demoable milestones with cross-cutting work priced, and estimated at bucket
level with three-point PERT challenged from both perspectives. The widest uncertainty sits exactly
where knowledge is thinnest: serving-policy breadth and the analytics surface.

## 7. Sensitivity — what moves the number most
1. Analytics/event surface (M5) — closed surface adds ~1 sprint (2-hour spike gates it).
2. Serving-policy breadth (M3) — the design review is the go/no-go on the whole range.
3. FE capacity + design lead time (M4) — the pairing/spec mitigations are schedule-critical.

## 8. Reproducibility & analog ledger
- Measured paths: `src/Umbraco.Core/**` (cs), `src/Umbraco.Web.UI.Client/**` (ts/js/html) —
  git-fallback method; generated/vendored excluded; commands + outputs in SPEC.md.
- **Analog:** the webhook vertical slice.
  `git ls-files 'src/Umbraco.Core/Webhooks/**' | grep '\.cs$' | xargs wc -l | tail -1` → `9142 total`
  (inflated by ~200 small event classes — noted, not used as-is);
  `…/Controllers/Webhook/**` → `583 total`; `…/packages/webhook/**` (ts/html) → `2466 total`; 291 files.
- **Multiplier:** studio UI ≈ webhook client package (2,466 LOC of CRUD screens) × 1.5–2 for a
  novel product surface (preview orchestration, results viz, lifecycle states).
- **Anchor scope:** the analog prices **M1 and M4 structural work only**. M2/M3 are
  correctness-driven and M5 is integration-driven — priced by PERT judgment, and re-priced after
  their spikes/design review.
