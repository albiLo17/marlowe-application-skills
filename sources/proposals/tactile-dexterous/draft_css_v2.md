# SoTa: Soft Tactile Skins for Dexterous Manipulation

**PI:** Jeannette Bohg
**Group member SUNet IDs:** [TBD: SUNet IDs]

## Abstract

Contact-rich dexterous manipulation requires fine-grained tactile feedback that vision alone cannot supply, yet tactile data remains hard to scale because human and robot hands rarely share a common sensor layout that would let demonstrations transfer across embodiments. SoTa addresses this gap with low-cost, custom-fit, layout-aligned tactile skins that place 202 semantically-aligned taxels on both human and robot hands, enabling paired bimanual visuo-tactile demonstrations and cross-embodiment policy co-training. Validating the approach requires training a visuo-tactile Diffusion Policy across multiple data-composition regimes and tasks, sweeping the human-data fraction under a fixed robot-data budget. The full sweep involves seven method variants, ten contact-rich tasks, three-day H100 training jobs, and five iterations — on the order of 3,600 H100 GPU-hours for the core study, with an optional follow-up of similar scale to test non-task-specific in-the-wild human data. Marlowe access enables the multi-day, high-utilization H100 runs and the concurrent ablation campaigns this scaling study requires.

## Scientific Overview

Vision-based imitation learning has driven recent gains in robot manipulation, scaled by large in-the-wild teleoperation datasets [1, 2] and cross-embodiment generalist training [3]. Contact-rich manipulation, however, depends on signals that vision alone supplies sparsely — contact forces, slippage, and surface conformation — and tactile sensing has been shown to fill this gap on multifingered hands [4, 5]. The remaining bottleneck is sourcing tactile data at scale: most tactile datasets are robot-only and embodiment-specific, isolating policy training from the much larger pool of human manipulation behavior that scalable wearable tactile gloves have begun to unlock [6]. Recent attempts to bridge human and robot tactile modalities rely on shared-sensor instruments with mechanical or learned alignment [7, 8], which trade off coverage or alignment fidelity. SoTa addresses this by introducing full-hand tactile skins with a fixed semantic taxel layout shared across human and robot hands, removing the need for a learned tactile alignment module entirely. The resulting dataset contains 770 human and 269 robot demonstrations (1,039 episodes total) across five contact-rich tasks — box closing, cloth folding, pepper placement, cup picking, and board wiping — with synchronized egocentric RGB observations and proprioception at 30 Hz. The compute requirement follows directly from the scientific design: evaluating whether scaled human visuo-tactile data improves robot policy performance requires training the same policy architecture across many data-composition × task combinations, with multiple iterations to draw reliable conclusions.

## Prior Experience

Our team has substantive experience operating SLURM-managed GPU clusters across Stanford research compute infrastructure, Google Cloud A3 instances with H100 GPUs, and lab-managed servers, including multi-day distributed training campaigns. We have run preliminary training on H100-class hardware via GCP, profiling utilization and convergence behavior before scaling up. The DreamZero distributed multi-GPU pipeline that underlies our world / action model line of work is already implemented and has been exercised in profiled training runs, and the SoTa visuo-tactile Diffusion Policy pipeline produces physical-robot evaluation results on two of the five target tasks today (cloth folding and pepper placement). Our day-to-day engineering practice includes early-stage job profiling, iterative tuning of batch size and parallelism strategy, GPU and VRAM utilization monitoring, and retention-aware checkpointing. We are familiar with PyTorch, CUDA 12, NCCL, and the Hugging Face stack, and we are comfortable provisioning, monitoring, and tearing down multi-GPU jobs on managed clusters.

## Scaling Studies

Preliminary profiling of the inherited DreamZero training pipeline, executed on a GCP A3-highgpu-2g instance with NVIDIA H100 GPUs, demonstrated approximately 86% GPU utilization with VRAM saturated near 80 GB per device; the loss approached convergence after roughly three days of wall-clock training [Figure 1]. SoTa-specific profiling on the visuo-tactile Diffusion Policy is still pending [TBD: SoTa-specific scaling profile — utilization, throughput, runtime], but architectural similarity to the profiled pipeline and the already-running physical-robot evaluation suggest a comparable GPU-bound regime.

The planned scaling study is what drives the requested allocation. The core sweep runs one H100 GPU per (method, task) combination with a three-day per-run wall time. Across seven method variants — robot-only at 50 demonstrations, robot-only at 75 demonstrations, human-only, three robot-plus-human mixes at 50 / 100 / 150 human demonstrations, and a noise-tactile robot-only ablation — and ten contact-rich tasks (slightly expanded from the five currently evaluated to support broader generalization claims), one iteration consumes 7 × 10 × 3 days = 720 H100 GPU-hours. To draw reliable conclusions we expect five iterations across data refinement and architectural tuning, totalling 3,600 H100 GPU-hours for the core study. An additional planned experiment, mixing robot data with both task-specific and in-the-wild human data under the same 7 × 10 × 3-day structure, contributes up to 720 H100 GPU-hours per iteration. Larger-scale ablations and the world / action follow-up may extend to multi-node runs of up to 16 GPUs, where NVLink-enabled gradient synchronization is the critical factor.

## Computational Profile

A typical training job uses one NVIDIA H100 GPU per (method, task) combination, running for roughly 72 hours of wall time. Larger ablations and world / action follow-up runs may span 2–8 H100 GPUs per job and, for the most ambitious experiments, extend to multi-node configurations with up to 16 GPUs and NVLink-enabled inter-GPU communication. GPUs per node: [TBD: GPUs per node]. Number of nodes for 16-GPU runs: [TBD: nodes for 16-GPU runs]. Peak concurrency reaches roughly 16 simultaneous jobs during scaling-sweep phases; typical concurrency: [TBD: typical concurrent jobs]. CPU demand is modest, with preliminary profiling on the inherited pipeline showing approximately 3% CPU utilization on a GCP A3-highgpu-2g host; requested CPU cores per job: [TBD: CPU cores per job]. System RAM per job is expected at 64–128 GB. VRAM use targets the full ~80 GB per H100 with utilization in the 80–100% range, consistent with the inherited profiling. Jobs are compute-bound during training and shift to moderate-to-high I/O during dataset preprocessing.

The total allocation request, on H100 GPUs, is approximately 3,600 GPU-hours for the core SoTa scaling study (7 methods × 10 tasks × 3 days × 5 iterations). The optional in-the-wild human-data experiment adds up to 3,600 GPU-hours under the same iteration assumption, for a combined upper bound of 7,200 H100 GPU-hours.

## Justification for Marlowe

Three alternative compute environments fall short of the requested scale. Our lab servers do not provide H100-class GPUs, eliminating them for any experiment that depends on H100 VRAM, throughput, or NVLink. Cloud H100 capacity, including GCP A3, is technically available but cost-prohibitive at the 3,600–7,200 GPU-hour scale we plan, particularly with the requirement to keep many independent multi-day jobs running concurrently during scaling-sweep phases. Shared Stanford partitions accessible to our group exhibit contention that disrupts the multi-day stability our training jobs require, and they do not consistently provide the high-bandwidth multi-GPU communication regime our larger ablations need. Marlowe specifically provides what these alternatives cannot: dedicated H100 access with NVLink-enabled interconnect for gradient synchronization across devices, priority scheduling that supports long-running uninterrupted training, and the concurrent capacity to run our 7 × 10 method-by-task sweep without serialization. The combination of multi-day stability and high inter-GPU bandwidth is the specific bottleneck Marlowe removes.

## Technical Requirements & Feasibility

All software dependencies are open-source and pip-installable. Training relies on the PyTorch ecosystem with CUDA 12 and NCCL for distributed execution, alongside Hugging Face libraries for data and model utilities, and the standard Diffusion Policy training stack. No containerization is required — a standard PyTorch environment on the cluster's H100 partition is sufficient. No proprietary software, special hardware peripherals, or non-standard kernel configurations are needed beyond the H100 GPUs themselves. The SoTa codebase will be open-sourced upon publication; development repository: [TBD: GitHub URL]. Run instructions and reproducibility scripts will be published alongside the dataset.

## Storage & Data

The SoTa dataset comprises synchronized egocentric RGB observations, proprioceptive signals, and bilateral tactile readings at 30 Hz with 202 taxels per hand, currently spanning 1,039 episodes (770 human, 269 robot) across five contact-rich tasks. Total dataset duration is reported inconsistently in the current paper draft (approximately 2.5 hours in the dataset analysis versus approximately 6 hours elsewhere) and will be reconciled before the experimental sweep begins. For the full scaling study we estimate approximately 100 TB of scratch storage, covering raw and processed video, intermediate artifacts (processed video shards, tactile and proprioceptive sequence files, model-ready training representations), and model checkpoints. Data will be sourced from our existing collection pipeline and moved to Marlowe scratch via direct download or SCP / rsync. Checkpoints are approximately 20 GB each and saved on a regular interval during training; the retention policy keeps the most recent and best-performing checkpoints per run and removes intermediate artifacts after analysis. All training jobs are fully checkpoint-enabled and support pause / resume after interruption. I/O is moderate-to-high during dataset preprocessing and primarily compute-bound during the training phase.

## Impact and Acknowledgements

This project addresses a fundamental barrier in robot learning: the absence of scalable cross-embodiment tactile data for contact-rich manipulation. Demonstrating that layout-aligned human visuo-tactile demonstrations improve robot policy performance under a fixed robot-data budget would establish a practical recipe for scaling tactile policy training beyond the bottleneck of robot teleoperation, complementing recent progress on cross-embodiment learning from heterogeneous data sources [3]. We intend to submit the scientific results to a top robot-learning venue (currently targeting CoRL 2026, with the underlying paper [9] already in draft) and to open-source the code, models, and dataset.

## References

[1] A. Khazatsky, K. Pertsch, S. Nair, A. Balakrishna, S. Dasari, S. Nasiriany, et al., "DROID: A Large-Scale In-the-Wild Robot Manipulation Dataset," arXiv:2403.12945, 2024.

[2] Z. Fu, T. Z. Zhao, C. Finn, "Mobile ALOHA: Learning Bimanual Mobile Manipulation with Low-Cost Whole-Body Teleoperation," arXiv:2401.02117, 2024.

[3] Q. Vuong, S. Levine, H. R. Walke, K. Pertsch, A. Singh, R. Doshi, et al., "Open X-Embodiment: Robotic Learning Datasets and RT-X Models," in *Towards Generalist Robots: Learning Paradigms for Scalable Skill Acquisition* Workshop @ CoRL, 2023.

[4] H. Qi, B. Yi, S. Suresh, M. Lambeta, Y. Ma, R. Calandra, J. Malik, "General In-Hand Object Rotation with Vision and Touch," in *Conference on Robot Learning (CoRL)*, 2023.

[5] T. Lin, Y. Zhang, Q. Li, H. Qi, B. Yi, S. Levine, J. Malik, "Learning Visuotactile Skills with Two Multifingered Hands," in *IEEE International Conference on Robotics and Automation (ICRA)*, pp. 5637–5643, 2025.

[6] S. Sundaram, P. Kellnhofer, Y. Li, J.-Y. Zhu, A. Torralba, W. Matusik, "Learning the Signatures of the Human Grasp Using a Scalable Tactile Glove," *Nature*, vol. 569, no. 7758, pp. 698–702, 2019.

[7] J. Yin, H. Qi, Y. Wi, S. Kundu, M. Lambeta, W. Yang, C. Wang, T. Wu, J. Malik, T. Hellebrekers, "OSMO: Open-Source Tactile Glove for Human-to-Robot Skill Transfer," arXiv:2512.08920, 2025.

[8] Y. Wi, J. Yin, E. Xiang, A. Sharma, J. Malik, M. Mukadam, N. Fazeli, T. Hellebrekers, "TactAlign: Human-to-Robot Policy Transfer via Tactile Alignment," arXiv:2602.13579, 2026.

[9] [TBD: Final citation for the CoRL 2026 SoTa paper draft].

---

<!-- MISSING INPUTS

TBDs:
- Project & PI: Group member SUNet IDs
- Scaling Studies: SoTa-specific scaling profile (utilization, throughput, runtime)
- Computational Profile: GPUs per node
- Computational Profile: Number of nodes for 16-GPU runs
- Computational Profile: Typical concurrent jobs
- Computational Profile: Requested CPU cores per job
- Technical Requirements & Feasibility: GitHub repo URL for the SoTa codebase
- References: Final citation for the CoRL 2026 SoTa paper draft (reference [9])

Figure placeholders:
- Figure 1 (Scaling Studies): Inherited DreamZero H100 profiling — GPU utilization curve, ~80 GB VRAM saturation band, and loss-curve convergence at ~3 days. User noted figures are still being gathered.

Citations:
- 8 real citations extracted from the project paper's bibliography (refs [1]–[8]), drawn from: vision-based imitation scaling ([1] DROID, [2] Mobile ALOHA), cross-embodiment policy training ([3] Open X-Embodiment), tactile sensing for multifingered manipulation ([4] In-Hand Rotation with Vision & Touch, [5] Visuotactile Skills with Two Multifingered Hands), scalable human-collected tactile data ([6] STAG / Sundaram et al. Nature 2019), and the two closest related cross-embodiment tactile interfaces ([7] OSMO, [8] TactAlign).
- 1 TBD citation (ref [9]) — the project's own CoRL 2026 draft.
- No papers downloaded to `related_work/` for this draft; the project paper's bibliography provided sufficient context for all 8 cited works.

Open questions for the author:
- Reconcile dataset duration (~2.5 hr in the dataset analysis vs ~6 hr in abstract/intro) before submission.
- Confirm SoTa-specific profiling status: the visuo-tactile Diffusion Policy is operational on 2 of 5 tasks today; will SoTa-specific profiling figures be available before submission, or should Figure 1 remain DreamZero-based?
- Confirm whether the "10 contact-rich tasks" framing is final, vs the 5 tasks currently evaluated in the draft. The GPU-hour math in this draft uses 10 tasks per `computation_details.txt`.
- Confirm urgent timelines (e.g., grant or conference deadlines) — Marlowe asks about these in Section 1 of the application form.
- Confirm PI is Jeannette Bohg (inferred from lab affiliation and template).

-->
