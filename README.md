# Marlowe Application Skills

A focused Claude Code skill for drafting Stanford **Marlowe** GPU project proposals fast.

This repo ships a single primary skill — [`marlowe-proposal`](marlowe-proposal/) — that reads structured project inputs and produces a 2–4 page **Computational Suitability Statement (CSS)** draft in Markdown, ready for review and PDF export.

The four upstream [Academic Research Skills (ARS)](https://github.com/Imbad0202/academic-research-skills) suite skills (`deep-research`, `academic-paper`, `academic-paper-reviewer`, `academic-pipeline`) are still present in this repository and can be invoked independently for full-paper workflows, but the primary purpose of this fork is Marlowe CSS drafting.

---

## What the skill does

Given a project folder under `sources/proposals/<name>/`, the `marlowe-proposal` skill:

1. Loads the Marlowe CSS spec from [sources/instructions/instructions.md](sources/instructions/instructions.md) and the four staff review axes (Experience / Software & Resources / Computational Readiness / Need for Marlowe).
2. Reads the project's own inputs — `project_info.txt`, `computation_details.txt`, and the project paper PDF if available.
3. Skims past proposals in [sources/example_proposals/](sources/example_proposals/) for **style only** (no verbatim reuse, no copied numbers, no borrowed dataset / model names).
4. **Extracts 5–15 real citations from the project paper's bibliography** for the Scientific Overview and Impact sections. If extra context is needed, the skill can web-search or download relevant papers via `gdown` / `curl` / `wget` into a `related_work/` subfolder (created on demand inside the project folder). Never fabricates references.
5. Drafts a 2–4 page CSS following the Marlowe section structure (Project & PI, Abstract, Scientific Overview, Prior Experience, Scaling Studies, Computational Profile, Justification for Marlowe, Technical Requirements & Feasibility, Storage & Data, Impact and Acknowledgements, References).
6. Inserts `[TBD: <field>]` for every missing input and `[Figure N: <suggested content>]` for missing visuals.
7. Writes the draft to `sources/proposals/<name>/draft_css_v1.md` (or `v2`, `v3`, … if a prior draft exists).
8. Appends a Missing Inputs checklist at the bottom so you can see what to fill in before submission.

The skill is intentionally lightweight: one Claude pass, no multi-agent orchestration, no interactive clarification loop. The `—` markers in `project_info.txt` and the structured shape of the inputs do the work that would otherwise need a back-and-forth.

---

## Quickstart

### 1. Add your project inputs

Create a folder under `sources/proposals/` named for your project:

```text
sources/proposals/<your-project-name>/
├── project_info.txt           ← Overview + Job Profile + Computational Readiness
├── computation_details.txt    ← GPU-hours math
├── your-paper-or-draft.pdf    ← (optional but helpful — provides Scientific Overview content + bibliography for citations)
└── related_work/              ← (auto-created if the skill needs to download cited papers for context)
```

Use [sources/proposals/tactile-dexterous/project_info.txt](sources/proposals/tactile-dexterous/project_info.txt) as a template for the input shape. Mark anything you don't know yet with a literal em-dash `—`; the skill converts these to `[TBD: <field>]` in the draft.

### 2. Invoke the skill

In Claude Code, say something like:

> Draft the Marlowe CSS for `<your-project-name>`

Claude picks up the `marlowe-proposal` skill, reads your inputs and the Marlowe references, and writes the draft to `sources/proposals/<your-project-name>/draft_css_v1.md`.

### 3. Fill in the TBDs and figures

Open the draft. At the bottom you'll find a `<!-- MISSING INPUTS -->` block listing every `[TBD: ...]` and `[Figure N: ...]` marker plus any open questions the skill flagged. Address them, then re-invoke the skill to regenerate a v2 if you've updated `project_info.txt`.

### 4. Export to PDF

Convert the Markdown to PDF via Pandoc or your preferred tool:

```bash
pandoc sources/proposals/<your-project-name>/draft_css_v1.md \
  -o sources/proposals/<your-project-name>/draft_css_v1.pdf \
  --pdf-engine=xelatex
```

The skill targets roughly 1,100 to 1,400 words (about four pages), matching the length of past accepted proposals. It avoids em-dashes and other AI-typical tells in the prose.

---

## Repository layout

```text
marlowe-application-skills/
├── marlowe-proposal/                    ← the primary skill
│   ├── SKILL.md
│   ├── references/
│   │   ├── marlowe_css_requirements.md  ← distilled Marlowe spec + 4 review axes
│   │   ├── section_writing_guide.md     ← per-section content + length budget + mapping
│   │   ├── anti_copy_guidance.md        ← paraphrase rules + TBD / figure conventions
│   │   └── citations_protocol.md        ← citation sourcing, web lookup, gdown / curl downloads
│   └── templates/
│       └── css_skeleton.md              ← structure-only headings, no reusable prose
│
├── sources/                             ← all per-project inputs and reference materials
│   ├── instructions/                    ← Marlowe official preparation guide
│   ├── template/                        ← base template (don't copy verbatim)
│   ├── example_proposals/               ← past proposals (style reference only)
│   └── proposals/                       ← one folder per project — your inputs and drafts live here
│       └── tactile-dexterous/           ← example worked through end-to-end
│
├── deep-research/                       ← ARS upstream — academic research suite (independent)
├── academic-paper/                      ← ARS upstream — paper writing
├── academic-paper-reviewer/             ← ARS upstream — peer review simulation
├── academic-pipeline/                   ← ARS upstream — full-pipeline orchestrator
└── ...                                  ← ARS upstream support files
```

---

## Inside the `marlowe-proposal` skill

| File | Purpose |
|---|---|
| [SKILL.md](marlowe-proposal/SKILL.md) | Entry point — workflow, triggers, constraints, inputs / outputs |
| [references/marlowe_css_requirements.md](marlowe-proposal/references/marlowe_css_requirements.md) | Distilled Marlowe CSS spec + the four staff review axes + quality signals |
| [references/section_writing_guide.md](marlowe-proposal/references/section_writing_guide.md) | Per-section content brief, length budget, review-axis mapping, pitfalls |
| [references/anti_copy_guidance.md](marlowe-proposal/references/anti_copy_guidance.md) | Paraphrase rules, TBD / figure placeholder conventions, end-of-draft Missing Inputs format |
| [references/citations_protocol.md](marlowe-proposal/references/citations_protocol.md) | Citation sourcing (paper-first, then web, then download to `related_work/`), gdown / curl / wget conventions, numbered IEEE format |
| [templates/css_skeleton.md](marlowe-proposal/templates/css_skeleton.md) | Section ordering and length cues only (HTML comments, no reusable prose) |

---

## Design principles

- **One-pass, no agents.** Single Claude run consumes all references and inputs, then writes the draft. Predictable, fast, no orchestration overhead.
- **TBD-driven.** `—` markers in `project_info.txt` become explicit `[TBD: <field>]` placeholders in the draft. The end-of-document `<!-- MISSING INPUTS -->` block enumerates every TBD, figure placeholder, and open question — so you know exactly what to fill in before submission.
- **Review-axis aware.** Every substantive section is mapped to one of Marlowe's four staff review axes. A section that doesn't visibly advance an axis is a section to rewrite.
- **Anti-copy by construction.** Past proposals are style references only. The skeleton carries no reusable prose. The skill's guidance prohibits reusing phrases longer than ~7 words from examples / template, and forbids importing numbers, dataset names, or model names that don't appear in *this* project's own inputs.

---

## What's not in scope (v0.1)

- LaTeX or direct PDF compilation — Markdown only; you convert downstream
- Figure generation — the skill emits `[Figure N: ...]` placeholders; you supply the actual figures
- Automated similarity check against past proposals — paraphrase enforcement is guidance-only at v0.1
- Interactive clarification loop — one-shot draft only; TBDs surface what's missing

Open enhancements (let the maintainer know if you want them):

- `commands/marlowe-css.md` for a `/marlowe-css <project>` slash trigger
- Optional LaTeX output for stricter page-limit control
- A second-pass self-check that diffs the draft against `example_proposals/` and flags any high-similarity passages

---

## Worked example

The repo ships [sources/proposals/tactile-dexterous/](sources/proposals/tactile-dexterous/) as a worked example. Inputs:

- `project_info.txt` — structured overview + job profile + readiness for the SoTa visuo-tactile manipulation project
- `computation_details.txt` — GPU-hours math (7 methods × 10 tasks × 3 days × 5 iterations = 3,600 H100 GPU-hours, plus an optional +3,600 for in-the-wild human data)
- `_CoRL_2026__Tactile_Skins.pdf` — the underlying paper draft

The produced draft lives at [sources/proposals/tactile-dexterous/draft_css_v1.md](sources/proposals/tactile-dexterous/draft_css_v1.md).

---

## Upstream: Academic Research Skills (ARS)

The four ARS skills shipped in this repo (`deep-research/`, `academic-paper/`, `academic-paper-reviewer/`, `academic-pipeline/`) come from the [Academic Research Skills](https://github.com/Imbad0202/academic-research-skills) upstream. They support full academic-paper workflows — Socratic research dialogue, paper drafting, multi-perspective peer review, and 10-stage pipeline orchestration. See [MODE_REGISTRY.md](MODE_REGISTRY.md) for the 25 ARS modes and the upstream README for suite-level documentation.

If you only need Marlowe CSS drafts, you can safely ignore the ARS skills — they don't interfere with `marlowe-proposal`.

---

## License

This repository is a downstream of the CC-BY-NC 4.0 [Academic Research Skills](https://github.com/Imbad0202/academic-research-skills) suite. The `marlowe-proposal` skill follows the same license. Non-commercial academic use only.
