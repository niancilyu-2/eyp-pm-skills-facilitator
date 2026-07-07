# Personalization Campaign Studio — Engineering Planning Doc
**Produced by a real `pm-plan-and-estimate` run** against `workshop/codebase/Umbraco-CMS`
@ `ba6ec7ab`, revised 2026-07-07 after engineering review. **Status: needs engineering validation.**

## 1. Exec summary
One program, three layers: **assignment and trust first** (a real segment engine, hardened ingress,
default-deny serving), then the **campaign studio** (create → preview → results → promote/stop) as
a workspace on the core's dormant segment seam. Buffered estimate **P50 ≈ 91 pd / P90 ≈ 107 pd**
(~3.4–4.0 sprints): fits the 8-week freeze at P50 with mitigations; P90 consumes essentially the
whole runway. Budget envelope (top-down burn) **$240k–$280k**; task-cost floor $127k–$150k. Biggest
risks: serving-policy breadth, the unverified analytics surface, and a seam that core has
already marked obsolete.

## 2. Problem & scope
- **Problem/hypothesis:** from WF1/WF2 — marketers can't run a trustworthy experiment end-to-end
  (`roadmap-recommendation.md`, `campaign-studio-prototype-notes.md`).
- **In scope (MVP):** segment provider **and per-request assignment engine**; hardened segment
  headers; campaign/variant model (+ migration & upgrade registration); default-deny lifecycle;
  per-segment preview links; studio UI as a **workspace under an existing section**; results shell
  from captured events; feature flag.
- **Out of scope:** ML/AI ideation; full audit console; approval chains; stats engine; headless
  exposure; a new top-level section (deferred with rationale — cuts section-access migration and
  most localization scope).
- **Done when:** a marketer completes create → preview → promote unaided; a stopped variant cannot
  be served via any publish or request path; editor p95 unchanged.

## 3. Approach: chosen + alternatives
**Chosen (A): activate the dormant core seam + build the studio on it** (evidence in SPEC.md —
the seam exists, is off by default, and only 5 files reference it; note its deprecation status,
also in SPEC.md, makes core-team coordination a formal dependency).

| Approach | Effort | Risk | Blast radius | Why not |
|----------|--------|------|--------------|---------|
| **A (chosen)** | M–L | 🟡 | content pipeline + backoffice | — |
| B: bolt onto add-on analytics only | S–M | 🔴 | add-on boundary | doesn't fix trust/lifecycle — the churn driver; closed surface |
| C: render-middleware variant swap | M | 🔴 | every render path | fragile against restructuring; ignores the existing seam |

## 4. Milestones
| # | Bucket | Milestone | Grounding | Demo | Acceptance | Risk |
|---|--------|-----------|-----------|------|-----------|------|
| 1 | E | Segment provider + campaign/variant model (+ migration & upgrade registration) | `ISegmentService` seam (SPEC evidence) | segments resolve in a test; variant attaches to a node | provider replaces no-op; migration registered | 🟡 |
| 2 | E | **Per-request segment assignment + ingress hardening** | verified absence: client-supplied headers are the only inbound signal (SPEC) | member/session resolves to a segment server-side; forged header rejected | no segment served from unauthenticated client claims | 🔴 |
| 3 | E | Lifecycle as **default-deny serving policy** + per-segment preview | verified absence: publish pipeline has zero segment handling (SPEC) | stopped variant survives republish AND header-replay attempts; shareable preview link | "served only while live" proven by caching-enabled tests | 🔴 |
| 4 | E | Studio UI — 4 screens as a workspace (prototype parity) | webhook client package as structural analog (estimate ledger) | create → preview → results click-through | marketer completes flow unaided | 🟡 (single-FE bottleneck) |
| 5 | E/O | Results readout — shell first; event ingestion separately gated on its spike | analytics surface unverified (RAD) | readout renders captured events | numbers reproducible from raw events | 🔴 |
| 6 | O | Perf: cached evaluation + invalidation (incl. Delivery output caching) | render pipeline | p95 flat under block-heavy load | no editor/serve p95 regression | 🟡 |
| 7 | R | Flag, docs, dark launch | flags/QA | ship dark → ramp | flag toggles clean | 🟢 |

## 4b. Cross-cutting work
| Concern | Covered by | Notes |
|---------|-----------|-------|
| Permissions / auth policies | M4 | new policy + workspace access wiring; ~2–3 pd inside M4 |
| Localization | M4 | lang keys for 4 screens; **culture × segment matrix flagged to design review** |
| Cache / CDN invalidation | M3, M6 | includes Delivery API output-cache purge on state change |
| Test strategy | M3, M6, M7 | caching-enabled integration tests (harness false-green caveat), acceptance/E2E for the 4 screens |
| API surface + doc regen | M1, M5 | management-API conventions; OpenAPI regeneration |
| Migrations + upgrade registration | M1 | campaign table; registered in the upgrade plan |
| Deprecated surfaces | M1, M4 | obsolete segment API — written core-team outcome gates M4's picker design |

## 5. RAD
- **Risks:** 🔴 serving-policy breadth (M3 is a correctness guarantee, not a feature); 🔴 analytics
  surface unverified (M5 gated); 🔴 header ingress until M2 lands; 🟡 single FE owns M4 with a 50%
  designer feeding it — pairing + pre-staged specs required.
- **Assumptions:** variant-on-node without migration (spike 1h); events obtainable (spike 2h).
- **Dependencies:** core team (obsolete-seam replacement direction — written outcome, blocking for
  M4 design); Delivery API team (post-MVP).

## 6. PM ↔ Eng translation
| Item | PM outcome | Eng implementation |
|------|-----------|--------------------|
| Assignment engine (M2) | campaigns target real people, not header tricks | member/session → segment resolution on both serve paths + ingress hardening |
| Trust fixes (M3) | compliance can approve production use | default-deny serving gate + publish-path guards + invalidation |
| Studio (M4) | marketers self-serve tests, zero spreadsheets | workspace package: campaign CRUD + preview + results |
| Results (M5) | "prove the winner" in one screen | event capture + aggregation + readout API (shell first) |
| Seam activation (M1) | none visible — enabling layer | real ISegmentService provider + campaign/variant model + migration |

## 7. Open questions
- **The M3 gate decision rule:** if the trust fixes alone (M1–M3) retain the at-risk regulated
  accounts, M4–M5 get re-scoped before build. Owner: PM + eng lead, at the M3 design review.
- Preview-link token scoping (M3) — a shareable per-segment link must not become the header
  bypass M2 closes (see SPEC RAD).
- Rule precedence for overlapping campaigns (blocks M1 design).
- Winner-call rigor in v1 (product call; doesn't block).
- v2: does the studio graduate to its own section? (revisit after adoption data).
