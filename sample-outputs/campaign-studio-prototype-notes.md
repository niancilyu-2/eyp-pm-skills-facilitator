# Prototype notes — Personalization Campaign Studio (handoff payload for pm-plan-and-estimate)
**Produced by a real `pm-prototype` run**, 2026-07-06. Companion to `campaign-studio-prototype.html`.

## Hypothesis
A marketer can go from test idea to a **trusted winner readout** without leaving the CMS — and
without a developer. Validated if test users complete create → preview → promote unaided.

## Chosen direction (and rejected alternatives)
- **Chosen: personalization campaign studio** — 4-screen happy path (campaigns / create /
  per-segment preview / results). Most direct test of the "end-to-end without spreadsheets"
  complaint that dominates the evidence.
- Rejected: segment-lifecycle admin console — real need (compliance calls), but it's governance
  plumbing; represented here as one 🔒 chip + one audit line, deferred to planning.
- Rejected: AI test-ideation backlog first — real asks (travel/fitness accounts), but it
  presupposes the studio exists; sequenced as a later layer.

## Persona / context
Growth marketer / lifecycle PM at an enterprise customer; today runs test backlogs in spreadsheets,
stitches results from analytics exports, and screenshots segment previews for stakeholders.

## Assumptions (carried into planning)
- [Assumption] Segments can be sourced from existing member/session data for the MVP.
- [Assumption] Campaign variants can attach to existing content nodes without a schema migration —
  **needs a spike**.
- [Assumption] Basic analytics events (views/clicks) are obtainable for the results readout —
  integration surface unverified.
- [Assumption] Rules-level lifecycle enforcement ("stopped stays stopped") is a platform behavior,
  not a UI promise — must be enforced server-side.

## Open questions
- Rule precedence when two live campaigns target the same page/segment (surfaced on-screen).
- Statistical rigor of the winner call in v1 — simple threshold vs. real confidence intervals?
- Who may promote/stop — any editor or a permission?

## Edge cases: wired vs. deferred
- **Wired:** Stopped 🔒 state with republish lock note (campaigns list), preview-failure retry
  (preview screen), loading skeleton (results refresh), archived-empty hint.
- **Deferred:** approval chains, full audit console, overlapping-campaign conflict UI — planning
  concerns, deliberately out of prototype scope (workshop guardrail).
