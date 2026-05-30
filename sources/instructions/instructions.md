## Marlowe GPU Project Application – Preparation Guide

Below is a brief guide detailing the information you will need to complete a Medium or Large project application for Marlowe. Please review the following, and take note of the job profile information, storage requirements, and PDF instructions. The application form has more detailed instructions.

**Section 1: Project & PI Information**
Prepare basic project details including project title, abstract, PI name, group member SUNet IDs, and any urgent timelines (e.g., upcoming grant or conference deadlines).

**Section 2: Project PDFs**
a) **Computational Suitability Statement (CSS)**: You will need to upload a 2–4 page PDF describing computational needs, readiness, scalability, and justification for using Marlowe. The 2-4 page PDF for medium projects should focus on computational suitability rather than a full scientific narrative, please include:

- A brief scientific overview for context
- Prior experience on Marlowe or similar GPU-based systems
- Any weak or strong scaling studies (completed or planned)
- Codes and toolchains used (include GitHub links, if available, with instructions on how to run your codes)
- Job profiles: wall time, GPUs/node, concurrency, memory, I/O, and checkpointing
- Computational readiness and tuning status, expected/demonstrated MFU, etc. Provide detail on why your workload requires more than standard lab or cloud resources.
  - Large projects (>10,000 GPU-hours) should provide strong evidence of readiness and scalability.
     

**Section 3: Computational Profile**
Summarize the typical and max job type, wall time, GPU and node usage, concurrency, and usage pattern for this project. This helps assess fit with system capabilities.

**Section 4: Technical Requirements & Feasibility**
List software frameworks, container tools, and any special configuration needs.

**Section 5: Storage & Data**
Estimate scratch storage requirements -- capacity. Indicate how data will be sourced, moved, and stored. Include details on checkpointing (if applicable).

**Section 6: Impact & Acknowledgments**
Briefly describe expected outcomes such as publications or software.

***


## Review Process

**Computational Suitability Review for Medium and Large Projects (Staff)**
Staff will review the CSS for both Medium and Large projects, considering the following areas:

- **Experience**
  Assessment of familiarity with GPU clusters, including past experiences
- **Software and Resource Requirements**
  Review of codes, toolchains, clear instructions for running applications, and job profiles for use of GPUs, CPUs, memory, I/O, and checkpointing
- **Computational Readiness**
  Evaluation of scaling, optimization, computational efficiency (e.g., MFU), and readiness
- **Need for Marlowe**
  Justification for needing GPU resources beyond standard lab or cloud environments, Sherlock, etc.
