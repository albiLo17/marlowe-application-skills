Computational Suitability Statement (CSS)

**Project and PI Information:**

Project Title: [details]

PI: Jeannette Bohg

Group member SUNet IDs: ALL

**Abstract:**
[details]

**Scientific Overview:**

[details]


**Prior Experience:**

Our team has extensive experience working with SLURM-managed compute clusters across Stanford’s SC infrastructure, Google Cloud Platform, and other heterogeneous environments. We have hands-on experience training large-scale models on diverse, high-volume datasets (e.g., DROID, Open X), and are familiar with distributed multi-GPU workflows required for such workloads. In addition, we have experience provisioning and managing GPU resources in the cloud, including H100 instances for large model fine-tuning. 

Through this experience, we have established robust practices for running multi-GPU workloads, including careful monitoring of GPU utilization, memory usage, and training throughput to ensure efficient hardware use. We routinely profile jobs at early stages and iteratively tune batch sizes, gradient accumulation, and parallelism strategies before scaling to full experiments.

**Scaling Studies:**

A key aspect of our computational requirements is scaling large model parameters—working across the [details] to[details] range—where our training pipelines require [details] datasets processed concurrently in GPU memory, either co-located across multiple GPUs on a single node or distributed across independent nodes. Throughout model fine-tuning, these distributed processes must routinely communicate intermediate gradients and activations to make forward progress. This high inter-GPU communication regime, combined with the need for multiple concurrent [details] GPUs, underscores our need for a reliable, high-performance cluster with fast interconnects, which Marlowe specifically provides.

Our typical jobs require [details] NVIDIA [details] GPUs, with utilization maintained at [details]% and minimal CPU demands (e.g. about [details]% on an [details]). RAM usage typically ranges from [details] GB. Training jobs are fully checkpoint-enabled, with each checkpoint occupying approximately [details] GB, allowing seamless pause and restart.

We demonstrate here efficient GPU utilization in our implementations. Profiling of our model training runs shows consistent high throughput and effective saturation of the available \~[details] GB VRAM on [pdet] GPUs. These metrics confirm our implementations fully leverage the hardware.  Below, we show an logs of an example [details] training run on [details]. The loss curve has reached near convergence after around [details] days of training, and the GPU utilization is high (\~[details]%; we cannot increase batch size further). In the future, for large-scale jobs, we plan to use [details] A100/H100 GPUs for training.

[Insert images]


**Computational Profile:**

Most of our experiments involve training [details]-parameter models on [details] NVIDIA H100 GPUs. A typical job runs for [details] hours on [details] GPUs in a compute-bound setting, while larger pretraining runs scale to multi-node (up to [details] GPUs) with NVLink-enabled communication. We use the already implemented distributed training pipelines for [do we have a pipeline we build from?] to ensure efficient scaling and throughput. During peak phases, we run up to [details] jobs concurrently. 

**Justification for Marlowe:**

[This is the large GPU requirement, we might instead need large number of jobs requirement.] Our rationale for requesting Marlowe stems from the inadequacy of available A100/H100 GPUs resources through Google Cloud Platform and our lab servers, which suffer from high contention. Moreover, our lab servers do not provide H100 GPUs. The dedicated resources and priority scheduling on Marlowe would allow faster experiment turnaround and the stable multi-day runtimes our pretraining jobs require, significantly accelerating our research cycles. Moreover, our workflows will benefit significantly from Marlowe's NVLink-enabled GPUs, which reduce communication overhead for gradient synchronization across devices.  Provisioning equivalent capacity through cloud providers is cost-prohibitive at our scale.

**Technical Requirements & Feasibility:** 

All software dependencies are open-source and pip-installable. Training relies on the [Add framework if possible] framework (publicly available), with standard dependencies including PyTorch, CUDA 12, NCCL, and Hugging Face libraries. No proprietary software, special hardware peripherals, or non-standard kernel configurations are required.

**Storage & Data:** 

Our storage needs primarily consist of scratch space for [dataset details], intermediate processing, and model checkpoints. Data will be sourced from [dataset info], downloaded directly to cluster storage or transferred via SCP/rsync. Intermediate artifacts include [add info]. We checkpoint models regularly during training (\~20 GB per checkpoint), retaining only the most recent and best-performing checkpoints while cleaning up unused data after analysis. Due to [...explain...] and concurrent experiments, we estimate an overall scratch storage requirement of approximately 100 TB.

**Impacts and Acknowledgements**

This project addresses a key barrier in robot learning: [TO Fill] We plan to publish at [conference] and open-source all code, models, and datasets.

**References:**

\[1] 
