---
name: marlowe-proposal
description: "Draft a Marlowe Computational Suitability Statement (CSS) for a Medium or Large GPU project application. Reads project inputs from sources/proposals/<name>/, applies Marlowe-specific section requirements and the four staff review axes, extracts real citations from the project paper PDF (optionally downloading related work via gdown / curl), and produces a 2-4 page Markdown draft with [TBD] markers for missing fields and [Figure N] placeholders for missing visuals. Triggers on: marlowe proposal, marlowe css, computational suitability statement, draft marlowe application, marlowe application."
metadata:
  version: "0.3.0"
  last_updated: "2026-05-28"
  status: active
---

# Marlowe Proposal — Computational Suitability Statement (CSS) Drafter

A focused, single-pass skill for drafting Marlowe GPU project applications (Medium / Large). It reads a structured project folder under [sources/proposals/](../sources/proposals/), applies the Marlowe CSS requirements, extracts real citations from the project paper's bibliography, and writes a 2-4 page Markdown draft with `[TBD: ...]` markers wherever the inputs are incomplete.

This skill is intentionally lightweight: one Claude pass, no multi-agent ceremony, no interactive clarification loop.

## Trigger Conditions

Activate when the user asks to draft, generate, or write a Marlowe proposal / application / CSS for a specific project. Examples:

- "Draft the Marlowe CSS for tactile-dexterous"
- "Write a Marlowe proposal for `<project>`"
- "Generate a Computational Suitability Statement at `sources/proposals/<name>`"

If the project name is ambiguous, list directories under [sources/proposals/](../sources/proposals/) and ask which one.

## Required Inputs (per project)

Inputs live under `sources/proposals/<project-name>/`. Expected files:

| File / Folder | Purpose | Required? |
|---|---|---|
| `project_info.txt` | Overview + Job Profile + Computational Readiness. Fields marked `—` are TBDs. | Required |
| `computation_details.txt` | GPU-hours math and additional compute justification. | Required |
| `*.pdf` | Project paper / draft. Used to write the Scientific Overview, ground Impact statements, and source citations from its bibliography. | Recommended |
| `*.png` / `*.pdf` (figures) | Pre-generated profiling figures. If absent, the skill inserts `[Figure N: ...]` placeholders. | Optional |
| `related_work/` | Subfolder for cited papers downloaded by the skill to ground citations. The skill creates this folder if it doesn't exist. | Auto-created on demand |

If `project_info.txt` or `computation_details.txt` is missing, surface that as a hard error and ask the user to provide it before drafting.

## Required References (read every invocation)

Before drafting, READ all four:

1. [references/marlowe_css_requirements.md](references/marlowe_css_requirements.md) — distilled Marlowe spec and the four staff review axes
2. [references/section_writing_guide.md](references/section_writing_guide.md) — per-section content, length budget, and review-axis mapping
3. [references/anti_copy_guidance.md](references/anti_copy_guidance.md) — paraphrase rules + TBD / figure conventions
4. [references/citations_protocol.md](references/citations_protocol.md) — citation sourcing (project paper's bibliography first, then web lookup, then `gdown` / `curl` / `wget` download to `related_work/`), citation format

The skeleton at [templates/css_skeleton.md](templates/css_skeleton.md) is for section ordering and length cues only. Do NOT reuse its prose or its inline HTML comments.

## Style Reference: Past Proposals

PDFs under [sources/example_proposals/](../sources/example_proposals/) and the template at [sources/template/proposal_template_examples.md](../sources/template/proposal_template_examples.md) are STYLE references only.

You MAY:
- Skim them to learn voice, sentence rhythm, and how technical justifications are framed
- Note which figures and tables they include and how they're captioned
- Borrow generic domain vocabulary (e.g., "checkpointing", "NVLink interconnect", "weak/strong scaling")

You MUST NOT:
- Reuse any phrase longer than ~7 words verbatim
- Copy paragraphs and minimally edit them
- Reuse specific numbers, dataset names, or model claims that don't appear in THIS project's own inputs
- Reuse citations from `sources/example_proposals/` or `sources/template/` unless those papers are also genuinely relevant to this project

See [references/anti_copy_guidance.md](references/anti_copy_guidance.md) for the full rule set.

## Workflow

When invoked for project `<name>`:

1. **Locate the project folder**: `sources/proposals/<name>/`. List its contents to confirm available inputs. If the folder doesn't exist, ask the user for the correct path.

2. **Read inputs**: `project_info.txt`, `computation_details.txt`, and the project PDF if present. For every `—` in `project_info.txt`, record the field name as a future TBD.

3. **Read all four reference files** under `marlowe-proposal/references/`.

4. **Extract citations from the project paper**. Read the paper PDF's introduction, related-work section, and bibliography. Identify 5–15 foundational citations that fit the Scientific Overview and Impact sections — these are the only sections where citations belong in a CSS. Capture authors / title / venue / year / identifier for each. If a relevant work needs more context than the project paper provides, you are explicitly authorized to:
   - Search the web for metadata or context (WebSearch / WebFetch)
   - Create `sources/proposals/<name>/related_work/` and download relevant PDFs via `gdown` (Google Drive), `curl`, or `wget`
   - Read each downloaded PDF only as much as needed to write one grounded sentence
   
   **Real citations only** — never fabricate authors, years, venues, or DOIs. See [references/citations_protocol.md](references/citations_protocol.md).

5. **Skim 1-2 example proposals** for style only — sentence rhythm cues, not phrasing or numbers. Stop reading once you have a feel for the voice; do not exhaustively process them.

6. **Draft section-by-section** following the ordering in `templates/css_skeleton.md` and the per-section guidance in `section_writing_guide.md`:
   - Project Title + PI + Group member SUNet IDs
   - Abstract
   - Scientific Overview (citations: 3–6)
   - Prior Experience (citations: 0–1)
   - Scaling Studies (citations: 0–1)
   - Computational Profile
   - Justification for Marlowe
   - Technical Requirements & Feasibility
   - Storage & Data
   - Impact and Acknowledgements (citations: 0–2)
   - References

   For each section: respect the length budget, map content to the relevant review axis, insert `[TBD: <field>]` for missing inputs, insert `[Figure N: <suggested content>]` for visuals, and cite inline using numbered IEEE format (`[1]`, `[2, 3]`, `[1–4]`).

7. **Surface the GPU-hour math** from `computation_details.txt` explicitly (e.g., methods × tasks × days × iterations). Put it in Scaling Studies or Computational Profile / Justification, whichever fits the project's framing.

8. **Write the draft** to `sources/proposals/<name>/draft_css_v1.md`. If `draft_css_v1.md` already exists, increment to `draft_css_v2.md`, and so on.

9. **Append a Missing Inputs checklist** at the bottom of the draft, inside an HTML comment block marked `<!-- MISSING INPUTS -->`, listing every TBD field, every figure placeholder, and the count + provenance of cited references (e.g., "8 real citations extracted from the project paper's bibliography; 1 [TBD: ...] reference").

10. **Print a one-line summary** to the user: word count, section count, number of TBDs, number of figure placeholders, number of real citations, output path.

## Constraints

- **Length cap (hard):** do not exceed the length of the example proposals in `sources/example_proposals/`. Those run about four pages and roughly 1,100 to 1,360 words. Aim for about 1,200 words excluding references. Treat the example length as a ceiling, not a target to approach from above.
- **Be concise.** Cut filler, throat-clearing openers, and redundant restatement. Prefer short, direct sentences. Do not pad a section to look thorough.
- **No em-dashes or double-hyphens in the draft.** Never use the em-dash character or a double-hyphen as sentence punctuation; they read as AI-generated text. Use commas, periods, colons, or parentheses instead. Ordinary hyphens in compound words (multi-day, cross-embodiment) are fine, and use the word "to" for numeric and page ranges.
- **Review-axis coverage:** Marlowe staff review on four axes (Experience, Software & Resources, Computational Readiness, Need for Marlowe). Every substantive section should advance at least one. See the mapping table in `section_writing_guide.md`.
- **Scientific Overview is brief** (about 150 words). Marlowe's spec says the CSS is about computational suitability, not a full scientific narrative.
- **Citations are sparse but real.** Target 5 to 10 references total, concentrated in Scientific Overview and Impact. Never fabricate.
- **Voice:** first-person plural; present or future tense for proposed work, past tense for completed profiling and prior experience.
- **No hallucinated numbers, no inherited claims from other projects.** If a number or claim is not in `project_info.txt`, `computation_details.txt`, or the project PDF, do not assert it. Where `project_info.txt` carries values inherited or provisional from a different project, do not transcribe them as this project's facts; mark `[TBD: ...]` or raise a question in the Missing Inputs block. Reviewers will check.

## Out of scope (v0.3)

- LaTeX / PDF compilation — Markdown only; the user converts downstream
- Figure generation — placeholders only
- Automated similarity check against past proposals — paraphrase enforcement is guidance-only
- Interactive clarification — one-shot draft only; TBDs surface missing info
