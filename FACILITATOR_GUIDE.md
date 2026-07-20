# Facilitator guide

Run-of-show for the three-skill session on the Umbraco scenario: prompts, timings, demo moves,
talking points. The seeded scenario and every expected number live in [`ANSWER_KEY.md`](ANSWER_KEY.md);
pre-staged outputs for every stage live in [`sample-outputs/`](sample-outputs/README.md).

**Participant framing (one sentence):** *"Customers can't run personalization campaigns they
trust in Umbraco — find the gap, prototype the fix, cost it."*

The skills are general-purpose; the scenario comes entirely from the synthetic data (every file
is `[MOCK DATA]`-prefixed and labeled fictional).

## Setup (before the session)

Send participants the public repo's `workshop/PRE_READ.md` about a week ahead
([link](https://github.com/niancilyu-2/eyp-pm-skills/blob/main/workshop/PRE_READ.md)).

Two rules that prevent live failures:
1. Always run the agent from the repo root, never from inside `workshop/codebase/Umbraco-CMS`
   (that clone ships its own agent config).
2. Rehearse the full chain once on the session machine beforehand, including the fabrication
   test, and keep the outputs.

```bash
# install the plugin (Claude Code; Codex users just run codex from the repo root)
#   /plugin marketplace add ./eyp-pm-skills
#   /plugin install eyp-pm-skills@eyp-pm-skills

# clone the real Umbraco codebase (large; needed for Workflow 3)
./scripts/clone-umbraco.sh
```

Inputs: the ten files in `workshop/mock-data/` (see its README), plus `workshop/team-capacity.md`
for WF3.

## Workflow 1 — `pm-ideation`  (~15–20 min agent runtime)

```
/pm-ideation workshop/mock-data workshop/product-goals.md
```
(The second path is optional context — the org's leading KPIs — so success criteria land in
product language, not just dollars.)

For a 2-hour session this is the stage to run as a facilitated demo (or hand cohorts
`sample-outputs/roadmap-recommendation.md`) so participant hands-on time goes to WF2/WF3 —
it's a big read (16 calls, 41 accounts, 49 reviews, 41 tickets).

A good run clusters the sub-themes into one gap, dedupes the planted CRM rows and says so, cites
the roadmap omission, and sets the decoys aside — details and numbers in the answer key.

Talking points:
- "It clustered messy sub-themes and deduped dirty CRM rows — work that takes a PM days. Ask it
  why it excluded the $118k security account."
- "Every number traces to a row, call, ticket, or review. Ask where the headline figure came from."
- "It argued against itself before recommending — the red-team section is what you take into the
  room."

## Workflow 2 — `pm-prototype`  (~15–20 min incl. brainstorm)

```
/pm-prototype workshop/outputs/roadmap-recommendation.md
```

Steer the brainstorm to the campaign studio, 4-screen happy path: campaigns list (Draft / Live /
Stopped 🔒) → create (audience → variant → goal) → per-segment preview → results (lift readout,
Promote winner, one audit line).

Scope guardrail, said at the gate: *"Don't build lifecycle admin — approval chains, audit
consoles, and state machines are WF3's problem. Lifecycle here is one 🔒 chip and one audit
line."* Suggested hypothesis: *"A marketer can go from test idea to a trusted winner readout
without leaving the CMS — and without a developer."*

The output opens looking like an Umbraco backoffice screen (the template ships a theme built
from Umbraco's design tokens). Demo moves: flip the segment chips on Preview, click Stop losing
variant (it locks and the audit line appends), then Promote winner; finish by pinning a 💬
comment and exporting the feedback JSON.

Talking points:
- "It made you choose a direction before building, and the banner lists what's simulated."
- "Notice what it did not build: the audit console."
- If asked "how do I get this to customers?": it's one self-contained HTML file — share a link
  (SharePoint/Drive or a static host), never a zipped attachment; details in the skill's
  zero-build reference.

## Workflow 3 — `pm-plan-and-estimate`  (~20–30 min)

```
/pm-plan-and-estimate workshop/outputs/campaign-studio-prototype.html workshop/outputs/campaign-studio-prototype-notes.md workshop/team-capacity.md --codebase workshop/codebase/Umbraco-CMS
```

The highlight: the codebase already contains dormant segment seams (`ContentVariation.Segment`,
`ISegmentService` with a no-op default, `SegmentSettings.Enabled=false`), so the plan becomes
"provide the provider, build the studio on the extension point" — cited to real files, with PERT
ranges, a sprint/runway view from `team-capacity.md`, and a spec whose verified assumptions were
re-executed at write time (including verified absences and a deprecation check).

Talking points:
- Frame the boundary up front: "parts of this sit in engineering's territory — the point is to
  arrive with a grounded draft that saves them estimating from scratch, and everything is stamped
  'needs engineering validation'. Calibrate to your org's PM/eng split."
- "The estimate points at files that actually exist; open one live."
- "Optimist vs. skeptic debate, ranges instead of one number, 'needs eng validation' on top."
- "Nothing in the spec is trusted from memory — the commands were re-run when it was written."

## The fabrication test (~3 min)

```bash
mv "workshop/mock-data/[MOCK DATA] Umbraco Churn and Expansion Workbook.xlsx" /tmp/
```
Re-run `/pm-ideation workshop/mock-data`. It should report the workbook as missing, downgrade
signal strength, and not invent the dollar figure. Restore:
```bash
mv "/tmp/[MOCK DATA] Umbraco Churn and Expansion Workbook.xlsx" workshop/mock-data/
```
Talking point: "a correct 'I don't have that' is the behavior you're paying for."

## Suggested 2-hour shape

WF1 as facilitated demo (10 min on the pre-staged output plus one live "where did this number
come from?" probe) → hands-on WF2 (40) → WF3 (40) → fabrication test + debrief (20). If anything
stumbles live, swap in the matching file from `sample-outputs/`.

## Reset between cohorts

Delete generated artifacts in `workshop/outputs/` (keep `.gitkeep`). Mock data and the clone can
stay. Verify the dataset is intact (the fabrication test moves a file to /tmp):
`ls workshop/mock-data/ | wc -l` should print 11.
