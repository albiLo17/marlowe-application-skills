# Marlowe CSS Requirements (distilled)

Source: [sources/instructions/instructions.md](../../sources/instructions/instructions.md). Read that file too if you need the verbatim spec.

## Document length

- **Medium projects:** 2-4 page PDF
- **Large projects (>10,000 GPU-hours):** same 2-4 page range, but with explicit strong evidence of readiness and scalability

## What the CSS PDF MUST contain

Per the Marlowe Preparation Guide, the CSS PDF must include:

1. **A brief scientific overview** — context only, not a full scientific narrative
2. **Prior experience** on Marlowe or similar GPU-based systems
3. **Scaling studies** — completed or planned, weak or strong
4. **Codes and toolchains** — frameworks, libraries, GitHub links, run instructions
5. **Job profiles** — wall time, GPUs/node, concurrency, memory, I/O, checkpointing
6. **Computational readiness and tuning status** — measured or expected MFU; an explicit justification for why standard lab/cloud resources are insufficient

## Surrounding application-form sections (inlined into the CSS)

The Marlowe application form also asks for:

- **Section 1: Project & PI Information** — title, abstract, PI, group SUNet IDs, urgent timelines (e.g., upcoming grant or conference deadlines)
- **Section 3: Computational Profile** — typical / max job, wall time, GPU and node usage, concurrency, usage pattern
- **Section 4: Technical Requirements & Feasibility** — software frameworks, container tools, special configuration needs
- **Section 5: Storage & Data** — scratch capacity, data sourcing / movement / storage, checkpointing detail
- **Section 6: Impact & Acknowledgments** — expected outputs (publications, software)

Standard practice (visible in the example proposals) is to inline Sections 3-6 directly into the CSS PDF because they need narrative justification, not just form fields.

## The four staff review axes (Medium and Large)

Every substantive section of the CSS should advance at least one of these. A section that doesn't visibly serve any axis is weak.

1. **Experience**
   Familiarity with GPU clusters, including past experiences (lab servers, GCP, Sherlock, prior Marlowe, etc.)

2. **Software and Resource Requirements**
   Codes, toolchains, clear instructions for running applications, job profiles for GPU / CPU / memory / I/O / checkpointing

3. **Computational Readiness**
   Scaling, optimization, computational efficiency (e.g., MFU), readiness for production-scale runs

4. **Need for Marlowe**
   Justification for needing GPU resources beyond standard lab, cloud, or Sherlock environments. Specifically: what does Marlowe provide that the alternatives do not?

## Quality signals (what staff respond to)

- **Concrete numbers beat adjectives.** "~86% GPU utilization, ~80 GB VRAM saturation" outperforms "high utilization".
- **A small scaling study is more persuasive than a verbal claim of scalability.** Even a single-node profiling run with a loss curve carries weight.
- **Checkpoint restart-ability + retention policy** signals operational maturity.
- **The "why Marlowe and not X" answer must name X specifically.** Generic complaints about contention are weaker than "lab does not provide H100, GCP A3 contention is observed at peak, Sherlock partition lacks NVLink for our gradient-sync regime".
- **Open-source / pip-installable software stack** is a positive signal — special kernels and proprietary deps are friction.

## Quality signals (what staff penalize, inferred)

- Round numbers without justification ("we need 100 TB" without tying it to dataset + checkpoint counts)
- "Our team has experience" without naming systems or scales
- Scaling claims with no profiling evidence and no plan to obtain it
- Scientific Overview that consumes a third of the page budget
- Mismatched numbers between Scaling Studies and Computational Profile sections — keep one canonical set
