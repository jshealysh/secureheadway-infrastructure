# Current Checkpoint File Index

## Overview

This document is the authoritative index of Secure Headway infrastructure checkpoints.

Each checkpoint preserves:

- Target VM — hostname, IP address, and operating system.
- Actual commands — Bash, PowerShell, or Ansible, exactly as executed.
- Execution results — relevant output, failures, and corrections.
- Verification status — PASS, FAIL, or PENDING.
- Infrastructure changes — configurations, services, and Kubernetes resources.
- Architecture diagrams — Mermaid diagrams when applicable.
- Cloud portability — Hyper-V-specific versus AWS EKS/GKE-portable components.
- Documentation verification — consistency between documented and observed configuration.

ChatGPT will provide both the timestamped checkpoint document and its corresponding Markdown index entry at each checkpoint milestone.

## Current Execution State

| Field                           | Value                         |
| ------------------------------- | ----------------------------- |
| Project                         | Secure Headway Infrastructure |
| Current workstream              | AWX management architecture planning |
| Last completed step             | AWX-001 Hyper-V MAC inventory (PASS) |
| Last verified checkpoint        | To be updated                 |
| Next planned step               | AWX-002 VM provisioning (not started) |
| Outstanding issues              | Implement approved disk layout; verify storage persistence, backup/restore and bootstrap |
| Implementation approach version | AWX decisions dated 2026-11-22 |
| Last updated                    | 2026-11-22 (planning update) |

## Checkpoint Naming Convention

`SecureHeadway_K8S_<Milestone>_YYYY-MM-DD_HHMM.md`

Checkpoints must be preserved permanently. Existing checkpoint files must not be overwritten.

## Status Definitions

- **VERIFIED** — All required checks completed and supporting evidence recorded.
- **PASS** — The specified test or milestone passed its defined acceptance criteria.
- **PARTIAL** — Some work completed successfully; outstanding items remain.
- **PENDING** — Not yet executed or awaiting verification.
- **FAIL** — Verification failed; corrective action required.

## Index of Checkpoint Documents

Append each new checkpoint entry chronologically:

- [Milestone description](./checkpoints/SecureHeadway_K8S_Milestone_YYYY-MM-DD_HHMM.md) — **VERIFIED** — Brief summary of completed work.

## Daily Session Procedure

1. Read the Implementation Approach document.
2. Read the Current Execution State section.
3. Open the latest relevant checkpoint.
4. Confirm the next step and prerequisites.
5. Execute the next numbered step.
6. Capture commands, outputs, failures, and corrections.
7. Perform required verification.
8. Generate the timestamped checkpoint.
9. Provide the corresponding Markdown index entry.
10. Update Current Execution State.

## Cumulative Rebuild Requirements

During the Ansible/AWX automation phase, each completed implementation step must trigger a clean cumulative rebuild.

- Iteration 1: Rebuild and verify Step 1.
- Iteration 2: Rebuild and verify Steps 1–2.
- Iteration 3: Rebuild and verify Steps 1–3.
- Iteration N: Rebuild and verify Steps 1–N.

Each iteration must validate the actual infrastructure and generated Markdown documentation before advancing.

A failed verification prevents acceptance of the milestone.

## Documentation Integrity Rules

- Preserve all historical checkpoints.
- Never silently change previously verified results.
- Record corrections in new checkpoints.
- Ensure every index link resolves to an existing document.
- Maintain chronological checkpoint history.
- Update the current execution state after each milestone.
- Record the Git commit associated with verified rebuilds when available.

## AWX planning decision record — 2026-11-22

**Status: PLANNING / NOT DEPLOYED.** The approved AWX design and GitHub evidence/cost policy are documented in [the Implementation Approach](./SecureHeadway-Ansible-AWX-Implementation-Approach.md#awx-management-platform-decisions--2026-11-22).

- Dedicated persistent `ubuntu-awx-01` on Main LAN `172.16.0.17/24`, static MAC `00-15-5D-28-40-14` (reserved), External Hyper-V switch; no OPNsense involvement in AWX hosting.
- Single-node K3s, AWX Operator, AWX-only PostgreSQL in K3s; proposed 4 vCPU and 12 GiB RAM; **approved** 80 GB OS/K3s/AWX disk plus 40 GB AWX-only PostgreSQL disk (120 GB total).
- Public GitHub repository stores sanitized text-based automation, Markdown/Mermaid documentation, compact PASS/FAIL evidence and cumulative rebuild reports. Detailed logs remain local with bounded retention; avoid large binary artifacts, LFS and metered GitHub storage unless separately approved.
- **AWX-001 PASS:** User-provided read-only Hyper-V inventory showed 11 VMs and 14 NICs. AWX-002 is pending. No AWX installation has been verified.
- This is an architecture decision update, not a VERIFIED infrastructure checkpoint; do not create a fictional timestamped execution checkpoint.

### AWX-002 storage decision — 2026-11-22

**APPROVED DESIGN, NOT YET IMPLEMENTED:** Disk 1 = 80 GB for Ubuntu Server, K3s, AWX containers, execution environments and logs; Disk 2 = 40 GB for AWX-only PostgreSQL persistent data. Total = 120 GB. During provisioning, explicitly configure and verify the PostgreSQL persistent-volume path on Disk 2; attaching a second VHDX alone is insufficient. Record actual device identifiers, mount point, storage class/PV/PVC binding, reboot persistence, and restore prerequisites. AWX-002 remains PENDING until executed and verified.

## Parallel-cluster architecture clarification — 2026-11-22

**APPROVED REQUIREMENT / DESIGN ONLY:** Preserve the manually built reference Kubernetes cluster `k8s-control-01` (`172.16.0.40`), `k8s-worker-01` (`.41`), and `k8s-worker-02` (`.42`) while separately provisioning and repeatedly rebuilding a new Ansible-managed target Kubernetes cluster **in parallel**. This supersedes the earlier non-concurrent assumption **for these two clusters**. Both are development/testing simulations, never live production. AWX stays on persistent `ubuntu-awx-01` (`172.16.0.17`).

Candidate **unreserved** target VM addresses: `ansible-k8s-control-01` = `172.16.0.50`, `ansible-k8s-worker-01` = `.51`, `ansible-k8s-worker-02` = `.52`. Assign unique MACs after verifying Hyper-V pool and reservations. Separate external MetalLB VIP, DNS, TLS identities, inventories, disks, and kubeconfig contexts are required. Add hard destructive-operation guardrails to protect the reference cluster. Validate concurrent Hyper-V capacity. See [Implementation Approach](./SecureHeadway-Ansible-AWX-Implementation-Approach.md#parallel-kubernetes-reference-and-ansible-target-architecture--2026-11-22).

**Status:** Architecture documented; no target IP/MAC reservation or new target VM creation verified. AWX-002 remains pending.
