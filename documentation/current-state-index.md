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

### AWX-002K corrected NoCloud seed ISO — 2026-11-22

**USER-EXECUTED PASS.** Original AWX-only seed ISO preserved as `seed-ubuntu-awx-01.iso.bak`. Corrected `seed-ubuntu-awx-01.iso` generated with `genisoimage -R -J -V cidata`; `isoinfo -R -f` returned exact `/meta-data` and `/user-data`; `isoinfo -d` confirmed `Volume id: cidata`, Joliet UCS level 3, Rock Ridge version 1. ISO size 374784 bytes. Source files and other seed media unchanged. This verifies ISO structure only; Ubuntu autoinstall not yet tested. Next: create persistent AWX Hyper-V VM with 80 GB OS disk only during installation; PostgreSQL disk to attach later because seed storage match selects smallest disk.

### AWX-003A Hyper-V VM creation — 2026-11-22

**USER-EXECUTED PASS.** Hyper-V host created `ubuntu-awx-01` (Gen 2), 4 vCPUs, static 12884901888-byte/12-GiB memory, connected to `Public Virtual Switch`, static MAC `00155D284014`. Exactly one OS VHDX attached: `E:\\VM Disks\\Ubuntu-AWX-01\\Ubuntu-AWX-01-OS.vhdx` (creation requested 80 GB). DVD drives attached to existing `secureheadway-ubuntu-24.04.5-autoinstall.iso` and corrected `Seed\\seed-ubuntu-awx-01.iso`. Output confirms VM State Off, single OS disk, and PASS markers. PostgreSQL disk NOT attached. Next: read-only firmware, boot order, VM generation, DVD, VHDX capacity and VM settings preboot check; do not start before review.

### AWX-003B / AWX-003B.1 final preboot verification — 2026-11-22

**USER-EXECUTED PASS after correction to verification logic.** AWX-003B verified Gen2, 4 vCPUs, 12 GiB static memory, exactly one 80 GB OS VHDX, correct Public Virtual Switch and MAC, both ISOs, Secure Boot Off. Its final check failed because script incorrectly expected `BootType = DVD`; Hyper-V reports DVD as `BootType = Drive`. AWX-003B.1 confirmed first boot `Device type: DvdDrive` at SCSI controller 0 location 1 pointing to existing `secureheadway-ubuntu-24.04.5-autoinstall.iso`; seed ISO is second DVD at controller 0 location 2; VM remains Off. **Preboot PASS; no firmware correction required.** Next AWX-003C: user-authorized first boot and unattended Ubuntu install, with only 80 GB OS disk attached. VM install and network verification pending.

### AWX-003C first boot and remote login — 2026-11-22

**USER-REPORTED SUCCESS:** Ubuntu installed on `ubuntu-awx-01`; user is logged in remotely from development workstation `172.16.0.14`. This supports first-boot/install and SSH access success but does not independently verify the guest's IP, OS release, disk layout, or services. Next AWX-003D read-only checks on guest for hostname, OS, IPv4/routes/DNS, disk/mounts, SSH service and uptime. PostgreSQL disk not yet attached; no K3s/AWX installation yet.

### AWX-003D Ubuntu installation verification — 2026-11-22

**USER-EXECUTED PASS.** Guest `ubuntu-awx-01` reports Ubuntu 24.04.5 LTS, Linux `6.8.0-151-generic`, Microsoft Hyper-V Gen2 UEFI. `eth0` UP at `172.16.0.17/24`; static default route via `172.16.0.1`; DNS `1.1.1.1`, `8.8.8.8`. Storage: `sda` 80G, `sda1` 1G VFAT `/boot/efi`, `sda2` 78.9G ext4 `/`; `df -h /`: 78G filesystem, 6.8G used, 67G available, 10% usage. `sr0` installer ISO and `sr1` 366K seed ISO remain attached. SSH service `active`, user `deploy`; remote login from workstation `172.16.0.14` confirmed. No 40GB PostgreSQL data disk attached yet. Next: host-side check and create/attach dedicated 40GB VHDX; do not initialize or format guest storage until disk identity is independently verified.

### AWX-004A.2 PostgreSQL VHDX creation and attachment — 2026-11-22

**USER-EXECUTED PASS:** 40GB dynamic VHDX created at `E:\\VM Disks\\Ubuntu-AWX-01\\Ubuntu-AWX-01-PostgreSQL.vhdx`, attached to `ubuntu-awx-01` on SCSI controller 0 location 3. Two disks attached, VM remains Off. Hyper-V reported OS disk at SCSI 0 location 0 with unexpected GUID-suffixed name `Ubuntu-AWX-01-OS_35416DE3-4E80-45C2-9455-C92389B...` (truncated in output). **Investigate whether OS VHDX is checkpoint/differencing chain before boot or any disk operations.** AWX-004B read-only inspection of Get-VHD ParentPath/VhdType and Get-VMSnapshot requested. No PostgreSQL guest partitioning/formatting yet.

### AWX-004B Hyper-V disk chain and automatic checkpoint — 2026-11-22

**USER-EXECUTED PASS (read-only diagnosis).** `ubuntu-awx-01` remains Off. OS disk at SCSI 0:0 is `E:\\VM Disks\\Ubuntu-AWX-01\\Ubuntu-AWX-01-OS_35416DE3-4E80-45C2-9455-C92389BF8424.avhdx`, type Differencing, virtual size 80GB, parent `E:\\VM Disks\\Ubuntu-AWX-01\\Ubuntu-AWX-01-OS.vhdx`. PostgreSQL disk SCSI 0:3 `...\\Ubuntu-AWX-01-PostgreSQL.vhdx` type Dynamic, 40GB, no parent. One `Standard` automatic checkpoint `Automatic Checkpoint - ubuntu-awx-01 - (11/22/2026 - 8:16:37 PM)`; `CheckpointType: Standard`, `AutomaticCheckpointsEnabled: True`. Recommend disable future automatic checkpoints for persistent AWX VM, **preserve existing checkpoint** pending separate explicit decision. Never manually delete .avhdx; no guest PostgreSQL formatting yet.

### AWX-004C disable future automatic checkpoints — 2026-11-22

**USER-EXECUTED PASS.** On Hyper-V host, `Set-VM -Name ubuntu-awx-01 -AutomaticCheckpointsEnabled $false` succeeded and was verified. Existing single Standard automatic checkpoint dated 2026-11-22 20:16:37 was preserved, VM remains Off. OS .avhdx differencing chain remains in place. 40GB PostgreSQL VHDX attached but not initialized. Next: start VM and run read-only guest storage discovery to distinguish existing OS `sda` from newly attached 40GB device; no formatting yet.

### AWX-004D.2 guest PostgreSQL disk discovery — 2026-11-22

**USER-EXECUTED PASS (read-only).** Guest hostname `ubuntu-awx-01`; `/dev/sda` 80 GiB OS disk with GPT, `/dev/sda1` EFI VFAT and `/dev/sda2` ext4 mounted at `/`. New `/dev/sdb` 40 GiB (42949672960 bytes), no partition table or partitions reported by `fdisk -l`, no filesystem signatures in `blkid`, no mounts. `df -hT` confirms OS root `/dev/sda2` 78G, 6.8G used, 67G available; `sr1` cidata ISO still present. Ready for separately gated, explicitly targeted data-disk partitioning/formatting, UUID-based mount; do not touch `/dev/sda`. AWX-004D.1 host start output not supplied, but guest running evidenced by successful SSH commands.

### AWX-004E.1 PostgreSQL data disk partition — 2026-11-22

**USER-EXECUTED PASS.** On `ubuntu-awx-01`, validated `/dev/sdb` is empty 40 GiB disk; `sgdisk --clear --new=1:0:0 --typecode=1:8300 --change-name=1:AWX-PostgreSQL /dev/sdb` completed successfully. `lsblk` now shows `sdb` 40G disk and `sdb1` 40G partition with no filesystem or mountpoint. OS disk `sda` not targeted. Next AWX-004E.2: gated ext4 formatting of `/dev/sdb1` only; mount by UUID separately after verification.

### AWX-004E.2 ext4 formatting — 2026-11-22

**USER-EXECUTED PASS.** `ubuntu-awx-01`: preformat guards verified empty `/dev/sdb1` on 40GiB `/dev/sdb`. `mkfs.ext4 -L AWX-PostgreSQL /dev/sdb1` completed, with UUID `532571bf-d66d-4b59-8d99-2bdaca4e3253`; postcheck `blkid` returned ext4 type and UUID. `lsblk -f` display omitted FSTYPE/UUID columns in supplied output despite `blkid` PASS; independently verify before mounting. Filesystem not yet mounted; next AWX-004E.3 guarded UUID-based `/var/lib/awx-postgresql` mount and `/etc/fstab` entry, with backup and validation.

### AWX-004E.3 persistent PostgreSQL mount — 2026-11-23 UTC

**USER-EXECUTED PASS (pre-reboot).** On `ubuntu-awx-01`, independently verified ext4 UUID `532571bf-d66d-4b59-8d99-2bdaca4e3253` on `/dev/sdb1`. Backed up `/etc/fstab` to `/etc/fstab.awx-20261123-012924.bak`. Added UUID-based ext4 entry for `/var/lib/awx-postgresql`; `findmnt --verify --verbose` reported **0 parse errors, 0 errors, 2 warnings** (pre-existing `/swap.img` file source, systemd reload pending); `systemctl daemon-reload` then executed. `mount` succeeded. `findmnt` confirms `/var/lib/awx-postgresql` mounted from `/dev/sdb1` ext4 with expected UUID and `rw,relatime`; `df -hT`: 40G total, 24K used, 38G available. **Pending:** independent reboot/persistence verification before K3s or AWX Operator deployment. Existing Hyper-V automatic checkpoint preserved; automatic checkpoints disabled.

### AWX-004E.4B post-reboot PostgreSQL storage verification — 2026-11-23

**USER-EXECUTED PASS, storage milestone complete.** After reboot, guest `ubuntu-awx-01` reports OS `/dev/sda` 80G, `sda1` VFAT at `/boot/efi`, `sda2` ext4 at `/`; dedicated `/dev/sdb` 40G with `sdb1` ext4 automatically mounted at `/var/lib/awx-postgresql`. `findmnt` confirms UUID `532571bf-d66d-4b59-8d99-2bdaca4e3253`, ext4, `rw,relatime`; `df -hT` reports 40G size, 24K used, 38G available. All post-reboot checks passed. Next: read-only K3s host prerequisite audit before making installation changes; AWX PostgreSQL PV integration not yet configured.

### AWX-005A K3s host prerequisite audit — 2026-11-23

**USER-EXECUTED AUDIT COMPLETE.** `ubuntu-awx-01` Ubuntu 24.04.5 LTS, kernel 6.8.0-151-generic, Hyper-V Gen2, x86-64, 4 vCPUs, 11GiB usable memory; OS root 67GiB free; PostgreSQL mount 38GiB free; eth0 172.16.0.17/24 UP, default route 172.16.0.1, DNS 1.1.1.1/8.8.8.8 configured. `/swap.img` 4GiB enabled (0 used); `overlay` and `br_netfilter` absent from loaded modules; `net.ipv4.ip_forward=0`; no `k3s`, `kubectl`, or `helm` executable; k3s service inactive. **Pending prerequisites:** disable swap persistently, load overlay and br_netfilter and configure persistence, enable IPv4 forwarding (and required bridge netfilter settings), then re-audit before K3s installation. No changes made by AWX-005A.

### AWX-005B disable swap for K3s — 2026-11-23

**USER-EXECUTED PASS.** `ubuntu-awx-01`: original active `/swap.img` 4G (0 used), active `/etc/fstab` swap entry line 12 confirmed. Backup created `/etc/fstab.awx-swap-20261123-013146.bak`. Executed `swapoff -a`, commented `/swap.img` fstab entry, ran `systemctl daemon-reload`; verified no active swap and no active swap fstab entry. Original swap file preserved. Persistence after reboot not yet separately checked. Next AWX-005C: configure `overlay`, `br_netfilter`, and forwarding sysctls with backup and verification.

### AWX-005C K3s kernel prerequisites — 2026-11-23

**USER-EXECUTED PASS (pre-reboot).** On `ubuntu-awx-01`, `modprobe overlay` and `modprobe br_netfilter` succeeded, verified loaded via `lsmod` and `/sys/module`. Created `/etc/modules-load.d/99-awx-k3s.conf` with `overlay` and `br_netfilter`. Created `/etc/sysctl.d/99-awx-k3s.conf` with `net.ipv4.ip_forward = 1` and `net.bridge.bridge-nf-call-iptables = 1`. `sysctl -p` applied both, and readback verified both equal 1. No preexisting files at these paths. **Pending:** reboot verification of persistent swap-off, modules, sysctls, and PostgreSQL UUID mount before K3s installation.

### AWX-005D.2 K3s prerequisite reboot verification — 2026-11-23

**USER-EXECUTED PASS.** After reboot, `ubuntu-awx-01` hostname verified; no active swap; `overlay` and `br_netfilter` loaded; `net.ipv4.ip_forward=1` and `net.bridge.bridge-nf-call-iptables=1`; PostgreSQL ext4 filesystem mounted persistently at `/var/lib/awx-postgresql` with expected UUID `532571bf-d66d-4b59-8d99-2bdaca4e3253`. `df -hT`: root `/dev/sda2` 78G total/67G available; PostgreSQL `/dev/sdb1` 40G total/38G available. **AWX-005 host prerequisite milestone PASS.** Next AWX-006A: read-only port and container runtime audit before K3s installation; do not touch protected Kubernetes cluster.
