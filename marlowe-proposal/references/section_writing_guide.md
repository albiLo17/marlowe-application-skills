# Per-Section Writing Guide

Length budgets sum to ~1,500-2,000 words for a Medium 2-4 page Markdown draft. Adjust modestly if the inputs are denser, but do not exceed the page budget.

For paraphrase rules and conventions, see [anti_copy_guidance.md](anti_copy_guidance.md).

---

## 1. Project & PI Information (~50-100 words)

**What to write:**
- Project title (from `project_info.txt` Overview)
- PI name + group member SUNet IDs (likely `[TBD]` unless the inputs include them)
- 1-2 sentence project framing drawn from the project's one-sentence description

**Review axis:** N/A (administrative)

---

## 2. Abstract (~120-180 words)

**What to write:**
- The problem
- The approach
- Why the approach is GPU-heavy
- What Marlowe access enables that would otherwise be blocked

Should read standalone — a reviewer skimming only the abstract should understand the compute ask.

**Review axis:** Need for Marlowe (lightly)

**Pitfall:** Don't write a scientific abstract here. Foreground the compute angle.

---

## 3. Scientific Overview (~150-250 words)

**What to write:**
- Background + research question + the approach the compute is for
- Connect each scientific element to a downstream compute requirement: data scale → I/O, model size → VRAM, multi-task evaluation → concurrency, multi-seed runs → wall-time × concurrency, etc.

Marlowe's instructions explicitly say "brief, for context" — keep it tight.

**Review axis:** Need for Marlowe (sets up the why)

**Pitfall:** Inflating the science section at the expense of the compute sections. If the scientific overview is more than ~15% of the doc, trim.

---

## 4. Prior Experience (~150-220 words)

**What to write:**
- GPU cluster experience: SLURM, multi-node, distributed training, cloud GPUs
- Specific systems used: lab servers, GCP A3, Sherlock, prior Marlowe allocations
- Practices: profiling, checkpointing, batch-size tuning, parallelism strategies
- Datasets at scale handled (named, not just "large datasets")
- Software stacks the team is fluent in

**Review axis:** Experience (primary), Computational Readiness (secondary)

**Pitfall:** Vague "our team has experience" — be specific about systems and scales.

---

## 5. Scaling Studies (~250-350 words)

**What to write:**
- Demonstrated profiling evidence from the inputs: GPU utilization %, VRAM saturation, throughput, loss-curve behavior
- Multi-GPU / multi-node communication patterns (NVLink, NCCL, gradient sync regime)
- Why high inter-GPU bandwidth matters (if it does)
- Planned scaling: parameter range, dataset scale, concurrent jobs
- This is one valid home for the GPU-hour math from `computation_details.txt` if the framing is "scaling out across methods × tasks × seeds"

If `[Figure 1: ...]` is appropriate (e.g., a loss curve), anchor it here.

**Review axis:** Computational Readiness (primary), Need for Marlowe (secondary)

**Pitfall:** Asserting scalability without numbers. If profiling exists in `project_info.txt`, lift the numbers verbatim. If none, mark `[TBD: scaling profile]`.

---

## 6. Computational Profile (~200-280 words)

**What to write:**
- Typical job: GPUs/job, wall time/job, GPUs/node, nodes
- Max / peak concurrency
- CPU and RAM per job
- VRAM target / utilization target
- Job behavior: compute-bound, I/O-bound, or communication-bound
- **Total ask in GPU-hours** (from `computation_details.txt`), broken out by experiment family if relevant

**Review axis:** Software and Resource Requirements (primary), Computational Readiness (secondary)

**Pitfall:** Mismatched numbers between Scaling Studies and Computational Profile. Keep one canonical set across the whole document.

---

## 7. Justification for Marlowe (~150-220 words)

**What to write:**
- Why current options are inadequate, **named specifically**:
  - Lab servers — what's missing? (e.g., no H100, contention, no NVLink)
  - GCP / cloud — cost, availability, contention
  - Sherlock — partition limitations, queue characteristics
- What Marlowe provides that the alternatives do not (H100 availability, NVLink, multi-day run stability, priority scheduling, fast interconnect)
- Tie back to one or two specific bottlenecks in the workload (e.g., communication-bound multi-GPU regime, multi-day uninterrupted runs)

**Review axis:** Need for Marlowe (primary)

**Pitfall:** Generic "GPUs are expensive" framing. Reviewers want a concrete inadequacy claim against a named alternative.

---

## 8. Technical Requirements & Feasibility (~100-160 words)

**What to write:**
- Frameworks (PyTorch / JAX / specific libraries)
- CUDA / NCCL / driver requirements
- Container needs (Singularity, Docker) — typically none for standard PyTorch stacks
- Open-source vs proprietary status of all dependencies
- Special kernel / hardware needs (typically: none)
- GitHub link to the codebase if available

**Review axis:** Software and Resource Requirements

**Pitfall:** Forgetting to explicitly state that everything is pip-installable and uses standard kernels — that's a positive signal staff look for.

---

## 9. Storage & Data (~150-220 words)

**What to write:**
- Dataset description: size, modalities, sources
- Scratch storage estimate (e.g., 100 TB), tied to dataset + checkpoint counts, not a round number out of nowhere
- Intermediate artifacts (processed shards, sequence files, etc.)
- Checkpoint size + save frequency + retention policy + restart support
- Data movement plan (direct download / SCP / rsync / object store)
- I/O intensity expectation (typically: moderate-to-high during preprocessing, compute-bound during training)

**Review axis:** Software and Resource Requirements

**Pitfall:** Round storage numbers without justification. Tie the number to dataset scale + per-checkpoint size × concurrent runs.

---

## 10. Impact and Acknowledgements (~80-140 words)

**What to write:**
- Target venue(s) for publication (named if known, `[TBD]` otherwise)
- Open-source release plans for code, models, datasets
- One-sentence broader scientific impact

**Review axis:** N/A (administrative / supportive)

**Pitfall:** Overclaiming impact. One sentence is enough.

---

## 11. References (as needed)

- Only cite papers that are actually referenced in Scientific Overview or Prior Experience
- IEEE numbered or APA short-form is fine for a 2-4 page doc
- If the project PDF is a paper, the project's own references can be reused for the Scientific Overview citations

---

## Review-axis mapping summary

| Section | Experience | Software & Resources | Computational Readiness | Need for Marlowe |
|---|---|---|---|---|
| Project & PI | — | — | — | — |
| Abstract | — | — | — | ◐ |
| Scientific Overview | — | — | — | ◐ |
| Prior Experience | ● | — | ◐ | — |
| Scaling Studies | — | — | ● | ◐ |
| Computational Profile | — | ● | ◐ | — |
| Justification | — | — | — | ● |
| Technical Requirements | — | ● | — | — |
| Storage & Data | — | ● | — | — |
| Impact & Acks | — | — | — | — |

● = primary, ◐ = secondary, — = none

If a section is weak on its primary axis, address that before adjusting prose.
