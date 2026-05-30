# Anti-Copy Guidance

The example proposals under [sources/example_proposals/](../../sources/example_proposals/) and the template at [sources/template/proposal_template_examples.md](../../sources/template/proposal_template_examples.md) are STYLE references. Their content reflects past projects, not the current one. Verbatim reuse misrepresents the project and risks plagiarism flags during review.

## Writing style rules

These apply to the generated draft (not to these reference files):

1. **No em-dashes or double-hyphens.** Never use the em-dash character or a double-hyphen (`--`) as sentence punctuation; both are common AI-generated-text tells. Use commas, periods, colons, or parentheses. Ordinary hyphens in compound words (multi-day, cross-embodiment, in-the-wild) are fine, and use the word "to" for numeric and page ranges (3,600 to 7,200; pp. 698 to 702).
2. **Be concise.** Do not pad. Prefer short, direct sentences. Cut throat-clearing openers and redundant restatement.
3. **Length ceiling.** Past accepted proposals run about four pages and 1,100 to 1,360 words. The draft must not exceed that. Treat it as a hard ceiling, not a target to approach from above.
4. **Do not transcribe another project's inherited facts.** When `project_info.txt` marks a value as inherited or provisional from a different project, do not present it as this project's fact. Mark `[TBD: ...]` or raise a question instead. See the Missing Inputs section below.

## Hard rules

1. **No phrase longer than ~7 words copied verbatim** from any example PDF or from `proposal_template_examples.md`. If a sentence pattern is useful, paraphrase it in your own words and with your own structure.

2. **No specific numbers from another proposal.** Utilization percentages, VRAM figures, dataset sizes, GPU counts, checkpoint sizes, GPU-hour totals — these only appear in the draft if they also appear in THIS project's `project_info.txt`, `computation_details.txt`, or the project PDF.

   - Example: if THIS project's `project_info.txt` says "~86% GPU utilization" (because the project inherited that DreamZero profiling), it can appear. If it doesn't, it can't.
   - Example: "approximately 100 TB of scratch storage" appears in the template; it only enters the draft if `project_info.txt` confirms 100 TB for THIS project.

3. **No model names, dataset names, or methodology specifics borrowed from other projects.** "DROID", "Open X", "DreamZero", "navigation policy", "world-action model" — these only appear if THIS project's inputs reference them.

4. **No copied paragraphs with light edits.** If the only change is reordering a few words or swapping synonyms, that is a copy. Paraphrasing means re-expressing the idea with your own sentence structure.

## What you SHOULD borrow from examples

- Sentence rhythm and length variation (short punchy sentences followed by longer technical sentences)
- The shape of how each section flows (what comes first, what comes last)
- Generic technical vocabulary that's standard in the domain: "checkpointing", "NVLink interconnect", "gradient synchronization", "weak/strong scaling", "compute-bound", "I/O-bound", "MFU"
- The level of specificity reviewers respond to: concrete numbers, named systems, explicit comparisons

## Detection routine at draft time

After writing each section, scan it once with these two questions:

1. **Could this sentence appear in any other ML compute proposal verbatim?** If yes, it's generic and fine.
2. **Does this sentence contain a number, name, or specific claim that isn't in `project_info.txt`, `computation_details.txt`, or the project PDF?** If yes → either DELETE the claim or replace with `[TBD: ...]`. Do not fabricate.

## TBD marker convention

- `[TBD: <field name from project_info.txt>]` for missing structured fields.
  Examples: `[TBD: GPUs per node]`, `[TBD: batch size]`, `[TBD: PI name]`.
- `[TBD: <short description>]` for missing soft fields.
  Examples: `[TBD: which conference to target]`, `[TBD: GitHub repo URL]`.
- **One TBD per missing field.** Don't write `[TBD: many things]`.
- TBDs are not prose substitutes. Write `We expect approximately [TBD: VRAM utilization]% GPU utilization`, not `We expect [TBD: a discussion of utilization]`.

## Figure placeholder convention

- `[Figure N: <suggested content>]` inline at the natural anchor point in the prose.
- Suggested content should be specific enough that the user can later generate or insert the right figure.
  Good: `[Figure 1: Loss curve for the preliminary H100 profiling run, with GPU utilization band annotated and convergence point marked at ~3 days]`.
  Bad: `[Figure 1: profiling]`.
- Don't promise figures that the project clearly doesn't have. If unclear, use `[Figure: optional — include only if profiling data available]`.
- Number figures sequentially across the doc, starting at 1.

## End-of-draft Missing Inputs block

At the very end of the draft, append:

```text
<!-- MISSING INPUTS
TBDs:
- [list every [TBD: ...] marker with its section]
Figure placeholders:
- [list every [Figure N: ...] with its section]
-->
```

This gives the user a single place to track everything that needs to be filled in before PDF export.
