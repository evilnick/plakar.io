---
title: "OneDrive"
subtitle: "Encrypted, versioned backups for your OneDrive files"
description: Use Plakar to import, store, and export your OneDrive data securely. Immutable, deduplicated, and fully under your control.
technology_description: OneDrive is Microsoft’s cloud storage solution for individuals and businesses, seamlessly integrated with Office 365 and Windows.
categories:
  - source connector
  - destination connector
  - storage connector
tags:
  - onedrive
  - backup
  - restore
  - storage
  - cloud
stage: available
date: 2025-07-25
---

# Plakar + OneDrive: Take Back Control of Your Cloud Files

OneDrive makes file sync and sharing easy — but easy doesn’t always mean secure or flexible enough for real-world risks.

Your data can vanish faster than you think:

- ❌ Files deleted locally sync deletions to the cloud
- 🦠 Ransomware encrypts files, then syncs the damage
- 🔓 Compromised credentials put entire libraries at risk

🔐 **Plakar lets you import, store, and export your OneDrive files in an encrypted, versioned backup — so you decide what stays, what rolls back, and where it lives.**

Because your files deserve more than just “cloud storage” — they deserve real resilience.

> 👉 Get started with the [setup guide](docs/main/integrations/onedrive/)

## 🧠 What is OneDrive?

OneDrive is Microsoft’s cloud storage service, built into Windows and Office 365. It keeps files accessible across devices and easy to share — but its built-in protection only goes so far if something goes wrong.

## 🚨 Why OneDrive Needs More Than Sync

Sync is not backup. One wrong move can sync a deletion or overwrite across every device.

OneDrive’s Recycle Bin and Version History help, but they can’t save you if:

- Retention runs out before you catch the issue
- A ransomware attack encrypts your files before you react
- You need to restore a folder exactly as it was weeks or months ago

🎯 Plakar fills the gap with encrypted, content-aware snapshots you can store anywhere — including OneDrive itself.

## 🛡️ How Plakar Protects Your OneDrive

Plakar securely **imports** your OneDrive files, encrypts and deduplicates them locally, then **stores** versioned snapshots in your Plakar backup repository (called a *Kloset*). You can even **store your backup data on OneDrive**, giving you a self-managed layer of protection inside your existing storage.

When needed, simply **export** your files or folders back to OneDrive — exactly as they were.

| **Risk**                        | **How Plakar Helps**                                                |
|---------------------------------|----------------------------------------------------------------------|
| ❌ Accidental deletion           | Restore files or folders back to OneDrive at any point              |
| 🦠 Ransomware or malware         | Immutable, encrypted snapshots protect clean versions               |
| 🔓 Account breach                | Backups are zero-trust — you own the keys, not Microsoft            |
| 📂 Limited versioning            | Plakar preserves full history with deduplication                    |
| 🔄 Storage flexibility           | Store Plakar snapshots on OneDrive or another storage location      |

## ✅ Common Use Cases

- Import your full OneDrive into an encrypted Plakar repository
- Store daily snapshots **on OneDrive** for simple redundancy
- Export clean copies back to OneDrive after accidental loss or corruption
- Audit file changes with Plakar’s built-in snapshot viewer
- Migrate OneDrive content to another cloud or local storage

## 📊 Integration Details

| **Property**         | **Value**                           |
|----------------------|-------------------------------------|
| Category             | Cloud Storage                       |
| Supported Methods    | CLI / Agent / Web UI                |
| Protocols            | Microsoft Graph API, HTTPS          |
| Encryption Model     | Local key derivation, zero-trust    |

## 🗺️ How It Works (At a Glance)

```
OneDrive ⇄ Plakar Agent ⇄ Encrypted, Deduplicated Snapshots ⇄ OneDrive or Other Storage
```

Your data flows in securely, is encrypted locally, and can be stored **back on OneDrive** or any other destination you choose.

## � TL;DR: Backups You Control

Plakar + OneDrive gives you:

✅ Encrypted, versioned backups
✅ End-to-end encryption (you own the keys)
✅ Deduplication to save space
✅ Zero-trust backup flows
✅ Visual inspection and audit readiness
✅ No cloud vendor lock-in

---

� Ready to make OneDrive work on your terms?

[Back up, store, and restore your OneDrive with Plakar →](docs/main/integrations/onedrive/)
