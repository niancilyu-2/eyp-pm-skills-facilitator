# sample-outputs — REAL captured artifacts (and workshop fallbacks)

Unlike `skills/*/examples/` (illustrative), these six files are **actual outputs of running the
three-skill chain** on the promoted `workshop/mock-data/` set (campaign-studio scenario) and the
Umbraco clone @ `ba6ec7ab`, 2026-07-06: every number was computed from the inputs in that run, and
every `[Verified]` item in `SPEC.md` shows the command that was actually executed with its pasted
output. The underlying scenario data is synthetic (see `../mock-data/README.md`).

| File | Produced by | Use in the workshop |
|------|-------------|---------------------|
| `roadmap-recommendation.md` | WF1 `pm-ideation` | fallback / facilitated-demo handout; feeds WF2 |
| `campaign-studio-prototype.html` | WF2 `pm-prototype` | open in a browser; the right-sized 4-screen reference (guardrail: lifecycle = one 🔒 chip + one audit line) |
| `campaign-studio-prototype-notes.md` | WF2 | the handoff payload WF3 consumes |
| `PLANNING_DOC.md` | WF3 `pm-plan-and-estimate` | fallback for the eng-plan segment |
| `ESTIMATE.md` | WF3 | fallback for the estimate segment |
| `SPEC.md` | WF3 | the thorough-writing-plans-ready precursor artifact |

**Caveats:**
1. This run was performed by the skill author following the skills' own workflow. Rehearse a fresh
   run on your own machine before the session (per the facilitator guide).
2. The codebase citations pin to clone `ba6ec7ab`; a fresh Umbraco clone may drift (that's why the
   skills re-verify evidence at emission instead of trusting old citations — including these).

After a data swap, regenerate these by re-running the chain (see `docs/DATA-SWAP-CHECKLIST.md`).
