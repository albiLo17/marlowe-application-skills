# Citations Protocol

The CSS is a 2–4 page compute proposal, not a literature review — citations should be sparse, real, and motivational. Target **5–15 references total**, concentrated in the Scientific Overview and Impact sections.

## Real-citations-only rule

**Every citation in the draft must be a real, verifiable reference.** Never fabricate authors, titles, years, venues, or DOIs. If you cannot verify a reference with at least author + year + title + venue, drop it from the draft.

## Where citations belong

- **Scientific Overview (primary):** foundational papers the project builds on — base method, sensing modality, prior cross-embodiment / co-training work, key competing approaches
- **Impact and Acknowledgements (light):** target venue if anchored, prior related results being extended
- **Prior Experience / Tech Requirements (rare):** typically uncited — these describe operational practice and frameworks, not literature
- **Other sections:** generally no citations

## Source hierarchy

When picking which papers to cite, use this order:

1. **The project paper PDF's own references.** The paper draft in `sources/proposals/<name>/` already has a curated bibliography. Read its introduction, related-work section, and reference list, and pull the most foundational entries from there. This is the primary source.

2. **Web lookup for metadata completion.** If the paper PDF mentions a work by name without full bibliographic detail, you MAY look it up online to fill in the metadata (authors, year, venue, DOI / arXiv ID). Tools: `WebSearch`, `WebFetch`, or a direct arXiv / Semantic Scholar / OpenAlex query.

3. **Download via `gdown` / `curl` / `wget`** if you need to read a paper for context that the project paper doesn't provide. Save into `sources/proposals/<name>/related_work/` (create the folder if absent).

## Download conventions

If a paper isn't in the project folder and you need its content:

**`related_work/` folder:**
```
sources/proposals/<project-name>/
├── project_info.txt
├── computation_details.txt
├── <project-paper>.pdf
└── related_work/                 ← create if missing
    ├── <citation-key>.pdf
    └── ...
```

Use citation-key style filenames (e.g., `chi2023diffusion.pdf`, `yuan2017gelsight.pdf`) so the local filename maps to the bibliography entry.

**Bash commands the skill may use:**

```bash
# Create the folder
mkdir -p sources/proposals/<name>/related_work

# Google Drive (gdown)
gdown <google-drive-file-id> -O sources/proposals/<name>/related_work/<citation-key>.pdf
gdown "https://drive.google.com/uc?id=<FILE_ID>" -O ...

# Direct URL (curl)
curl -L -o sources/proposals/<name>/related_work/<citation-key>.pdf <pdf-url>

# arXiv
curl -L -o sources/proposals/<name>/related_work/<citation-key>.pdf \
  "https://arxiv.org/pdf/<arxiv-id>.pdf"

# wget alternative
wget -O sources/proposals/<name>/related_work/<citation-key>.pdf <pdf-url>
```

**When to download vs cite-without-reading:** if the paper PDF clearly establishes the reference's relevance and provides enough context for the CSS Scientific Overview, citing without downloading is fine. Only download when you need to write a substantive sentence about the work that goes beyond what the project paper itself provides.

## Citation format

Use **numbered IEEE-style citations**, e.g. `[3]`, for brevity. The References section uses the format:

```
[N] Author1, Author2, …, "Title," Venue, Year. (arXiv:XXXX.XXXXX | DOI: 10.XXXX/XXXXX)
```

Examples:
```
[1] C. Chi, Z. Xu, S. Feng, et al., "Diffusion Policy: Visuomotor Policy Learning via Action Diffusion," in Proc. Robotics: Science and Systems (RSS), 2023.

[2] W. Yuan, S. Dong, E. H. Adelson, "GelSight: High-Resolution Robot Tactile Sensors for Estimating Geometry and Force," Sensors, vol. 17, no. 12, p. 2762, 2017.
```

Cite inline as `[1]`, `[1, 3]`, or `[1–4]` (en-dash for ranges).

## Extraction workflow

When invoked, the skill should:

1. **Read the project paper PDF**'s abstract, introduction, related-work section, and reference list. Pages vary by paper — start with pages 1–5 to get the framing, then locate references (typically the last 1–3 pages).
2. **Identify 5–15 foundational citations** that fit the Scientific Overview and Impact sections. Prefer breadth over depth: 2–4 citations per cluster (e.g., tactile sensing, cross-embodiment learning, Diffusion Policy / base method, scaling laws if relevant).
3. **For each citation, capture:** authors, title, venue, year, identifier (arXiv ID or DOI). If a field is missing from the paper's bibliography, attempt web lookup.
4. **If a citation is critical but the paper PDF doesn't provide enough context to write a sentence about it,** download it to `related_work/` and read just enough to ground the sentence.
5. **Insert inline citations** at natural anchor points in the prose (1–3 citations per Scientific Overview paragraph; 0–1 in Impact).
6. **Build the References section** in numbered IEEE format, ordered by first appearance in the draft.

## Anti-patterns

- Citing a paper based only on its name without verifying it exists
- Inflating the citation count to look thorough — CSS readers want compute justification, not a literature survey
- Citing papers that are tangential to the compute story — every citation should support a specific sentence the reviewer actually needs to read
- Reusing a citation from `sources/example_proposals/` or `sources/template/proposal_template_examples.md` unless that paper is also genuinely relevant to *this* project (the anti-copy rule still applies — see `anti_copy_guidance.md`)
- Re-downloading a paper that's already in `related_work/` — check before fetching
