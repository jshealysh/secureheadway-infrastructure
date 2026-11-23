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

**User-confirmed reserved** target VM addresses: `ansible-k8s-control-01` = `172.16.0.50` / `00-15-5D-28-40-15`, `ansible-k8s-worker-01` = `172.16.0.51` / `00-15-5D-28-40-16`, `ansible-k8s-worker-02` = `172.16.0.52` / `00-15-5D-28-40-17`. Hyper-V dynamic MAC pool verified as `00-15-5D-00-0B-00`–`00-15-5D-00-0B-FF`, with no overlap. Separate external MetalLB VIP, DNS, TLS identities, inventories, disks, and kubeconfig contexts are required. Add hard destructive-operation guardrails to protect the reference cluster. Validate concurrent Hyper-V capacity. See [Implementation Approach](./SecureHeadway-Ansible-AWX-Implementation-Approach.md#parallel-kubernetes-reference-and-ansible-target-architecture--2026-11-22).

**Status:** Architecture documented; target IP/MAC reservations confirmed by user; no new target VM creation verified. AWX-002 remains pending.

### Ansible target network reservation milestone — 2026-11-22

**RESERVED (user-confirmed):** `ansible-k8s-control-01` = `172.16.0.50` / `00-15-5D-28-40-15`; `ansible-k8s-worker-01` = `172.16.0.51` / `00-15-5D-28-40-16`; `ansible-k8s-worker-02` = `172.16.0.52` / `00-15-5D-28-40-17`. Hyper-V dynamic MAC range checked and non-overlapping. This is a reservation milestone, **not** a deployed/verified Kubernetes target-cluster checkpoint.

### MetalLB VIP allocation decision — 2026-11-22

**User-selected:** reference Kubernetes MetalLB VIP `172.16.0.200`; disposable Ansible target MetalLB VIP `172.16.0.201`. In MetalLB Layer 2 mode, neither VIP has a fixed standalone MAC: the announcing node's NIC MAC answers ARP and may change on failover. Do not create dedicated static MAC reservations for VIPs. Exclude these IPs from DHCP allocations and verify actual MetalLB configuration, ARP behavior, and address availability when deployed. Target VIP selection is an architecture decision, not proof of live configuration.

### DHCP VIP exclusion confirmation — 2026-11-22

User confirmed Netgear RAX50v2 DHCP server at `172.16.0.1` was changed and saved with DHCP ending address `172.16.0.199`. Accordingly, reference MetalLB VIP `172.16.0.200` and Ansible target VIP `172.16.0.201` are outside the DHCP allocation range. **Router setting: user-confirmed PASS.** Starting address not captured; independent address-conflict and reference MetalLB health verification remain pending. MetalLB Layer 2 VIPs do not require separate MAC-based DHCP reservations.

### CI/CD and Grafana mandatory target scope — 2026-11-22

**USER-APPROVED REQUIREMENT / NOT DEPLOYED:** The disposable Ansible Kubernetes target must include application CI/CD (GitHub Actions build/test/publish and controlled deployment of ASP.NET Core Web API and Next.js) and Grafana observability (Grafana, Loki, Alloy, datasource/dashboard provisioning, log-ingestion tests; metrics backend and alerting to be finalized). Roadmap Steps 12 and 13 are mandatory cumulative rebuild and PASS/FAIL acceptance gates. Verify deployment provenance, target-only credentials, application health, rollout/rollback, Grafana queries, persistence, and documentation. The existing reference cluster must remain untouched. See [Implementation Approach](./SecureHeadway-Ansible-AWX-Implementation-Approach.md#cicd-and-grafana-target-scope-approval--2026-11-22). **AWX-002 still pending.**

### Prometheus / Alertmanager scope approval — 2026-11-22

**USER-APPROVED REQUIREMENT / NOT DEPLOYED:** Step 12 on the disposable Ansible Kubernetes target must include Prometheus, Alertmanager, node-exporter and kube-state-metrics, together with Grafana, Loki and Alloy. Proposed deployment uses pinned `kube-prometheus-stack`, one Grafana instance, Longhorn-backed persistent data, provisioned Prometheus/Loki datasources, dashboards, alert routing, and automated PASS/FAIL checks for targets, application metrics, logs, queries, persistence and cumulative rebuild. Initial sizing and 15-day/30-second scrape settings are estimates pending validation, not deployed facts. Existing reference cluster remains protected. **AWX-002 still pending.** See [Implementation Approach](./SecureHeadway-Ansible-AWX-Implementation-Approach.md#prometheus-and-alertmanager-mandatory-observability-decision--2026-11-22).

### Failure investigation / rebuild approval policy — 2026-11-22

**APPROVED OPERATING REQUIREMENT / PLANNING:** When a deployment or verification step fails, **preserve the disposable target and its diagnostic evidence**; do not automatically tear it down or start the next cumulative rebuild. Investigation and in-place experiments may continue across sessions until the root cause is understood. Record hypotheses, commands, outputs, temporary corrections and unresolved issues. Implement the verified fix in Ansible/tests/docs; targeted tests alone do not establish clean-rebuild acceptance. **Every destructive clean rebuild requires explicit user authorization after investigation**, plus target identity safeguards. Only after clean rebuilding Steps 01..N and passing functional, idempotency and documentation gates may the milestone be accepted. Existing manually built reference cluster is never a destructive target. **AWX-002 remains pending.**

### NET-003 MetalLB VIP verification — 2026-11-22

**Executed by user on Hyper-V host `172.16.0.11`; status PARTIAL PASS (read-only).** `172.16.0.200` neighbor table entry: `00-15-5D-28-40-12`, `Reachable`, interface `vEthernet (Public Virtual Switch)`. This matches the recorded reference `k8s-worker-02` MAC and supports active Layer 2 VIP advertisement. `172.16.0.201` neighbor entry: `00-00-00-00-00-00`, `Unreachable`, same interface; no active neighbor resolved at test time, consistent with target cluster not yet deployed. No ICMP response objects were displayed for either VIP. **Not verified:** application/Gateway health on .200, conclusive freedom from IP conflict on .201, actual MetalLB configuration. No changes made. Next: targeted verification of reference Gateway/service and target address availability as needed; AWX-002 remains PENDING.

### AWX-002 CPU and memory approval — 2026-11-22

**USER-APPROVED DESIGN / NOT DEPLOYED:** `ubuntu-awx-01` allocated 4 vCPUs and 12 GiB static RAM, plus approved 80 GB OS/K3s/AWX and 40 GB PostgreSQL disks. Planned Gen 2 Ubuntu Server 24.04 LTS, `Public Virtual Switch`, `172.16.0.17/24`, static MAC `00-15-5D-28-40-14`. Next action is read-only Hyper-V preflight for resources, VM identity, switch, MAC and disk paths before VM creation. **AWX-002 remains PENDING** until provisioning and checks are completed.

### AWX-002B Hyper-V resource/storage preflight — 2026-11-22

**USER-EXECUTED READ-ONLY VERIFICATION: PASS for present resource availability, VM creation still PENDING.** Host `SHEALYLLM` free RAM **87.61 GiB**; `Public Virtual Switch` is **External**; Hyper-V default VM path `C:\\ProgramData\\Microsoft\\Windows\\Hyper-V`, default VHDX path `C:\\ProgramData\\Microsoft\\Windows\\Virtual Hard Disks`; available disk space: `C:` **1650.26 GiB**, `E:` (Data) **3547.31 GiB**, `D:` Ubuntu ISO volume 0 GiB, `F:` cidata 0 GiB. AWX-002A earlier found VM name available and no static MAC collision. Proposed, **not yet user-approved**, AWX disk placement: `E:\\VM Disks\\Ubuntu-AWX-01\\` with separate 80 GB OS and 40 GB PostgreSQL VHDX files. Confirm placement and existing directory/file collisions before creation. Host memory is a point-in-time observation; concurrent-cluster capacity requires later validation.

### AWX-002C existing cloud-init seed inventory — 2026-11-22

**USER-EXECUTED READ-ONLY INVENTORY: PASS for directory/files; source-content review PENDING.** Existing seed directory `E:\\SecureHeadway Tools and ISO Images\\Linux\\Kubernetes\\Seed` exists. Four seed ISOs present: `seed-k8s-control-01-final.iso`, `seed-k8s-worker-01.iso`, `seed-k8s-worker-02.iso`, `seed-ubuntu-pki-01.iso` (roughly 366–370 KiB each). Four corresponding `Source\\<vm>\\meta-data` and `user-data` pairs present. **No AWX-specific seed ISO or source was listed.** Proposed new `Source\\ubuntu-awx-01` plus `seed-ubuntu-awx-01.iso`; do not overwrite existing seed files. Need inspect existing PKI `meta-data`/`user-data` for safe reusable conventions before authoring AWX-specific cloud-init. Do not disclose or commit secrets; review sanitized content only. AWX VM not created.

### AWX-002D/E cloud-init review and ISO tooling — 2026-11-22

**User-executed, read-only.** AWX-002D existing `ubuntu-pki-01` seed source inspected: NoCloud meta-data instance-id/hostname, Ubuntu autoinstall identity `deploy`, SSH server and authorized key, static MAC/IP, gateway, external DNS, packages, security updates and reboot. **Critical finding:** existing `storage.layout.match.size: smallest` would select a 40 GB PostgreSQL disk instead of the intended 80 GB OS disk if both are attached. Plan: attach **only 80 GB OS disk during autoinstall**; add 40 GB PostgreSQL disk afterward with explicit guest mount and PVC validation. Existing password hash was redacted and must not be committed or echoed.

**AWX-002E inventory:** `E:\\SecureHeadway Tools and ISO Images\\Linux\\Kubernetes\\secureheadway-ubuntu-24.04.5-autoinstall.iso` exists; existing four seed ISOs and their source pairs listed. `oscdimg.exe`, `genisoimage.exe`, `mkisofs.exe`, `xorriso.exe` were **not found in Windows PATH**. Seed ISO build method not yet determined; avoid assuming tools are absent or creating replacement media. Next: read-only inspection of installed tools, WSL distributions and any existing seed-generation scripts, without altering reference assets. AWX VM still not created.

### AWX-002F seed ISO tooling discovery — 2026-11-22

**USER-EXECUTED READ-ONLY DISCOVERY:** `wsl.exe --list --verbose` showed Ubuntu WSL2 (default, stopped) and docker-desktop WSL2 (stopped). No `oscdimg.exe` found in inspected Windows ADK/tools paths; no `.ps1`, `.sh`, `.cmd`, `.bat`, or `.py` build scripts found in inspected Kubernetes media tree. **Next:** query existing Ubuntu WSL2 for `xorriso`/`genisoimage`/`mkisofs` and test read access to seed source, without installing packages or modifying existing ISO assets. Starting stopped WSL for inspection is non-destructive but changes its running state. AWX provisioning remains pending.

### AWX-002G cloud-init source creation — 2026-11-22

**USER-EXECUTED: PASS for local AWX source creation and displayed field checks.** Under `E:\\SecureHeadway Tools and ISO Images\\Linux\\Kubernetes\\Seed\\Source\\ubuntu-awx-01`, new `meta-data` and `user-data` were created by copying the existing PKI autoinstall template and replacing identity/network settings. Observed `meta-data`: `instance-id: secureheadway-ubuntu-awx-01`, `local-hostname: ubuntu-awx-01`. Observed filtered `user-data`: hostname `ubuntu-awx-01`, SSH server enabled, MAC `00:15:5d:28:40:14`, static IP `172.16.0.17/24`, `storage.layout.match.size: smallest`. Source-preservation and SSH-copy checks reported PASS. **No full cloud-init schema validation or boot verification yet.** Existing password hash copied locally; do not publish. Only the 80 GB OS disk may be attached during autoinstall. Next: check Ubuntu WSL2 ISO tool availability, then create AWX-specific NoCloud seed ISO without touching existing media. AWX VM remains PENDING.

### AWX-002H.1 / AWX-002H.2 WSL and ISO tooling — 2026-11-22

User-executed read-only checks: **PASS**. WSL `Ubuntu` Running, version 2; `wsl.exe -d Ubuntu -- /bin/bash -c 'echo WSL_COMMAND_SUCCESS'` returned expected text and exit code 0. Direct `which` checks found `/usr/bin/genisoimage`, `/usr/bin/xorriso`, `/usr/bin/mkisofs`, `/usr/bin/cloud-localds`. Original AWX-002H nested-shell output was inconclusive; resolved by simpler command invocation. Next AWX-002I: generate unique `cidata` ISO from existing AWX source, verify ISO label and root file names; no VM created yet.

### AWX-002I seed ISO generation — 2026-11-22

User-executed result: **PARTIAL PASS / filename validation pending**. `seed-ubuntu-awx-01.iso` created (360448 bytes); `isoinfo -d` reported volume id `cidata`. Default `isoinfo -f` displayed ISO9660 names `/META_DAT.;1` and `/USER_DAT.;1`, causing the script's exact-name test to FAIL. This listing alone does not establish whether Rock Ridge names are present; next read-only step is to inspect `isoinfo -R -f` and `isoinfo -d` extension info before deciding whether ISO needs regeneration. No VM created.

### AWX-002J seed ISO filename diagnosis — 2026-11-22

**PASS (diagnostic):** User executed `isoinfo -f`, `isoinfo -R -f`, and extension checks against AWX seed ISO. Both listings show `/META_DAT.;1` and `/USER_DAT.;1`; `isoinfo -d` reports `NO Joliet present`, `NO Rock Ridge present`, volume `cidata`. Current AWX ISO is **NOT ACCEPTED** for NoCloud autoinstall. Next: explicitly authorized repair only of `seed-ubuntu-awx-01.iso`, add Rock Ridge and Joliet and verify exact names. Preserve all existing PKI/Kubernetes seed files and original Ubuntu autoinstall ISO.
