---
title: "SFTP"

subtitle: "Secure, encrypted backups over the SFTP protocol"

description: >
  Back up and restore directories to and from remote servers over SFTP.
  Plakar ensures encrypted, deduplicated, and verifiable snapshots
  across Linux, BSD, and NAS environments.

technology_title: SFTP is universal and security-focused

technology_description: >
  SFTP (SSH File Transfer Protocol) is a widely supported standard for securely transferring files
  across servers, NAS devices, and cloud environments using the SSH protocol.

  While SFTP provides secure transport, it does not provide immutability, deduplication,
  or integrated snapshot management on its own.

  Plakar fills that gap by turning any SFTP-accessible server into a secure backup source,
  a resilient storage backend, or a flexible destination for encrypted, verifiable snapshots.

categories:
  - source connector
  - destination connector
  - storage connector
  - viewer

tags:
  - SFTP
  - Linux
  - BSD
  - NAS
  - Synology
  - QNAP

seo_tags:
  - SFTP backup
  - Linux backup
  - NAS backup
  - secure file transfer
  - immutable snapshots
  - encrypted backups
  - disaster recovery
  - SSH file transfer
  - multi-server backup
  - airgapped storage

technical_documentation_link: /docs/main/integrations/sftp/

stage: stable

date: 2025-07-29

plakar_version: ">=1.0.3"

resource: SFTP

resource_type: file-transfer
---


Plakar’s SFTP integration lets you **back up**, **browse**, and **restore** data over a secure SSH channel. Whether you’re managing a single Linux server, a fleet of BSD hosts, or a NAS like Synology or QNAP, Plakar makes SFTP backups simple and resilient.

## 🔐 Secure backups beyond file transfer

SFTP is everywhere: from Linux and BSD servers to NAS devices like Synology or QNAP.
It’s trusted for secure file transfer, but file transfer is not backup.
- Files can be deleted or overwritten immediately after upload
- No version history or snapshots exist for recovery
- Relying on manual transfers leaves you exposed to mistakes or attacks
If your only “backup” strategy is copying files over SFTP, you’re one compromise or misconfiguration away from data loss.

**Plakar transforms SFTP into a reliable, encrypted backup workflow with immutable, verifiable snapshots.**

## 🛡️ Plakar + SFTP: full backup confidence

With Plakar, your SFTP workflow becomes more than file transfer:
- Store encrypted snapshots on any SFTP‑accessible server
- Pull or push backups from multiple servers into a single Kloset repository
- Inspect and verify snapshots without restoring them
- Restore anywhere: local FS, SFTP, or cloud object storage

**Whether it’s one NAS or an entire fleet of servers, Plakar adapts to your infrastructure.**

## 🚀 From snapshot to recovery, all in one tool

Plakar replaces ad‑hoc scripts and risky manual transfers with a unified solution:
- ✅ Immutable, versioned snapshots
- 🔐 End‑to‑end encryption over SSH/SFTP
- 🧠 Global deduplication and compression
- 🔎 Browsing and inspection without rehydration
- 📦 Optional offline export for compliance and long‑term retention

**With Push and Pull models, Plakar lets you scale from a single host to a multi‑server environment while keeping backups secure, verifiable, and recoverable.**