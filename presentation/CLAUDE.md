# DTN Presentation Project

## Harness: research and build the DTN presentation

**Goal:** Audit, improve, and extend the team's existing DTN research (from Notion) into a
15-minute Marp presentation for an undergraduate networks course. Differentiate against a
competing team on the same topic for a higher grade, and harden the slides to defend the
post-talk Q&A against tough questions.

**Trigger:** For requests about DTN / delay- and disruption-tolerant networking / extreme or
long-delay communication / creating or improving the slides, use the
`dtn-presentation-orchestrator` skill. Simple conceptual questions can be answered directly.

**Key asset:** the NASA JPL ION reference implementation, now vendored as the `src/` git
submodule (its `doc/` official PDFs and the `owltsim` light-time simulator power the
"extension" demo).

**Input:** team research lives in `_workspace/research/` (formerly `_input/`; Notion Markdown
exports). **Output:** `presentation.md` + `presentation.pdf` (Marp).

**Change log:**
| Date | Change | Target | Reason |
|------|--------|--------|--------|
| 2026-06-02 | Initial setup (5 agents + 2 skills + orchestrator) | all | New harness to research and build the DTN talk |
| 2026-06-02 | Added qa-defense-strategist agent + presentation-defense skill; fixed the 15-minute budget; added differentiation analysis to the curator | agents/qa-defense-strategist.md, skills/presentation-defense, content-pedagogist, research-curator, orchestrator | Reflect differentiation vs. the competing team and post-talk Q&A defense |
| 2026-06-02 | Reimplemented the Marp theme in the xAI (x.ai) design; pinned the Pretendard Korean font; added --no-stdin to the build | skills/marp-slide-builder (theme.css, xai-design.md, SKILL.md, marp-syntax.md) | Apply the xAI design and set the Pretendard font |
| 2026-06-04 | Consolidated into the 2026-computer-network git repo (presentation/ + ION src/ submodule); moved _input -> _workspace/research; added English README/DEMO | repo layout | Organize and publish to GitHub |
