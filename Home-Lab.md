# Home Lab

Started six years ago as a way to reach my video library from university.
Still the same lab — now it runs production workloads and my AI stack.

## Fleet

Two machines, nothing hand-configured:

- **Proxmox VE hypervisor** (Ryzen 7 Pro, 64GB ECC) — production VMs and containers, including a production Windows Server VM. Boot on a ZFS NVMe mirror; bulk data on a 4x16TB ZFS RAIDZ1 pool (~64TB raw).
- **Fedora Server** (Ryzen 5, 32GB RAM, AMD GPU with 32GB VRAM) — LLM inference and bulk storage.

An isolated storage fabric links the two; edge networking is UniFi. Out-of-band management and UPS-backed power round it out.

## Everything as code

**OpenTofu** owns the hypervisor layer — repos, certificates, software-defined networking, storage registration, VM/container definitions. **Ansible** owns system state across both hosts — base, hardening, networking, the AI stack, model deployments.

No WebUI clicks, no hand-edited host configs. The repo is the backup: either machine rebuilds from git plus re-downloadable weights.

## Self-hosted AI

The split is load-bearing: GPU-bound inference stays on the Fedora box, RAM-hungry tooling lives in a service container on the hypervisor.

- **Serving:** open-weight models via a llama.cpp ROCm fork, with speculative decoding (~1.9x). On-demand start on first request, idle unload, one model resident on the GPU at a time. A small swap arbiter keeps a base model warm from boot and swaps in alternates only when idle, returning after a quiet window.
- **One gateway:** LiteLLM as the single OpenAI-compatible endpoint. The model registry is derived from the same source of truth as the containers and routes — adding a model is one edit plus two playbook runs.
- **Around it:** trace observability (Langfuse full stack), metasearch, headless browsers for agent tooling, and Caddy in front — all with explicit restart policies and a boot-time start story verified by reboot, not assumed.

## Data and recovery

Every asset is classed before it is backed up:

1. **Reproducible from git** (roles, templates, configs) — no backup needed, re-apply.
2. **Re-downloadable** (model weights, container images, media) — re-fetch, plus a nightly off-machine copy of weights.
3. **Irreplaceable** (databases, trace stores, live state) — the only class that must be backed up, and the current hardening focus.

State-changing applies take snapshots first; rollback and restore runbooks cover the hypervisor config, the AI service stack, and the model store. Load-bearing choices are recorded as short ADRs.

## Operating discipline

Active work lives on an issue tracker. Changes land as small reviewed IaC diffs, each verified with read-only health probes (endpoints, listeners, units) before being called done. Nothing is "configured" — it is either in code or it does not exist.
