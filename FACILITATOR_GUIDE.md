# Facilitator Guide — PM AI Workshop

This guide runs the three skills end-to-end on the mock **Umbraco** scenario: the exact prompts,
expected outputs, and talking points.

> Workshop thesis: the demos should land on accuracy — output a PM can trace and defend — with
> speed as the side effect.

## The scenario (one connected thread)
Umbraco has personalization *basics* (core segments + an add-on for analytics/AB fundamentals) —
but marketers **can't run a trustworthy experiment end-to-end**: no campaign workspace, stopped
variants can reappear, segment previews aren't trusted. The evidence converges on building a
**personalization campaign studio**. The thread: **ideation** surfaces the gap from six messy
evidence legs → **prototype** makes the studio clickable → **plan-and-estimate** grounds it in the
real OSS codebase (which contains dormant segment seams).

**Participant framing (one sentence):** *"Customers can't run personalization campaigns they trust
in Umbraco — find the gap, prototype the fix, cost it."*

The skills don't know any of this: they're general-purpose; the scenario comes entirely from the
synthetic data (every file is `[MOCK DATA]`-prefixed and labeled fictional).

---

## Setup (do before the session)

Send participants [`workshop/PRE_READ.md`](https://github.com/niancilyu-2/eyp-pm-skills/blob/main/workshop/PRE_READ.md) about a week ahead — it covers what Umbraco is,
links to a hosted trial they can click around, and (for the CTO session) the codebase background.

> **Two rules that prevent live failures:** (1) always run the agent from the **repo root** — never
> from inside `workshop/codebase/Umbraco-CMS` (that clone ships its own agent config/MCP servers).
> (2) **Rehearse the full chain once** on this exact machine before the session — including the
> fabrication test — and keep the outputs. Real pre-staged fallbacks for every stage live in
> [`sample-outputs/`](sample-outputs/README.md). Nothing below is safe to run for the first time in
> front of the room.

```bash
# 1. install the plugin (Claude Code; Codex users just run codex from the repo root)
#      /plugin marketplace add ./eyp-pm-skills   # (or your clone directory)
#      /plugin install eyp-pm-skills@eyp-pm-skills

# 2. clone the real Umbraco codebase (large; needed for Workflow 3)
./scripts/clone-umbraco.sh
```

The mock inputs: ten `[MOCK DATA]`-prefixed files in `workshop/mock-data/` (see its README for the
full table — calls, churn workbook, support tickets, review export, public issues, forum digest,
two feature inventories, roadmap deck, evidence-packet note). Plus `workshop/team-capacity.md` for WF3.

---

## Workflow 1 — `pm-ideation`  (signals → problem statement + roadmap rec)  ~15–20 min agent runtime
**Prompt:**
```
/pm-ideation workshop/mock-data
```
**Time option:** this is a big read (16 calls, 41 accounts, 49 reviews, 41 tickets). For a 2-hour
session, consider running WF1 once as a **facilitated demo** (or handing cohorts
`sample-outputs/roadmap-recommendation.md`) and putting participant hands-on time into WF2/WF3.

**Expected output (the data is built to produce this):**
- The gap emerges as a **cluster**, not a keyword: experiment workflow + segment lifecycle +
  preview trust (+ campaign-page performance) → one theme: no trustworthy end-to-end
  personalization campaign flow.
- Quantified: cluster ≈ **27 account-rows / ~$2.18M ARR at risk raw** — and a good run **dedupes**
  the planted duplicate/child accounts (HarborOne UK, BrightCart EU) before summing, and says so.
- Cross-source: ~29–31/41 support tickets (16 escalations overall), ~33/49 reviews, top-voted public issues and
  forum threads — six independent legs.
- Cites the roadmap deck's omission (slide 7) while noting adjacent items ARE planned.
- **Dodges the traps:** the security-owned high-ARR account (different buying center), AI-content
  asks (already on the roadmap — the "wanted-but-already-planned" trap), headless ops asks.

**Talking points:**
- "It had to *cluster* messy sub-themes and dedupe dirty CRM rows — work that normally costs a PM
  days. Ask it why it excluded the $118k security account."
- "Every number traces to a row, call, ticket, or review. Ask 'where did $2.18M come from?'"
- "It argued against itself before recommending; the red-team section is what you take into the
  room."

---

## Workflow 2 — `pm-prototype`  (problem → clickable preview)  ~15–20 min incl. brainstorm
**Prompt:**
```
/pm-prototype workshop/outputs/roadmap-recommendation.md
```
**Workshop default direction (steer the brainstorm here):** the **personalization campaign
studio**, 4-screen happy path —
1. **Campaigns list** (status chips: Draft / Live / **Stopped 🔒**)
2. **Create campaign** (audience → variant → goal)
3. **Per-segment preview** (the trust moment — "this is what Gold-tier sees")
4. **Results** (lift readout + **Promote winner** button + one audit line)

**Scope guardrail (say it at the gate):** *"Don't build lifecycle admin — approval chains, audit
consoles, and state machines are WF3's problem. In the prototype, lifecycle = one 🔒 status chip
and one audit line on Results."* Suggested hypothesis: *"A marketer can go from test idea to a
trusted winner readout without leaving the CMS — and without a developer."*

**Expected output:** a single clickable HTML file that opens **looking like an Umbraco backoffice
screen** — navy top bar, Lato, Umbraco's real design tokens (the template ships an `umbraco` theme
extracted from `@umbraco-ui/uui-css`; prototypes default to it). Reference build:
`sample-outputs/campaign-studio-prototype.html`. Three things to demo: flip the **segment chips** on
Preview (the content actually changes), click **Stop losing variant**
(watch it lock 🔒 and the audit line append), then **Promote winner**. Finish by pinning a 💬
comment and exporting the feedback JSON.

**Talking points:**
- "It made you choose a direction before building, and the banner on the prototype lists what's
  simulated."
- "Notice what it did not build: the audit console. Scope control is part of the exercise."

---

## Workflow 3 — `pm-plan-and-estimate`  (prototype → eng-ready plan + cost)  ~20–30 min
**Prompt:**
```
/pm-plan-and-estimate workshop/outputs/campaign-studio-prototype.html workshop/outputs/campaign-studio-prototype-notes.md workshop/team-capacity.md --codebase workshop/codebase/Umbraco-CMS
```
**Expected output:** planning doc + estimate + spec (reference builds in `sample-outputs/`).
The highlight: the codebase **already contains dormant segment seams** — `ContentVariation.Segment`,
`ISegmentService` with a no-op default, `SegmentSettings.Enabled=false` — so the plan is "provide
the provider + build the studio on an existing extension point," cited to real files, with PERT
ranges, a sprint/runway view from `team-capacity.md`, and a spec whose Verified assumptions show
commands **re-run at emission** — including **verified absences** (proving what is missing on the
critical path) and a deprecation check on every surface the plan builds on.

**Talking points:**
- "The estimate points at files that actually exist; open one live."
- "Optimist vs. skeptical-SRE debate; ranges, not one rosy number; 'needs eng validation' on top."
- "The spec's Verified assumptions are re-executed at write time — nothing is trusted from memory."

---

## The fabrication test (~3 min)
```bash
# temporarily hide the quantitative backbone
mv "workshop/mock-data/[MOCK DATA] Umbraco Churn and Expansion Workbook.xlsx" /tmp/
```
Re-run `/pm-ideation workshop/mock-data`. It should **report the workbook as missing**, downgrade
signal strength (tickets/reviews still support the theme qualitatively), and **not** invent the
$2.18M figure. Restore:
```bash
mv "/tmp/[MOCK DATA] Umbraco Churn and Expansion Workbook.xlsx" workshop/mock-data/
```
**Talking point:** "A correct 'I don't have that' beats a confident fabrication."

---

## If you're short on time (or anything stumbles live)
**Real pre-staged outputs for every stage live in [`sample-outputs/`](sample-outputs/README.md).**
Suggested 2-hour shape: WF1 as facilitated demo (10 min using the pre-staged output + one live
"where did this number come from?" probe) → participants hands-on WF2 (40 min) → WF3 (40 min) →
fabrication test + debrief (20 min).

## Reset between cohorts
Delete generated artifacts in `workshop/outputs/` (keep `.gitkeep`). Mock data and the Umbraco
clone can stay. **Verify the dataset is intact** (the fabrication test moves a file to /tmp):
`ls workshop/mock-data/ | wc -l` → expect **11** (10 data files + README).
