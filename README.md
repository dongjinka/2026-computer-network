# 2026 Computer Networks Team Project Group1 

## **Communication Under Extreme Delay & Disruption : Delay-Tolerant Networking**

<div align="center">
  
[Overview](#overview) · [Presentation](#presentation) · [Reproduce the demo](#reproducing-the-demo) · [Structure](#repository-structure) · [Team](#team)

</div>

---

## Overview

TCP/IP quietly assumes one thing: an unbroken end-to-end path that exists *right now*. Push
communication to its limit — a rover on Mars, minutes of one-way delay, links that drop for
hours — and that assumption falls apart.

This project shows how networking survives that world:

- **The problem** — why TCP/IP breaks under long delay and frequent disruption.
- **The answer** — how Delay-Tolerant Networking holds data and forwards it later
  (*store-carry-forward*) using the Bundle Protocol (BPv7, RFC 9171).
- **The proof** — we build NASA JPL's **ION-DTN**, fake a 600-second Earth–Mars delay with its
  `owltsim` tool, and watch one message survive a dead link, in running code.

→ Reproduction steps: **[DEMO.md](DEMO.md)**

## Presentation

The full 15-minute talk lives in `presentation/`.

| Format | File |
|--------|------|
| Slides (PDF) | `presentation/CN_Group1_…_DTN.pdf` |
| Web deck | `presentation/CN_Group1_…_DTN.html` |
| Editable source (Marp) | `presentation/presentation.md` |
| Speaker script | `presentation/presentation_script.md` |

Re-render the source with `marp presentation.md --pdf`, or use the *Marp for VS Code* extension.

## Reproducing the demo

`src/` pins `nasa-jpl/ION-DTN` at commit `e9863023e` as a submodule — referenced, not copied in.

```bash
git clone --recurse-submodules <repo-url>   # fresh clone, with the submodule
git submodule update --init --recursive      # if you already cloned
```

Then follow **[DEMO.md](DEMO.md)** to build ION and run the two-node, 600-second-delay test.

## Repository structure

```
2026-computer-network/
├── presentation/     Slides, research, and Q&A prep — our deliverables
│   ├── presentation.md / .pdf      Marp source and rendered slides
│   ├── presentation_script.md      Speaker script
│   ├── CN_Group1_…_DTN.*           Final deck: PPTX, PDF, HTML
│   ├── _workspace/                 research/ (team sources) and Q&A prep
│   └── .claude/                    Agent/skill harness used to build the deck
├── src/              NASA JPL ION-DTN — git submodule, reference only
├── DEMO.md           How to reproduce the ION-DTN demo
└── README.md
```

## Team

2026 Computer Networks Group 1

| Member | Focus | Key question |
|--------|-------|--------------|
| Jaewon Kim (김재원) | TCP/IP limits | Why does TCP/IP break under extreme delay? |
| Seojun Park (박서준) | DTN architecture and mechanisms | How does DTN's structure solve it? |
| Dongjin Ka (가동진) | ION-DTN and real-world cases | How is DTN implemented and used? |

## License & attribution

- **ION-DTN** (`src/`) is a separate NASA JPL project under its own license. This repository only
  references a commit and does not redistribute its code — https://github.com/nasa-jpl/ION-DTN
- Slides and research materials are the team's own work.
- Font: Pretendard, under the SIL Open Font License.
