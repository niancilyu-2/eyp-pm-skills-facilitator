# sample-outputs — captured artifacts (workshop fallbacks)

Actual outputs of running the three-skill chain on `workshop/mock-data/` and the Umbraco clone
@ `ba6ec7ab` (2026-07-06, revised after an engineering review): every number was computed from
the inputs of that run, and every `[Verified]` item in `SPEC.md` shows the command that was
executed with its pasted output.

| File | Produced by | Use |
|------|-------------|-----|
| `roadmap-recommendation.md` | WF1 | demo handout; feeds WF2 |
| `campaign-studio-prototype.html` | WF2 | open in a browser; 4-screen reference build |
| `campaign-studio-prototype-notes.md` | WF2 | the handoff payload WF3 consumes |
| `PLANNING_DOC.md` / `ESTIMATE.md` / `SPEC.md` | WF3 | fallbacks for the planning segment |

Citations pin to clone `ba6ec7ab`; a fresh clone may drift — the skills re-verify evidence at
emission rather than trusting old citations, including these. After a data swap, regenerate by
re-running the chain.
