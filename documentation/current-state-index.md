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

### AWX-006A K3s pre-installation network/runtime audit — 2026-11-23

**USER-EXECUTED PASS (read-only).** `ubuntu-awx-01` eth0 UP at `172.16.0.17/24`. `ss -tulnp` showed only SSH TCP 22, local systemd-resolved DNS TCP/UDP 53, SSH forwarding loopback 6010; no listeners on checked ports 6443, 10250, 2379, 2380, 8472, 51820, 51821, 80, 443. No docker, containerd, crio, ctr, nerdctl, k3s, kubectl, or helm executable. k3s/kubelet/containerd/docker services inactive. UFW inactive (no host firewall changes requested). `getent ahostsv4` resolved `github.com` and `get.k3s.io` successfully (DNS only; HTTPS reachability not yet verified). Next AWX-006B: read-only inspect version-pinned K3s release availability and installer HTTPS before installation; protected reference cluster must remain untouched.

### AWX-006B K3s source audit — partial PASS, version retrieval FAIL

User execution: HTTPS to get.k3s.io and GitHub k3s releases returned HTTP 200. Stable channel request https://update.k3s.io/v1-release/channels/stable followed redirects to GitHub release HTML, not a machine-readable version string; prior script incorrectly printed PASS despite invalid version. No K3s installed. Next AWX-006B.1: inspect redirect effective URL, extract and validate release tag; do not install yet.

### AWX-006B.1 — PASS: K3s stable release verification

Verified on ubuntu-awx-01: channel https://update.k3s.io/v1-release/channels/stable returned HTTP 200, effective URL https://github.com/k3s-io/k3s/releases/tag/v1.37.2+k3s1. Extracted release tag v1.37.2+k3s1 passed strict validation. No K3s installation performed. Next: review pinned K3s single-node installation configuration before execution.

### AWX-006C pinned K3s installation — PARTIAL PASS / node registration pending

User-executed on ubuntu-awx-01: installer selected v1.37.2+k3s1, downloaded sha256sum and binary, reported binary checksum verification, installed /usr/local/bin/k3s and kubectl/crictl/ctr symlinks, created/enabled/started k3s systemd unit. `systemctl is-active k3s` returned active. `k3s --version` returned v1.37.2+k3s1 (f6973cb8), Go 1.26.10. However, `sudo k3s kubectl get nodes -o wide` returned `No resources found`. **Do not mark node readiness PASS or install AWX Operator yet.** Next AWX-006D: read-only diagnosis of node registration and K3s journal, service state, cluster objects, and configuration.

### AWX-006D — PASS: K3s node registration and control plane healthy

User-provided diagnostic output (2026-11-23 01:36 UTC) on ubuntu-awx-01: K3s systemd service active and enabled, Kubernetes /readyz returned `ok`, one node `ubuntu-awx-01` Ready control-plane at 172.16.0.17, Kubernetes v1.37.2+k3s1, containerd 2.3.7-k3s1. kube-system CoreDNS, local-path-provisioner, metrics-server all 1/1 Running; helm-install-gateway-api-crd job Completed. Startup transient readiness warnings for CoreDNS and metrics-server, no evidence of ongoing failure in snapshot. ExecStart verified `--disable traefik --disable servicelb --node-ip 172.16.0.17 --write-kubeconfig-mode 600`. Initial AWX-006C `No resources found` was transient during initialization; no remediation applied. Next: verify storage class, capacity and persistence architecture before AWX Operator installation.

### AWX-006E — PASS: K3s storage inventory and dedicated PostgreSQL mount

User verified on ubuntu-awx-01: default StorageClass `local-path` uses `rancher.io/local-path`, reclaim policy Delete, binding WaitForFirstConsumer, expansion false. `local-path-config` nodePathMap default path `/var/lib/rancher/k3s/storage` (OS disk). No PVs or PVCs exist. Dedicated `/dev/sdb1` ext4 mounted rw at `/var/lib/awx-postgresql`, 40G total / 38G available / 24K used. No storage configuration changes made. Architecture decision proposed: retain default local-path for general workloads, configure a dedicated PostgreSQL-specific provisioner path and non-default StorageClass on /var/lib/awx-postgresql; ensure isolation from default OS storage and explicit AWX database PVC selection. Next AWX-006F: read-only inspect provisioner configuration/RBAC before modifying storage.

### AWX-006F — PASS: inspected local-path provisioner and PostgreSQL mount

User observed on ubuntu-awx-01: local-path-provisioner Deployment 1/1 Available, image rancher/local-path-provisioner:v0.0.37, no container args; default StorageClass local-path provisioner rancher.io/local-path, reclaim Delete, WaitForFirstConsumer, K3s-managed addon local-storage. ConfigMap kube-system/local-path-config nodePathMap default paths only /var/lib/rancher/k3s/storage; setup mkdir/chmod and teardown rm -rf VOL_DIR. /var/lib/awx-postgresql mounted rw from /dev/sdb1 ext4, directory root:root 0755, 38G available; root /dev/sda2 ext4 66G available. Read-only inspection PASS. Plan: do not mutate K3s-managed default local-storage addon; inspect chart/operator support and design isolated PostgreSQL provisioner/storage class with distinct provisioner name and disk-specific path before making changes. No resources changed.

### AWX-006G — PASS: dedicated PostgreSQL disk identity and mount prerequisites

User verified on ubuntu-awx-01: /dev/sdb1 ext4 LABEL AWX-PostgreSQL, filesystem UUID 532571bf-d66d-4b59-8d99-2bdaca4e3253, PARTUUID e7faff63-b0e1-447f-8ae0-fe86eee93ebb. /etc/fstab entry `UUID=532571bf-d66d-4b59-8d99-2bdaca4e3253 /var/lib/awx-postgresql ext4 defaults 0 2`; active mount rw ext4 /dev/sdb1; directory root:root mode 0755. Only StorageClass local-path (default, rancher.io/local-path, Delete, WaitForFirstConsumer), no PVs/PVCs. All read-only checks PASS; no changes. Planned: isolated PostgreSQL storage with non-default StorageClass, Retain policy, disk mount preflight, while leaving K3s-managed default unchanged. Need to verify operator PostgreSQL deployment configuration and choose implementation before applying.

### AWX-006H — PASS: dedicated PostgreSQL storage safety

User executed on ubuntu-awx-01: expected filesystem UUID 532571bf-d66d-4b59-8d99-2bdaca4e3253 exactly matched active findmnt UUID for /var/lib/awx-postgresql; mount /dev/sdb1 ext4 rw,relatime. OS /dev/sda2 ext4 mounted /; distinct source confirmed. OS 78G total 66G free; dedicated PostgreSQL 40G total 38G free. All verification PASS; no changes. Next: determine supported AWX Operator database storage class/PVC configuration and implement isolated PostgreSQL storage using dedicated filesystem, preserving K3s default local-path.

### AWX-006I — PASS: static PostgreSQL PV prerequisites

User verified on ubuntu-awx-01: node Ready control-plane v1.37.2+k3s1 at 172.16.0.17, spec.unschedulable empty (no unschedulable flag), dedicated PostgreSQL mount UUID 532571bf-d66d-4b59-8d99-2bdaca4e3253 matches, proposed StorageClass awx-postgresql absent, proposed PV awx-postgresql-pv absent, no PVCs, K3s default local-path unchanged. All checks PASS. Proposed architecture: static local PV with non-default StorageClass kubernetes.io/no-provisioner, WaitForFirstConsumer, Retain, node affinity ubuntu-awx-01; explicitly bind AWX PostgreSQL claim. Before creation, verify supported AWX Operator PostgreSQL PVC/storage class options and local mount-path safety. No changes yet.

### AWX-006J — PASS: local PostgreSQL volume path inspection

User observed on ubuntu-awx-01: dedicated /var/lib/awx-postgresql mount UUID preflight passed, ext4 rw /dev/sdb1, directory contains only standard ext4 lost+found, no PostgreSQL processes detected, Kubernetes node ubuntu-awx-01, only default local-path StorageClass and no PVs or PVCs. Read-only PASS; no changes. Next: plan static local PV against a dedicated subdirectory rather than filesystem root, and verify supported AWX Operator managed PostgreSQL storageClassName/PVC settings before creation.

### AWX-006K — PASS: PostgreSQL data directory prepared

User executed on ubuntu-awx-01: hostname and dedicated ext4 mount UUID 532571bf-d66d-4b59-8d99-2bdaca4e3253 verified, confirmed /var/lib/awx-postgresql/data absent, created directory using install -d mode 0755 owner root:root, verified directory resides on dedicated /dev/sdb1 filesystem, verified empty. Disk 40G total / 38G available / 28K used. No PostgreSQL or Kubernetes PV/PVC installed. Next: verify AWX Operator PostgreSQL storageClassName/claim capabilities and compatibility before applying static local PV.

### AWX-006L — PASS: GitHub published release discovery (compatibility NOT established)

User ran on ubuntu-awx-01: GitHub API releases/latest HTTP 200; returned ansible/awx-operator tag 2.19.1, release URL https://github.com/ansible/awx-operator/releases/tag/2.19.1, published 2024-07-02T21:53:58Z, draft=false, prerelease=false. K3s node version v1.37.2+k3s1. Discovery PASS only. This does NOT confirm newest tag overall, AWX Operator compatibility with Kubernetes 1.37, or PostgreSQL storage configuration support. No installation/configuration changes. Next AWX-006M: read-only inspect repository tags and versioned AWX Operator PostgreSQL storage documentation before choosing version.

### AWX-006M — PARTIAL PASS: tags and deployment inventory; storage documentation unresolved

User on ubuntu-awx-01 retrieved first 30 ansible/awx-operator repository tags via GitHub API, headed by 2.19.1, 2.19.0, 2.18.0, through 2.1.0. Attempted raw URL for tag 2.19.1 docs/user-guide/advanced-configuration/persisting-data.md returned HTTP 404, so PostgreSQL storage fields and compatibility remain unverified. K3s deployments kube-system coredns, local-path-provisioner, metrics-server all 1/1, no AWX CRDs shown. No changes. Status PARTIAL PASS; next AWX-006N inspect repository tree and versioned CRD/roles directly before choosing PostgreSQL storage configuration.

### AWX-006N — PASS: AWX Operator 2.19.1 repository tree located

User executed on ubuntu-awx-01 read-only GitHub API tree for tag 2.19.1: 299 files, untruncated. Found docs/user-guide/database-configuration.md; config/crd/bases/awx.ansible.com_awxs.yaml; roles/installer/templates/statefulsets/postgres.yaml.j2; other PostgreSQL templates and installer tasks. Repository source paths verified, but exact storage fields and compatibility with K3s v1.37.2+k3s1 not yet established. No Kubernetes or filesystem changes. Next AWX-006O inspect versioned database docs, AWX CRD storage properties, and PostgreSQL StatefulSet template before storage provisioning.

### AWX-006O — PASS: AWX Operator 2.19.1 PostgreSQL storage configuration source inspected

User executed read-only source inspection on ubuntu-awx-01, with successful results: docs/user-guide/database-configuration.md documents postgres_storage_requirements (default 8Gi), postgres_storage_class (defaults to cluster default when unspecified), postgres_data_volume_init; AWX CRD config/crd/bases/awx.ansible.com_awxs.yaml declares those fields; roles/installer/templates/statefulsets/postgres.yaml.j2 uses volumeClaimTemplates ReadWriteOnce, optional storageClassName from postgres_storage_class and requests.storage. postgres_data_volume_init mounts PVC via subPath and handles permissions; postgres_data_path customization not supported with default PostgreSQL 15 image. Proposed CR postgres_storage_class: awx-postgresql, requests.storage: 30Gi, postgres_data_volume_init: true; 30Gi is design proposal, not a default. Source-level feature verification PASS. Compatibility with Kubernetes v1.37.2+k3s1 not yet established. Next: prepare static local PV and non-default StorageClass manifest, inspect via kubectl client-side dry-run before applying; retain policy, node affinity and mount guards. No resources changed.

### AWX-006P — PASS: client-side validation of static PostgreSQL StorageClass and PV

User ran on ubuntu-awx-01: verified dedicated filesystem UUID and data directory; created local ~/awx-postgresql-storage.yaml with non-default StorageClass awx-postgresql (kubernetes.io/no-provisioner, WaitForFirstConsumer, Retain, allowVolumeExpansion false) and static local PV awx-postgresql-pv (30Gi, ReadWriteOnce, Retain, path /var/lib/awx-postgresql/data, nodeAffinity kubernetes.io/hostname ubuntu-awx-01). kubectl apply --dry-run=client --validate=true reported both created (dry run); post-check confirmed neither resource actually exists. PASS for client-side validation only; not applied. Safety gap: local PV does not validate underlying filesystem UUID; verify mount dependency before deployment.

### AWX-006Q — PASS: K3s systemd mount dependency inspection

User verified on ubuntu-awx-01: k3s service active/enabled; FragmentPath /etc/systemd/system/k3s.service, no DropInPaths, RequiresMountsFor empty. Unit currently Wants/After network-online.target, Restart=always, K3s server with traefik/servicelb disabled and node-ip 172.16.0.17. systemd mount unit var-lib-awx\\x2dpostgresql.mount generated from /etc/fstab active (mounted) /dev/sdb1 ext4, UUID 532571bf-d66d-4b59-8d99-2bdaca4e3253, rw. Inspection PASS; no changes. Next: prepare narrowly scoped systemd drop-in RequiresMountsFor=/var/lib/awx-postgresql, inspect syntax before activation; note path dependency does not independently check UUID and cannot protect after mount loss.

### AWX-006R — PASS: K3s mount-dependency drop-in created, not yet activated

User executed on ubuntu-awx-01: verified correct PostgreSQL filesystem UUID and active K3s, confirmed drop-in absent, created /etc/systemd/system/k3s.service.d/10-require-awx-postgresql-mount.conf with [Unit] RequiresMountsFor=/var/lib/awx-postgresql and mode 0644, displayed contents, sudo systemd-analyze verify k3s.service returned no errors, K3s remained active. PASS for creation and syntax verification. No systemctl daemon-reload or K3s restart yet. Next AWX-006S: systemctl daemon-reload (no restart), inspect RequiresMountsFor, Requires and After and mount unit; verify K3s active and Kubernetes Ready. Caveat RequiresMountsFor is a startup ordering/requirement, not independent UUID validation or protection after mount disappears.

### AWX-006S — PARTIAL PASS: systemd dependency activated; verification assertion failed

User on ubuntu-awx-01 verified correct PostgreSQL mount UUID and drop-in, ran systemctl daemon-reload successfully. systemctl show k3s displayed RequiresMountsFor=/var/lib/awx-postgresql and DropInPaths=/etc/systemd/system/k3s.service.d/10-require-awx-postgresql-mount.conf. Requires and After each include escaped/quoted mount unit var-lib-awx\\x2dpostgresql.mount. Script halted at Step 6 due to strict grep comparison of systemd escaped representation, not evidence of a missing dependency. Steps 7-10 not executed; K3s readiness after reload not yet reverified. No K3s restart. Next AWX-006S.1 use systemctl show -p RequiresMountsFor and systemctl list-dependencies --plain --all k3s.service or systemctl show -p Requires/-p After with normalized output, verify mount active and node Ready. No further changes until verified.

### AWX-006S.1 — PASS: corrected K3s PostgreSQL mount dependency verification

User verified on ubuntu-awx-01: systemctl show k3s RequiresMountsFor=/var/lib/awx-postgresql; systemctl list-dependencies k3s.service --all --plain includes var-lib-awx\\x2dpostgresql.mount, dev-sdb1.device and filesystem-check unit for UUID 532571bf-d66d-4b59-8d99-2bdaca4e3253; mount unit active; mounted UUID matches; k3s active; node ubuntu-awx-01 Ready; kube-system coredns/local-path-provisioner/metrics-server each 1/1. Corrected verification PASS; resolves AWX-006S earlier escaped-name assertion failure. No restart or other changes. Next AWX-006T apply previously validated nondefault awx-postgresql StorageClass and static local PV, after rechecking filesystem and resource absence. Caution: RequiresMountsFor enforces startup dependency, not ongoing UUID monitoring or guaranteed failure protection after mount loss.

### AWX-006T — PASS: PostgreSQL static local StorageClass and PersistentVolume created

User executed on ubuntu-awx-01: verified expected UUID 532571bf-d66d-4b59-8d99-2bdaca4e3253, data directory and K3s RequiresMountsFor, revalidated ~/awx-postgresql-storage.yaml with kubectl client dry run, then successfully applied. StorageClass awx-postgresql provisioner kubernetes.io/no-provisioner, reclaim Retain, WaitForFirstConsumer, allowVolumeExpansion false, nondefault. PV awx-postgresql-pv 30Gi RWO Retain storageClass awx-postgresql, local.path /var/lib/awx-postgresql/data, phase Available, no claim. Default K3s local-path remains default (rancher.io/local-path, Delete). PASS. No AWX Operator or PostgreSQL workload installed. Next AWX-006U: read-only post-creation inspect storageClass, PV node affinity and PVC inventory, mount identity and K3s node readiness; then operator deployment planning. Note 30Gi is declared PV capacity not enforced filesystem quota.

### AWX-006U — PASS: independent live Kubernetes PostgreSQL storage verification

User executed on ubuntu-awx-01: confirmed UUID 532571bf-d66d-4b59-8d99-2bdaca4e3253 mounted at /var/lib/awx-postgresql. Live StorageClass awx-postgresql: kubernetes.io/no-provisioner, Retain, WaitForFirstConsumer, allowVolumeExpansion false. Live PV awx-postgresql-pv: 30Gi, ReadWriteOnce, Retain, local.path /var/lib/awx-postgresql/data, node affinity kubernetes.io/hostname In [ubuntu-awx-01], Available; assertions PASS. No PVCs in any namespace. Default local-path StorageClass unchanged. Kubernetes node ubuntu-awx-01 Ready. PASS. PostgreSQL storage foundation verified; no operator or database deployed. Next AWX-007A: read-only operator installation prerequisites and cluster compatibility inspection before deploying AWX Operator 2.19.1 on K3s v1.37.2+k3s1.

### AWX-007A — PASS: read-only AWX Operator installation prerequisite inspection

User executed on ubuntu-awx-01: K3s client/server v1.37.2+k3s1, Kustomize v5.8.1, single node ubuntu-awx-01 Ready control-plane 172.16.0.17 Ubuntu 24.04.5 LTS kernel 6.8.0-151-generic, containerd 2.3.7-k3s1; 4 CPUs, 12247640Ki memory, 110 pod capacity. git/curl installed; make/docker/helm absent. Only default Kubernetes namespaces; no AWX CRDs or deployments. kube-system coredns/local-path-provisioner/metrics-server 1/1. Static PostgreSQL PV awx-postgresql-pv 30Gi RWO Retain Available; awx-postgresql nondefault StorageClass, local-path remains default. git ls-remote tag 2.19.1 reachable at dd37ebd440edf953d822f2c134833a44a8e77532. K3s service active. Inspection PASS, installation readiness conditional on installation method and operator compatibility with Kubernetes 1.37; no changes made. Next AWX-007B: inspect pinned operator release installation manifests and prerequisites using read-only git archive/clone or GitHub source before choosing tools, with no operator deployment.

### AWX-007B — PASS with installation follow-up: pinned operator source inspection

User executed on ubuntu-awx-01: K3s node Ready; git ls-remote tag 2.19.1 resolves dd37ebd440edf953d822f2c134833a44a8e77532; README retrieved (documentation hosted at awx-operator.readthedocs.io); config/default/kustomization.yaml retrieved: namespace awx, namePrefix awx-operator-, resources ../crd, ../rbac, ../manager, manager_auth_proxy_patch.yaml. config/manager/manager.yaml shows development image controller:latest, WATCH_NAMESPACE, resources, pull-secret reference. No AWX deployments present (only healthy kube-system). Read-only inspection PASS. Installation method/image substitution not yet verified; do NOT deploy raw manager manifest with controller:latest. Next AWX-007C inspect tag's release image references, config/manager/kustomization.yaml, installation documentation and examples, with no cluster mutation. K3s 1.37 runtime compatibility remains unverified.

### AWX-007C — PASS: operator release image and official Kustomize installation documented

User-provided output from ubuntu-awx-01: pinned tag 2.19.1 config/manager/kustomization.yaml maps image controller to quay.io/ansible/awx-operator with newTag latest; manager.yaml has controller:latest and IfNotPresent, WATCH_NAMESPACE from metadata.namespace, imagePullSecrets redhat-operators-pull-secret. Makefile IMG defaults quay.io/ansible/awx-operator:$(VERSION), deploy sets image via kustomize. docs/installation/basic-install.md documents local kustomization.yaml with remote resource github.com/ansible/awx-operator/config/default?ref=<tag>, images override newTag <tag>, namespace awx, then kubectl apply -k . GitHub source read PASS; K3s node Ready; no AWX deployments. No installation performed. Plan: pin operator 2.19.1 explicitly, render and inspect manifests before apply, validate registry image availability and Kubernetes API compatibility separately. Do not expose admin passwords or secrets from sample docs.

### AWX-007D — PASS: AWX Operator 2.19.1 registry tag and OCI image index verified

User executed on ubuntu-awx-01: uname -m x86_64; Kubernetes node architecture amd64. Quay API tag ansible/awx-operator:2.19.1 exists, last modified 2024-07-02, manifest list true, 8 child manifests present, index digest sha256:7302e0c8e5a79c11275848b36c63c61d8e7487840164c109b7cda9aa93456901. Registry HEAD returned HTTP/2 200, content-type application/vnd.oci.image.index.v1+json and matching docker-content-digest. K3s node Ready; no AWX deployments. PASS for tag/index existence, NOT YET proof of linux/amd64 child architecture, successful image pull, or compatibility with K3s v1.37.2. No cluster changes. Next AWX-007E read-only GET OCI index and check linux/amd64 platform and digest; avoid exposing secrets.

### AWX-007E — PASS: pinned AWX Operator OCI index includes Linux/AMD64

User output from ubuntu-awx-01: Kubernetes node architecture amd64; GET quay.io/v2/ansible/awx-operator/manifests/2.19.1 returned OCI index application/vnd.oci.image.index.v1+json with linux/amd64 child digest sha256:53ac69ccc5d8f492f92daa9bfee894797a3aa091bc9291c80b333ebc04779a33; other platform entries arm64, s390x, ppc64le and unknown/unknown metadata entries. Pinned image index digest from AWX-007D sha256:7302e0c8e5a79c11275848b36c63c61d8e7487840164c109b7cda9aa93456901. Node Ready; only kube-system deployments, no AWX. PASS for registry architecture check; actual image pull and runtime compatibility on Kubernetes v1.37.2+k3s1 not yet proven. No changes. Next AWX-007F prepare local pinned Kustomize overlay, render without applying, inspect namespace/CRDs/RBAC/deployment/image and any deprecated APIs; do not deploy.

### AWX-007F — PASS: pinned Kustomize operator manifests rendered locally without deployment

User executed on ubuntu-awx-01. Created /home/deploy/awx-operator-install/kustomization.yaml with remote resource github.com/ansible/awx-operator/config/default?ref=2.19.1, namespace awx, immutable image index digest sha256:7302e0c8e5a79c11275848b36c63c61d8e7487840164c109b7cda9aa93456901. sudo k3s kubectl kustomize rendered /home/deploy/awx-operator-install/operator-rendered.yaml (3280 lines, 132705 bytes). Image substitution verified at line 3248. Render includes Namespace awx; 4 CRDs (awxbackups, awxmeshingresses, awxrestores, awxs); ServiceAccount, Roles, ClusterRoles, bindings, ConfigMap, Service, operator Deployment. K3s node Ready before/after; no AWX deployment in cluster. PASS for render only; server-side API acceptance and runtime compatibility with K3s v1.37.2 unverified. No cluster mutation. Next AWX-007G validate rendered manifest locally/client-side and inspect API compatibility, namespace, RBAC, deployment before any apply; avoid relying on server dry-run to prove CRDs can be installed without effects.

### AWX-007G — PARTIAL PASS: client validation succeeds; server dry-run blocked by absent awx namespace

User ran on ubuntu-awx-01: rendered manifest exists; immutable image digest verified. kubectl create --dry-run=client --validate=false -f rendered.yaml -o name recognized 16 resources (namespace, four AWX CRDs, service account, RBAC, ConfigMap, Service, Deployment). kubectl apply --dry-run=server --validate=strict accepted namespace, four CRDs and three cluster-scoped RBAC objects, but eight namespace-scoped objects returned NotFound: namespaces awx not found because server dry-run does not persist the namespace. Script continued as designed and ended. Confirmed awx namespace absent, no AWX deployment, node Ready. PARTIAL PASS (namespace dependency; not evidence of incompatible APIs). No cluster mutations. Next AWX-007H create ONLY empty namespace awx after explicit step authorization, then repeat strict server dry-run and inspect unchanged cluster. Do not apply operator resources yet. K3s 1.37 runtime compatibility remains unverified.

### AWX-007H — PASS: empty awx namespace created; strict server-side operator dry-run accepted

User executed on ubuntu-awx-01: K3s node Ready; verified namespace awx absent, then created only namespace awx (Active). sudo k3s kubectl apply --dry-run=server --validate=strict -f /home/deploy/awx-operator-install/operator-rendered.yaml accepted all 16 rendered resources: namespace, 4 AWX CRDs, ServiceAccount, 2 Roles, 2 ClusterRoles, 2 RoleBindings, 1 ClusterRoleBinding, ConfigMap, Service, Deployment. Namespace produced expected missing last-applied-configuration annotation warning from imperative creation; dry-run reported configured but did not persist patch. No Deployment or Pods in awx; no AWX CRDs installed; node Ready. PASS for API acceptance; no operator deployed. Next AWX-007I install pinned operator manifests using previously validated rendered YAML only, then check rollout and pods/logs; no AWX custom resource creation or PostgreSQL PVC yet. Operator runtime compatibility with K3s 1.37.2 still unproven until rollout.

### AWX-007I — PARTIAL PASS: operator resources installed; rollout blocked by unavailable kube-rbac-proxy image

User executed on ubuntu-awx-01. Applied pinned /home/deploy/awx-operator-install/operator-rendered.yaml. Namespace awx configured (expected last-applied annotation warning); four AWX CRDs created (awxbackups v1beta1, awxmeshingresses v1alpha1, awxrestores v1beta1, awxs v1beta1); ServiceAccount, RBAC, ConfigMap, metrics Service, Deployment created. Operator rollout timed out after 180s; Deployment awx-operator-controller-manager 0/1, Pod 1/2 ImagePullBackOff. Container awx-manager using quay.io/ansible/awx-operator@sha256:7302e0c8e5a79c11275848b36c63c61d8e7487840164c109b7cda9aa93456901 pulled, started and became leader. kube-rbac-proxy image gcr.io/kubebuilder/kube-rbac-proxy:v0.15.0 returned NotFound and ErrImagePull/ImagePullBackOff; missing redhat-operators-pull-secret warning also observed but operator manager pulled successfully. PV awx-postgresql-pv 30Gi RWO Retain remains Available; node Ready. No AWX application custom resource or PostgreSQL PVC created. PARTIAL PASS; next AWX-007J read-only inspect Deployment proxy args/ports/security, source kustomize proxy patch, and alternative image registry metadata, before controlled patch. Do not reinstall operator or change unrelated cluster.

### AWX-007J — PASS: read-only diagnosis isolates operator rollout to missing proxy image

On ubuntu-awx-01, inspected live awx-operator-controller-manager Deployment: kube-rbac-proxy image gcr.io/kubebuilder/kube-rbac-proxy:v0.15.0, args secure-listen 0.0.0.0:8443 upstream http://127.0.0.1:8080/, logtostderr=true, v=0; 8443/TCP, requests 5m/64Mi, limits 500m/128Mi. awx-manager pinned quay.io/ansible/awx-operator@sha256:7302e0c8e5a79c11275848b36c63c61d8e7487840164c109b7cda9aa93456901, health 6789, metrics 127.0.0.1:8080, leader election; ready/running. Original proxy registry HEAD HTTP 404. Alternative quay.io/brancz/kube-rbac-proxy:v0.15.0 HEAD HTTP 200, Docker manifest list digest sha256:2c7b120590cbe9f634f5099f2cbb91d0b668569023a81505ca124a5c437e7663. Pod 1/2 ImagePullBackOff solely proxy; missing redhat-operators-pull-secret warning but manager image pulled. Node Ready. Read-only diagnosis PASS; rollout still blocked. Next AWX-007K read-only check replacement OCI/Docker manifest list includes linux/amd64 before patching and persist future Kustomize patch. No changes performed in AWX-007J.

### AWX-007K — PASS: alternative kube-rbac-proxy OCI index includes Linux/AMD64

Read-only on ubuntu-awx-01. Kubernetes node architecture amd64. quay.io/brancz/kube-rbac-proxy:v0.15.0 returned Docker manifest list with linux/amd64 child sha256:a02db6144ae2a082e799f0c3a4ecd82f28a91dbd9885040221ff904e957ab95a (also arm, arm64, ppc64le, s390x). Registry-reported immutable manifest-list digest from AWX-007J: sha256:2c7b120590cbe9f634f5099f2cbb91d0b668569023a81505ca124a5c437e7663. Operator Deployment remains 0/1; Pod 1/2 ImagePullBackOff for unavailable gcr.io/kubebuilder/kube-rbac-proxy:v0.15.0; node Ready. PASS for architecture check; image pull/runtime not yet verified. Next AWX-007L persist Kustomize image override for proxy with immutable digest, render and inspect without cluster changes; subsequent step will apply controlled correction.

### AWX-007L — PASS: persistent proxy image override rendered; no cluster changes

On ubuntu-awx-01, updated /home/deploy/awx-operator-install/kustomization.yaml to retain AWX operator image quay.io/ansible/awx-operator@sha256:7302e0c8e5a79c11275848b36c63c61d8e7487840164c109b7cda9aa93456901 and override unavailable gcr.io/kubebuilder/kube-rbac-proxy with quay.io/brancz/kube-rbac-proxy@sha256:2c7b120590cbe9f634f5099f2cbb91d0b668569023a81505ca124a5c437e7663. Rendered /home/deploy/awx-operator-install/operator-corrected.yaml successfully. Diff against original operator-rendered.yaml showed exactly one changed line at ~3213 (proxy image reference), no other manifest changes. Verified old image absent from corrected manifest. Live Deployment remains unchanged: proxy still gcr.io/kubebuilder/kube-rbac-proxy:v0.15.0, AWX manager pinned digest. K3s node Ready. PASS, no cluster changes. Next AWX-007M strict server-side dry-run corrected manifest, then apply only if validation succeeds, wait for rollout and inspect events; no AWX custom resource or PostgreSQL PVC.

### AWX-007M — PASS: corrected pinned AWX Operator successfully rolled out

User supplied complete AWX-007M terminal output (2026-11-23 UTC). On ubuntu-awx-01, strict server-side dry-run accepted corrected /home/deploy/awx-operator-install/operator-corrected.yaml. Applied 16 resources; namespace, CRDs, SA, RBAC, ConfigMap and Service largely unchanged; manager Role configured; operator Deployment configured. Verified proxy image quay.io/brancz/kube-rbac-proxy@sha256:2c7b120590cbe9f634f5099f2cbb91d0b668569023a81505ca124a5c437e7663 and AWX manager image quay.io/ansible/awx-operator@sha256:7302e0c8e5a79c11275848b36c63c61d8e7487840164c109b7cda9aa93456901. kubectl rollout status succeeded; Deployment 1/1 available. New Pod awx-operator-controller-manager-5bc888784-tgrkz 2/2 Running, 0 restarts, node ubuntu-awx-01; old Pod awx-operator-controller-manager-5dddf49d-rgjjt terminating and old ReplicaSet scaled to 0. Quay proxy image successfully pulled (~25MB) and started; AWX manager started. Old image pull errors in events refer to previous Pod. awx-postgresql-pv 30Gi RWO Retain Available; node Ready. PASS for operator rollout; no AWX custom resource or PostgreSQL PVC created. Next AWX-007N verify steady-state pods, deployment, CRDs, operator logs and PV before AWX instance provisioning.

### AWX-007N — PASS: AWX Operator steady-state health verified

User executed read-only verification on ubuntu-awx-01 (2026-11-23 UTC). Deployment awx-operator-controller-manager 1/1 available and rolled out. Pod awx-operator-controller-manager-5bc888784-tgrkz 2/2 Running, zero restarts; awx-manager and kube-rbac-proxy ready. Manager logs show watching namespace awx, leader lease acquired 02:05:00Z, controllers for AWX, AWXBackup, AWXRestore and AWXMeshIngress started with four workers each, metrics and health listeners started; no errors in shown log tail. Proxy logs warn deprecated --logtostderr and future removal of insecure listen/certless operation; proxy generated self-signed TLS cert and listening securely on 0.0.0.0:8443. Four AWX CRDs installed. No AWX custom resource yet. StorageClass awx-postgresql no-provisioner Retain WaitForFirstConsumer; static PV awx-postgresql-pv 30Gi RWO Retain Available; no PVCs. K3s node Ready. PASS; operator installation and initial health verification complete. Next AWX-008A inspect AWX CRD schema and operator-supported PostgreSQL/ingress/service fields read-only before authoring AWX instance custom resource. No app/database created.

### AWX-008A — PASS: installed AWX CRD supports planned PostgreSQL storage configuration

Read-only on ubuntu-awx-01. AWX Operator Deployment rolled out. AWX v1beta1 CRD schema includes postgres_storage_class (string), postgres_storage_requirements (object with requests/limits), postgres_data_volume_init (boolean); service_type enum ClusterIP/NodePort/LoadBalancer (case variants); replicas default 1, admin_user default admin; ingress and secret configuration supported. StorageClass awx-postgresql kubernetes.io/no-provisioner Retain WaitForFirstConsumer; PV awx-postgresql-pv 30Gi RWO Retain Available; no PVCs in awx; no AWX custom resource; node Ready. PASS. Next AWX-008B read-only inspect operator source for PostgreSQL PVC template, generated PVC name, selectors, capacity, and pod scheduling behavior before creating AWX custom resource. No app or DB created.

### AWX-008B — PARTIAL PASS: PostgreSQL storage verified; candidate PVC template URL 404

On ubuntu-awx-01, AWX Operator rollout successful. Candidate source URL https://raw.githubusercontent.com/ansible/awx-operator/2.19.1/roles/installer/templates/postgres-pvc.yml.j2 returned HTTP 404; path unverified, no inference of operator defect. Git tag 2.19.1 resolves dd37ebd440edf953d822f2c134833a44a8e77532. StorageClass awx-postgresql provisioner kubernetes.io/no-provisioner, Retain, WaitForFirstConsumer, expansion false. PV awx-postgresql-pv 30Gi RWO Filesystem Retain, local path /var/lib/awx-postgresql/data, nodeAffinity kubernetes.io/hostname=ubuntu-awx-01, Available. No PVCs or AWX CR in namespace awx. Node Ready. PARTIAL PASS due to missing source template inspection. Next AWX-008B.1 read-only inspect pinned git source tree via shallow checkout/archive to locate actual PVC template and report name/storage fields; no cluster changes.

### AWX-008B.1 — PASS: located PostgreSQL storage generation in pinned operator source

Read-only on ubuntu-awx-01. Cloned AWX Operator tag 2.19.1 to temporary directory; verified exact commit dd37ebd440edf953d822f2c134833a44a8e77532. Source inspection located PostgreSQL StatefulSet template roles/installer/templates/statefulsets/postgres.yaml.j2, with postgres_storage_class at lines 159-160 and postgres_storage_requirements.limits.storage / requests.storage at 163-169; defaults roles/installer/defaults/main.yml around line 397; separate storage PVC template roles/installer/templates/storage/persistent.yaml.j2. No standalone postgres-pvc template; prior 404 was wrong path. The source search found PVC kind in backup/restore and storage templates, indicating need to inspect StatefulSet volumeClaimTemplates directly. Operator rollout successful, StorageClass awx-postgresql and static PV awx-postgresql-pv 30Gi RWO Retain Available; no AWX CR or PVC; node Ready. PASS source location, not yet validated generated claim name/access mode. Next AWX-008B.2 inspect exact relevant source lines and default values read-only before authoring AWX CR.

### AWX-008B.2 — PASS: PostgreSQL StatefulSet claim template verified against pinned source

User-provided source inspection from AWX Operator tag 2.19.1 commit dd37ebd440edf953d822f2c134833a44a8e77532. roles/installer/templates/statefulsets/postgres.yaml.j2: StatefulSet name '{{ ansible_operator_meta.name }}-postgres-{{ supported_pg_version }}' (line 6), replicas 1; volumeClaimTemplates metadata.name 'postgres-{{ supported_pg_version }}' (line 155), accessModes ReadWriteOnce (lines 157-158), storageClassName from postgres_storage_class (159-160), resources.requests.storage from postgres_storage_requirements.requests.storage (167-169), optional limits.storage (163-165). Postgres container and optional init container mount claim via subPath. roles/installer/defaults/main.yml:397-399 default postgres storage request 8Gi, postgres_data_volume_init false at 418, postgres_init_container_commands default chown 26:0 /var/lib/pgsql/data at 419-420. roles/installer/templates/storage/persistent.yaml.j2 is for projects persistence, not PostgreSQL. Existing StorageClass awx-postgresql no-provisioner Retain WaitForFirstConsumer, PV awx-postgresql-pv 30Gi RWO Retain Available; no PVC, no AWX CR; operator rolled out. PASS. Plan explicit postgres_storage_class=awx-postgresql and requests.storage=30Gi, postgres_data_volume_init=true; first verify supported_pg_version and effective defaults before creating AWX CR, then dry-run. StatefulSet-created PVC naming depends on resolved PostgreSQL version and instance name; not yet established.

### AWX-008C — PASS: PostgreSQL 15 and StatefulSet PVC naming verified

Read-only inspection on ubuntu-awx-01 of AWX Operator 2.19.1 pinned source commit dd37ebd440edf953d822f2c134833a44a8e77532. roles/installer/vars/main.yml:7 supported_pg_version: 15. roles/installer/defaults/main.yml:260-261 default PostgreSQL image quay.io/sclorg/postgresql-15-c9s:latest (database_configuration.yml may select RELATED_IMAGE_AWX_POSTGRES or user image instead); defaults postgres_data_volume_init false and init commands chown 26:0 /var/lib/pgsql/data, chmod 700 /var/lib/pgsql/data. StatefulSet template name '{{ ansible_operator_meta.name }}-postgres-15', volumeClaimTemplates name postgres-15, RWO, configurable postgres_storage_class and postgres_storage_requirements.requests.storage. If AWX CR metadata.name=awx, predicted StatefulSet awx-postgres-15, Pod awx-postgres-15-0, PVC postgres-15-awx-postgres-15-0 (Kubernetes StatefulSet naming convention); these resources do not exist yet. Operator rollout successful. StorageClass awx-postgresql no-provisioner Retain WaitForFirstConsumer; PV awx-postgresql-pv 30Gi RWO Retain Available. No AWX CR or PVC. PASS. Next step prepare and server-side dry-run AWX CR with explicit ClusterIP, postgres_storage_class awx-postgresql, request 30Gi, postgres_data_volume_init true, optionally postgres_keep_pvc_after_upgrade true; no actual apply yet. Verify planned name and CRD validation.

### AWX-008D — PASS: AWX application manifest created and server-side strict dry-run validated

User executed on ubuntu-awx-01. Created /home/deploy/awx-application/awx.yaml (refused overwrite if already existed) with AWX v1beta1 custom resource metadata.name=awx, namespace=awx; spec service_type ClusterIP, replicas 1, postgres_storage_class awx-postgresql, postgres_storage_requirements.requests.storage 30Gi, postgres_data_volume_init true, postgres_keep_pvc_after_upgrade true. Operator Deployment rollout successful. sudo k3s kubectl apply --dry-run=server --validate=strict -f /home/deploy/awx-application/awx.yaml returned 'awx.awx.ansible.com/awx created (server dry run)'. No AWX CR or PVC was actually created. Dedicated PV awx-postgresql-pv 30Gi RWO Retain Available/unclaimed, StorageClass awx-postgresql; node Ready. PASS: Kubernetes API schema acceptance only, not proof of runtime reconciliation, image pulls, PVC binding or AWX readiness. Next AWX-008E apply exact reviewed manifest, then inspect AWX CR, PVC, PV, pods, events; avoid outputting secret values and do not change unrelated clusters.

### AWX-008E — PARTIAL PASS: AWX CR applied, PostgreSQL running and dedicated PV bound; AWX web/task initializing

User ran on ubuntu-awx-01. Operator rollout successful and AWX CR awx created after strict server-side dry-run. After 30-second initial reconciliation, operator Pod 2/2 Running zero restarts; PostgreSQL StatefulSet awx-postgres-15 1/1 Ready, Pod awx-postgres-15-0 1/1 Running zero restarts; PVC postgres-15-awx-postgres-15-0 Bound to dedicated awx-postgresql-pv 30Gi RWO StorageClass awx-postgresql; PV Bound claim awx/postgres-15-awx-postgres-15-0 Retain. AWX task Deployment 0/1, Pod 0/4 Init:0/2 at age 4 seconds; AWX web Deployment 0/1, Pod 0/3 ContainerCreating at age 5 seconds. Services: awx-service ClusterIP 10.43.152.163:80, PostgreSQL headless service 5432. Events show PostgreSQL image quay.io/sclorg/postgresql-15-c9s:latest pulled and started, AWX quay.io/ansible/awx:24.6.1 being pulled, redis:7 pulled/started. Historical old operator proxy ImagePullBackOff warnings are from replaced pod, not current. Node Ready. PARTIAL PASS because AWX application readiness unverified; no external access configured. Next AWX-008F read-only wait and inspect web/task rollout, Pods, AWX status, PVC and recent events; avoid secrets.

### AWX-008F — PASS: AWX 24.6.1 web/task and PostgreSQL workloads ready

User verification on ubuntu-awx-01: operator deployment rolled out; awx-web deployment 1/1 Available, Pod 3/3 Running zero restarts; awx-task deployment 1/1 Available, Pod 4/4 Running zero restarts; PostgreSQL StatefulSet awx-postgres-15 1/1, Pod 1/1 Running zero restarts; operator Pod 2/2 Running zero restarts. AWX migration Job awx-migration-24.6.1 Completed zero restarts. AWX CR generation 1, version 24.6.1, image quay.io/ansible/awx:24.6.1, conditions Running=True, Failure=False, Successful=False at time of capture (operator reconciliation status should be rechecked; do not misstate Successful=True). Admin username admin and Secret names are reported; secret values not recorded. PVC postgres-15-awx-postgres-15-0 Bound to awx-postgresql-pv 30Gi RWO Retain. Service awx-service ClusterIP 10.43.152.163:80, no external IP. Node Ready. PASS for Kubernetes workload readiness; application HTTP/API functionality and user login still unverified. Next AWX-008G read-only internal HTTP checks through kubectl service port-forward with local curl, AWX CR conditions, endpoints and migration job; do not reveal credentials or expose service publicly.

### AWX-008G — PASS: Internal AWX HTTP and API functional checks

On ubuntu-awx-01, awx-web and awx-task Deployments successfully rolled out. awx-service ClusterIP 10.43.152.163:80 backed by endpoint 10.42.0.9:8052. AWX migration Job awx-migration-24.6.1 Complete 1/1 (65 seconds). Temporary loopback-only kubectl port-forward service/awx-service 18080:80 started; initial curl connection refused during startup then succeeded. GET /api/ returned valid AWX REST API root JSON; GET /api/v2/ping/ returned version 24.6.1, ha False, active_node awx-web-7cf548985-cxt77 (install UUID redacted). GET / returned HTTP 200. AWX CR conditions Failure=False, Running=True reason Successful, Successful=True reason Successful. Deprecated v1 Endpoints informational warning. PASS for internal HTTP/API functionality; no authenticated login or workstation browser access yet. Next AWX-008H verify controlled SSH tunnel prerequisites and connectivity without changing service exposure or disclosing credentials. Do not publish secrets.

### AWX-008H — PARTIAL PASS: SSH service active, enablement check stopped script

On ubuntu-awx-01, hostname check PASS. systemctl is-active ssh returned active; systemctl is-enabled ssh returned disabled (nonzero), causing set -e to terminate script during section 2. No subsequent effective sshd forwarding settings, listener, AWX readiness, EndpointSlice, or conditions were executed; do not infer their status. No configuration changed. Next AWX-008H.1 inspect ssh.service and ssh.socket enablement, active state, unit triggers and effective forwarding settings read-only; decide whether service is socket-activated before enabling anything.

### AWX-008H.1 — PASS: SSH socket activation and TCP forwarding verified

Read-only checks on ubuntu-awx-01: ssh.service active/running but disabled; ssh.socket active/running and enabled, listening on 0.0.0.0:22 and [::]:22, triggering ssh.service. Thus ssh.service disabled is expected socket activation, not an error; no change required. sshd effective settings: pubkeyauthentication yes, gatewayports no, allowtcpforwarding yes, disableforwarding no, permitopen any. ss confirms port 22 listeners. SSH logs show successful deploy account ED25519 public-key logins from Windows workstation 172.16.0.14 (fingerprint omitted). PASS. Next step Windows PowerShell localhost SSH tunnel to ubuntu-awx-01 (172.16.0.17) with local port 18080; AWX Kubernetes service remains ClusterIP. Need verify key path before assuming it is the staging key; avoid putting credentials in public documentation.

### AWX-008I — PARTIAL PASS: Windows SSH tunnel blocked by public-key authentication

Windows workstation 172.16.0.14: OpenSSH client located at C:\\WINDOWS\\System32\\OpenSSH\\ssh.exe; local TCP 18080 available. Attempted localhost-only SSH local forwarding 127.0.0.1:18080 to AWX service 10.43.152.163:80 via deploy@172.16.0.17. First-use ED25519 host-key prompt accepted and host recorded in known_hosts without out-of-band fingerprint verification; observed fingerprint SHA256:llc/QNclZ6QYc/uZ/OMFCNqRINhGNPiM4dBAEdWYfu4 (public host fingerprint, not a credential). SSH authentication failed: Permission denied (publickey). Tunnel not established. Next step: verify ED25519 host-key fingerprint via existing authenticated SSH session/Hyper-V console and identify correct Windows private key before retry. No SSH server or Kubernetes changes.

### AWX-008I.1A — PASS: SSH host identity fingerprint matched

User ran ssh-keygen -lf /etc/ssh/ssh_host_ed25519_key.pub -E sha256 on ubuntu-awx-01 and reported ED25519 SHA256:llc/QNclZ6QYc/uZ/OMFCNqRINhGNPiM4dBAEdWYfu4. This exactly matches the Windows first-connect SSH host-key prompt from AWX-008I.1. Host identity verified assuming the command ran in trusted existing session or Hyper-V console. No host key changes required. AWX-008I.1B Windows key/agent inventory remains pending; tunnel not yet established.

### AWX-008I.1B — PASS: Windows SSH key inventory; agent unavailable

Windows PowerShell output lists private-key filenames C:\\Users\\johns\\.ssh\\id_ed25519_k8s and C:\\Users\\johns\\.ssh\\secureheadway_staging. ssh-add -l reports Error connecting to agent: No such file or directory. SSH agent service listing contained name ssh-agent but its status/start type were not clearly visible in pasted table; do not infer start type. Direct ssh -i key selection is available without agent. No key contents or secret material published. Next AWX-008I.2 test public-key authentication against deploy@172.16.0.17 using explicit key files, BatchMode, IdentitiesOnly, no remote commands other than harmless hostname, and no port forwarding yet. AWX tunnel still pending.

### AWX-008I.2 — BLOCKED: SSH host-key trust lookup fails before key authentication

On Windows workstation, both private key files id_ed25519_k8s and secureheadway_staging exist. Each explicit ssh -i -o IdentitiesOnly=yes -o BatchMode=yes -o StrictHostKeyChecking=yes deploy@172.16.0.17 hostname attempt stopped with 'No ED25519 host key is known for 172.16.0.17' and 'Host key verification failed'. Therefore neither key was actually tested for server authorization; do not classify them unauthorized. Earlier AWX-008I first-connect prompt had reported adding host to known_hosts and AWX-008I.1A independently verified host ED25519 SHA256:llc/QNclZ6QYc/uZ/OMFCNqRINhGNPiM4dBAEdWYfu4. Next AWX-008I.3 read-only inspect Windows ssh -G effective userknownhostsfile, hostname, identityfile, SSH config, and ssh-keygen -F known-hosts entries; no automatic acceptance or bypass of host key checking. Tunnel not established.

### AWX-008I.3 — PARTIAL PASS: workstation host trust established; staging key rejected

User clarified first AWX host-key acceptance occurred on Hyper-V host 172.16.0.11, while later key tests occurred on Windows development workstation 172.16.0.14, explaining distinct known_hosts trust. On Windows 172.16.0.14 (INTEL_I9_JAS), private key secureheadway_staging exists. SSH connection to deploy@172.16.0.17 prompted with ED25519 SHA256:llc/QNclZ6QYc/uZ/OMFCNqRINhGNPiM4dBAEdWYfu4, independently verified previously; user accepted and host added to workstation known_hosts. Explicit -i secureheadway_staging -o IdentitiesOnly=yes authentication returned Permission denied (publickey). Therefore workstation SSH host trust PASS, staging key authentication FAIL. Next test id_ed25519_k8s with strict host key checking and explicit key, read-only; no server changes or secrets published. SSH tunnel still not established.

### AWX-008I.4 — PASS: Windows OpenSSH authenticates using MobaXterm key

On Windows development workstation 172.16.0.14, C:\\Users\\johns\\.ssh\\id_ed25519_k8s exists. Interactive ssh.exe -i <key> -o IdentitiesOnly=yes -o StrictHostKeyChecking=yes deploy@172.16.0.17 hostname prompted for private-key passphrase, returned ubuntu-awx-01, exit code 0. Confirms successful deploy SSH authentication using same key configured in working MobaXterm session. Earlier BatchMode=yes prevented interactive passphrase entry, explaining failure of key test. Host ED25519 fingerprint previously independently verified and workstation known_hosts populated. No SSH server changes, new keys or agent configuration necessary. Next AWX-008J open SSH tunnel with explicit key and interactive passphrase; verify from separate PowerShell terminal that localhost:18080 serves AWX. Tunnel not yet confirmed.

### AWX-008J — PARTIAL PASS: SSH tunnel reportedly remains connected

User answered yes to whether Windows PowerShell SSH local-forward command stayed connected without an error. Explicit passphrase-protected key C:\\Users\\johns\\.ssh\\id_ed25519_k8s and verified host identity used for SSH forwarding from Windows 127.0.0.1:18080 via deploy@172.16.0.17 to AWX ClusterIP 10.43.152.163:80. Report suggests tunnel process remains open, but no client-side HTTP request or forwarded traffic has yet been verified. Do not claim tunnel end-to-end functional until localhost HTTP/API tests. Next AWX-008K read-only Windows PowerShell test TCP localhost:18080 and HTTP GET / and /api/v2/ping/ with short timeout; redact install UUID. Keep tunnel PowerShell window open.

### AWX-008K — PASS: Windows-to-AWX SSH tunnel and HTTP/API connectivity verified

Windows workstation 172.16.0.14, with an established SSH local-forward tunnel to ubuntu-awx-01 172.16.0.17, verified Test-NetConnection 127.0.0.1:18080 TcpTestSucceeded=True. Invoke-WebRequest http://127.0.0.1:18080/ returned HTTP 200. Invoke-RestMethod http://127.0.0.1:18080/api/v2/ping/ returned AWX version 24.6.1, ha False (expected single instance). PASS: AWX service reachable end-to-end from Windows via encrypted SSH tunnel without public exposure or Kubernetes service changes. Browser visual login page and authenticated administrator login have not yet been verified. Next AWX-008L retrieve initial AWX admin password securely on ubuntu-awx-01 without printing it into chat, logs or public GitHub; verify browser login manually. SSH tunnel must remain running.

### AWX-008L — PASS: Administrator login verified; initial installation milestone complete

User confirmed successful interactive administrator login to AWX web interface from Windows workstation via established localhost SSH tunnel. Previous AWX-008K confirmed local TCP 127.0.0.1:18080, HTTP 200, AWX REST API v24.6.1. AWX-008L administrator login success is user-confirmed; secret password value was not shared, logged, or stored in public documentation. AWX installation milestone: dedicated ubuntu-awx-01, K3s, AWX Operator 2.19.1, AWX 24.6.1 web/task, PostgreSQL 15 with persistent dedicated PV, migration job, internal API, SSH tunnel and administrator login verified. AWX remains Kubernetes ClusterIP, not exposed publicly. Pending future validation: AWX project/inventory/credentials and job execution, GitHub integration, restart persistence, and operational hardening. Do not mark these as completed.

### AWX-009A — PASS: Pre-restart health and evidence of successful recent startup

User output UTC 2026-11-23 22:53:57 on ubuntu-awx-01: uptime 2 minutes; K3s service active and enabled; node Ready v1.37.2+k3s1 IP 172.16.0.17. AWX Operator, web, task deployments each 1/1 available; PostgreSQL StatefulSet 1/1; operator Pod 2/2, postgres Pod 1/1, task Pod 4/4, web Pod 3/3 all Running. AWX migration Pod Completed. Pods ~20h old despite 2-minute uptime, consistent with recovery across VM reboot; restart counters present, precise restart causes not yet established. PostgreSQL PVC postgres-15-awx-postgres-15-0 Bound to local PV awx-postgresql-pv 30Gi RWO Retain. /dev/sdb1 ext4 mounted rw at /var/lib/awx-postgresql; k3s systemd RequiresMountsFor=/var/lib/awx-postgresql and Requires/After mount unit verified. AWX CR version 24.6.1 conditions Failure=False, Running=True reason Successful, Successful=True reason Successful. awx-service ClusterIP 10.43.152.163:80; GET /api/v2/ping/ returned version 24.6.1, ha False. Host /var/lib/awx-postgresql and /var/lib/awx-postgresql/data show root:root mode 755; do not alter permissions without inspecting actual nested PGDATA and pod volume mount. All 15 checks completed. PASS for observed health; persistent DB data integrity and startup sequence require further read-only checks. No restart executed as part of AWX-009A.

### AWX-009B — PASS: PostgreSQL persistent storage mapping and ownership verified

On ubuntu-awx-01, /dev/sdb1 ext4 mounted rw at /var/lib/awx-postgresql. Host directory /var/lib/awx-postgresql/data root:root mode 755; nested /var/lib/awx-postgresql/data/data owner UID 26:root mode 700; actual nested userdata and PostgreSQL subdirectories UID 26, mode 700. PostgreSQL Pod awx-postgres-15-0 Ready; pg_isready reports /var/run/postgresql:5432 accepting connections. postgres, logger, checkpointer, background writer, walwriter, autovacuum and logical replication processes present; AWX task/web client connections visible. Inside container /var/lib/pgsql/data owned postgres:root mode 700 and /var/lib/pgsql/data/userdata owned postgres:postgres mode 700. PostgreSQL container mounts volume postgres-15 at /var/lib/pgsql/data, referencing PVC postgres-15-awx-postgres-15-0; PVC Bound to dedicated PV awx-postgresql-pv 30Gi RWO. Host paths and container mounts are consistent with persistent local volume mapping. PASS for storage path, permissions, readiness and database connection acceptance. Do not alter permissions. Logical AWX database tables/records and integrity have not yet been verified; next AWX-009C read-only schema and row-count checks without credentials or sensitive data output.

### AWX-009C — PASS: AWX PostgreSQL schema and persisted record verification

On ubuntu-awx-01, PostgreSQL Pod awx-postgres-15-0 Ready; local psql connected to database awx as user awx, PostgreSQL server version 15.19. Public schema contains 170 base tables. All six checked core tables exist: auth_user, django_migrations, main_inventory, main_job, main_organization, main_project. Aggregate record counts: users=1, organizations=1, inventories=1, projects=1, jobs=0, migrations=294. Database size 22 MB. All seven scripted checks completed with no SQL errors. No secrets or individual user records exposed. PASS for database availability and expected schema/records after recent VM startup; controlled before/after reboot comparison and workload readiness following deliberate reboot remain to be tested.

### AWX-009D — PARTIAL PASS: PostgreSQL fingerprint not captured; stdin forwarding correction required

Target ubuntu-awx-01 verified. PostgreSQL PVC postgres-15-awx-postgres-15-0 remained Bound to awx-postgresql-pv 30Gi RWO; /dev/sdb1 ext4 mounted rw at /var/lib/awx-postgresql. Fingerprint section printed no results because kubectl exec invocation omitted -i while passing psql SQL via heredoc; without stdin forwarding, psql received no statements and returned successfully. No database baseline captured, no controlled restart authorized. Correct with kubectl exec -i and repeat AWX-009D; verify expected fields are present before any restart. No secrets captured.

### AWX-009D.1 — PASS: Validated pre-restart PostgreSQL fingerprint

On ubuntu-awx-01, corrected kubectl exec -i successfully forwarded read-only SQL to PostgreSQL. Captured and individually validated all 10 fingerprint fields: database=awx; postgres_version=15.19; system_identifier=7711184318677762075; table_count=170; users=1; organizations=1; inventories=1; projects=1; jobs=0; migrations=294. All 10 field-presence checks PASS. PVC postgres-15-awx-postgres-15-0 Bound to awx-postgresql-pv 30Gi RWO. Dedicated /dev/sdb1 ext4 mounted rw at /var/lib/awx-postgresql. No secrets or individual account details recorded. Baseline established for controlled restart comparison; reboot not yet executed at this checkpoint.

### AWX-009E.2 — PASS: Controlled VM reboot and automatic AWX service recovery

User confirmed reboot of ubuntu-awx-01; post-reboot script observed boot time 2026-11-23 22:59:28 UTC and uptime ~1 minute. K3s active and enabled; Kubernetes node Ready v1.37.2+k3s1. Deployments awx-operator-controller-manager, awx-task and awx-web each 1/1 ready and available; PostgreSQL StatefulSet awx-postgres-15 1/1 ready. Pods operator 2/2, postgres 1/1, task 4/4, web 3/3 all Running, with restart counters incremented and recent restart times. Previous migration job remains Completed. /dev/sdb1 ext4 mounted rw at /var/lib/awx-postgresql; PVC postgres-15-awx-postgres-15-0 remains Bound to awx-postgresql-pv 30Gi RWO. AWX CR conditions Failure=False, Running=True, Successful=True. AWX API /api/v2/ping/ responded version 24.6.1, ha=False. PASS for automatic service recovery and storage binding after deliberate reboot. Final PostgreSQL system identifier and aggregate record fingerprint comparison against AWX-009D.1 remains pending; no database continuity claim beyond verified mount/PVC until that comparison.

### AWX-009F — PASS: Post-restart PostgreSQL fingerprint matches baseline; AWX persistence/recovery milestone COMPLETE

After the controlled ubuntu-awx-01 restart verified in AWX-009E.2, read-only SQL against PostgreSQL returned the exact AWX-009D.1 baseline: database=awx; postgres_version=15.19; system_identifier=7711184318677762075; table_count=170; users=1; organizations=1; inventories=1; projects=1; jobs=0; migrations=294. Script's strict string comparison reported PASS. Dedicated /dev/sdb1 ext4 remained mounted rw at /var/lib/awx-postgresql. Together with AWX-009E.2 (K3s active/enabled, node Ready, AWX Operator/Web/Task and PostgreSQL ready, AWX reconciliation successful, AWX API v24.6.1 responding), this completes the controlled VM restart and persistent database recovery milestone. No backups, restore, or disaster recovery tests performed. Note: record-count parity is not a row-level integrity check; system identifier establishes same PostgreSQL cluster.

### AWX-010A.1 — PARTIAL PASS: Backend healthy; Organizations browser error unresolved

On ubuntu-awx-01 all AWX deployments operator/task/web 1/1 available; PostgreSQL StatefulSet 1/1 and operator/postgres/task/web Pods running. GET /api/v2/ping/ without authentication returned HTTP 200. GET /api/v2/organizations/ without authentication returned HTTP 401, expected for protected API. Web logs explicitly identify AnonymousUser and curl/8.5.0 for this request; these are not evidence of a browser-side or backend fault. User's authenticated AWX Organizations UI displayed 'Something went wrong' and 'There was an error loading this content'; root cause remains undetermined. Next step: inspect UI error Details and browser request status without exposing cookies/tokens. No AWX configuration changes made.

### AWX-010A.2–010A.4 — PASS: AWX Organizations UI recovered after MobaXterm tunnel restart

Browser initially showed Organizations 'Something went wrong' with AxiosError: Network Error. Following the VM reboot, Edge later displayed 127.0.0.1:18080 ERR_CONNECTION_REFUSED, indicating no listener on the Windows local forwarded port. User restarted MobaXterm SSH tunnel; subsequent screenshot confirms AWX Organizations page loaded successfully at http://127.0.0.1:18080, logged in as admin. Existing organization 'Default' listed with 0 members and 0 teams. User-confirmed tunnel restart resolved current browser connection failure; a separate AWX backend defect was not established. No organization changes made. Next: inspect existing Default organization before deciding whether to rename or create an organization. No secrets recorded.

### AWX-010B — PASS: Inspected existing Default organization

User-provided AWX UI screenshot confirms organization 'Default' exists, created 2026-11-22 21:15:12 (UI-displayed time) by admin, last modified same displayed time by admin. Organization Details shows an associated Galaxy API Token credential label (truncated in UI; no credential value inspected), with Access, Teams, Execution Environments, Notifications tabs. Previous list showed 0 members and 0 teams. No changes made. Decision: preserve Default and its Galaxy credential unchanged; create separate organization 'Secure Headway' with description 'Secure Headway Infrastructure Automation' for dedicated automation resources. Creation not yet executed or verified.

### AWX-010C — PASS: Secure Headway organization created

User screenshot of AWX Organization Details confirms organization name 'Secure Headway', description 'Secure Headway Infrastructure Automation', created and last modified 2026-11-23 18:20:41 (AWX UI local time) by admin. Galaxy Credentials displays a truncated 'Galaxy API Token: An...' entry; no secret value inspected or recorded. Organization's Execution Environments tab is available, but no specific EE selection is evidenced in the screenshot. Existing Default organization was intentionally preserved. Next step: inspect current AWX inventories before creating dedicated Secure Headway inventory, avoiding duplicates.

### AWX-010D — PASS: Existing inventories inspected

User screenshot of AWX Resources > Inventories shows exactly one inventory: 'Demo Inventory', Type=Inventory, Organization=Default, Sync Status=Disabled. No inventory belonging to the Secure Headway organization is shown. Decision: preserve Demo Inventory unchanged; create a separate dedicated inventory for Secure Headway. No changes made during inspection.

### AWX-010E — PASS: Secure Headway Infrastructure inventory created

AWX inventory Details screenshot verifies Name='Secure Headway Infrastructure', Organization='Secure Headway', Type='Inventory', Description='Managed infrastructure for Secure Headway automated provisioning and verification', Total hosts=0, Variables YAML empty ('---'), created and last modified 2026-11-23 18:24:31 (AWX UI local time) by admin. Dedicated inventory created without altering Default organization's Demo Inventory. Next step: inspect Groups tab and establish non-destructive group hierarchy, keeping AWX controller and existing disposable test Kubernetes nodes out of automated targeting until explicitly authorized.

### AWX-010F — PASS: Inventory groups initial state verified

User confirmed the 'Groups' tab for 'Secure Headway Infrastructure' inventory contains no groups. This is a user-reported observation (no screenshot attached for this step). No groups or hosts have been added yet. Next AWX-010G: create first empty group ubuntu_servers; do not add or target any hosts, including AWX controller or existing disposable Kubernetes test nodes.

### AWX-010G — PASS (user-confirmed): First inventory group created

User confirmed that group 'ubuntu_servers' appears in the 'Secure Headway Infrastructure' inventory after the AWX-010G creation step. Intended group description: 'Ubuntu servers explicitly authorized for Secure Headway infrastructure automation'; exact saved description and group variables not independently inspected. No hosts or child groups were requested to be added. Next step: inspect group details to confirm description, variables, and host count before expanding inventory hierarchy.

### AWX-010H — PASS (user-confirmed): ubuntu_servers group empty and non-targeting

User confirmed in AWX UI that group 'ubuntu_servers' exists in 'Secure Headway Infrastructure', has zero hosts, zero related/child groups, and empty Variables ('---'). Exact saved description has not been independently verified. No hosts were added, and no automation targets have been authorized. Next step: inspect existing AWX Projects before configuring GitHub SCM integration, preserving demo resources and avoiding duplicates.

### AWX-010I — PASS: Existing AWX projects inspected

User screenshot shows one project, Demo Project, status Successful, type Git, revision 347e44f. Organization not displayed in list. Preserve existing project unchanged. Next AWX-010J: create dedicated Secure Headway project using public HTTPS Git repository https://github.com/jshealysh/secureheadway-infrastructure.git, branch main, without SCM credentials. Verify synchronization separately. No hosts or jobs targeted.

### AWX-010J — PASS: Secure Headway GitHub project created and synchronized

AWX project Details screenshot confirms Secure Headway Infrastructure Automation, organization Secure Headway, Git URL https://github.com/jshealysh/secureheadway-infrastructure.git, branch main, successful last job, revision d8e6278 matching AWX-010I documentation commit, cache timeout 0 seconds, project base path /var/lib/awx/projects, playbook directory _8__secure_headway_infrastructure_automation. Enabled options discard local changes before syncing and update revision on job launch. Created 2026-11-23 18:33:57 UI local time by admin. Public repository synchronization verified; no provisioning jobs or infrastructure hosts targeted. Next inspect available execution environments before selecting one for a safe controller-only verification job.

### AWX-010K — PASS: Existing execution environments inspected

User screenshot confirms three globally available execution environments: AWX EE (24.6.1), quay.io/ansible/awx-ee:24.6.1; AWX EE (latest), quay.io/ansible/awx-ee:latest; Control Plane Execution Environment, quay.io/ansible/awx-ee:24.6.1. Select AWX EE (24.6.1) for initial verification jobs; preserve existing EE definitions and control plane configuration. Version tag is not an immutable digest. Future dedicated Secure Headway EE will use version-controlled dependencies and pinned image digest. No job execution verified in this step. Next assign organization default EE and verify saved configuration.

### AWX-010L — PASS: Organization default execution environment configured

User screenshot confirms Secure Headway organization default execution environment AWX EE (24.6.1), description unchanged, Galaxy credential label still displayed, last modified 2026-11-23 18:38:00 UI local time by admin. Initial EE configuration verified; execution of a Secure Headway playbook remains pending. Next prepare an isolated localhost-only smoke test in the execution environment, without SSH credentials or managed infrastructure targets.

### AWX-010M — PASS: Smoke test playbook committed and reviewed

Retrieved ansible/playbooks/awx-smoke-test.yml from GitHub, blob SHA d2cb19fc17ebb6d959fcf9f3a501c74b4356ac11. Static review confirms localhost-only, connection local, gather_facts false, BEGIN/END markers, Ansible version reporting, python3 --version with changed_when false, and no infrastructure configuration changes or SSH credential requirement. No edits needed. Runtime validation pending. Next manually synchronize AWX project and verify successful revision before creating job template.

### AWX-010N — PASS: Project synchronized with smoke test playbook revision

User screenshot confirms Secure Headway Infrastructure Automation project Last Job Status Successful, Git branch main, source control revision 41a1bd5 matching AWX-010M documentation commit, cache timeout 0 seconds, discard local changes and update revision on job launch enabled. Next create localhost-only smoke test job template using empty Secure Headway Infrastructure inventory, AWX EE (24.6.1), no credentials, and limit localhost. No playbook execution yet.

### AWX-010O — PASS: First smoke test job template created

Screenshot verifies Secure Headway - AWX Smoke Test, organization Secure Headway, inventory Secure Headway Infrastructure, project Secure Headway Infrastructure Automation, EE AWX EE (24.6.1), playbook ansible/playbooks/awx-smoke-test.yml, job type run, limit localhost, forks 0 (default), verbosity 0, timeout 120, job slicing 1, show changes off, empty variables, no credentials displayed. Created 2026-11-23 18:49:59 UI local time by admin. Runtime verification pending. Next launch isolated smoke test and inspect task output and recap; require actual localhost execution, zero failures and zero changes.

### AWX-010P — PARTIAL PASS: First smoke test job launched; output verification pending

User screenshot shows job ID 3, Secure Headway - AWX Smoke Test, status Successful, elapsed 00:00:02, UI Tasks counter 4, and 'No output found for this job.' Actual task results, Ansible/Python versions, PASS marker and recap are not visible. Do not accept runtime milestone until actual stdout confirms five successful localhost tasks and zero changes/failures/unreachable. Next retrieve job stdout through authenticated AWX API or download control; no relaunch or configuration changes yet.

### AWX-010P.1 — PASS: First end-to-end Secure Headway Ansible job verified

2026-11-23: User retrieved downloaded stdout for job ID 3 (previous UI status Successful). Actual stdout confirms all five tasks executed on localhost, Ansible 2.15.12, Python 3.9.19, BEGIN and END PASS markers, recap ok=5 changed=0 unreachable=0 failed=0 skipped=0 rescued=0 ignored=0. Empty inventory warning expected for implicit localhost; no remote targets or configuration changes. UI previously showed no output and Tasks counter 4; downloaded stdout confirms execution and output retention, but UI display discrepancy root cause remains unverified. Initial AWX controller configuration and GitHub-to-Ansible smoke test milestone accepted. Next inspect job Details for actual SCM revision and execution environment evidence before closing the cumulative checkpoint.
