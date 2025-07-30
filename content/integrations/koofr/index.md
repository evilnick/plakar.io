---
title: "Koofr"
subtitle: "Encrypted, versioned storage and backup for your Koofr files"
description: Use Plakar to import, store, and export your Koofr data securely. Immutable, deduplicated backups you fully control.
technology_description: Koofr is a privacy-focused European cloud storage service that lets you connect multiple storage accounts under one roof.
categories:
  - source connector
  - destination connector
  - storage connector
tags:
  - koofr
  - backup
  - restore
  - storage
  - cloud
stage: available
date: 2025-07-25
---

# Plakar + Koofr: Take Privacy-First Backups Even Further

Koofr is simple, private, and European — a great choice for secure cloud storage. But even Koofr can’t save you from:

- 🚫 Accidental deletions or sync errors
- 🦠 Ransomware or silent corruption
- 🔓 Leaked credentials

🔐 **Plakar adds encrypted, versioned control to your Koofr files. Import, store, and export them with zero-trust encryption and point-in-time recovery.**

Because privacy doesn’t stop at cloud storage — it extends to backups too.

> *👉 Get started with the [setup guide](docs/main/integrations/koofr/)*

## 🧠 What is Koofr?

Koofr is a secure cloud storage service based in Europe. It lets you store files, share them safely, and even connect other cloud accounts — all while respecting your privacy.

But if you ever lose access or files go missing, native tools have limits.

## 🚨 Why Koofr Needs Real Backup Control

Koofr is private by design — but accidents and attacks don’t care where your data lives.

Native versioning helps, but can’t cover:

- Permanent deletion or account lockout
- Malware or ransomware overwriting files
- The need to store clean, offline copies

🎯 Plakar steps in with encrypted, immutable snapshots — on your terms.

## 🛡️ How Plakar Works with Koofr

Plakar securely **imports** your Koofr files, encrypts and deduplicates them locally, and **stores** snapshots in your Plakar repository (called a *Kloset*). You can even **store your Plakar backups back on Koofr**, or export files back when needed.

| **Risk**                       | **How Plakar Helps**                                              |
|--------------------------------|--------------------------------------------------------------------|
| 🚫 Accidental deletion          | Restore any file or folder back to Koofr                           |
| 🦠 Malware or ransomware        | Immutable, encrypted snapshots keep clean versions safe            |
| 🔓 Account compromise           | Zero-trust: you own the keys, not Koofr                            |
| 📂 Partial versioning gaps      | Content-aware deduplication covers every change                    |
| 🔄 Storage flexibility          | Store snapshots on Koofr or another secure backend                 |

## ✅ Common Use Cases

- Import your Koofr files into an encrypted Plakar repository
- Store daily Plakar snapshots **on Koofr** for redundancy
- Export restored data back to Koofr if needed
- Keep an offline, immutable copy of your cloud files
- Migrate Koofr content without vendor lock-in

## 📊 Integration Details

| **Property**         | **Value**                           |
|----------------------|-------------------------------------|
| Category             | Cloud Storage                       |
| Supported Methods    | CLI / Agent / Web UI                |
| Protocols            | Koofr API, HTTPS                    |
| Encryption Model     | Local key derivation, zero-trust    |

## 🗺️ How It Works (At a Glance)

```

Koofr ⇄ Plakar Agent ⇄ Encrypted, Deduplicated Snapshots ⇄ Koofr or Other Storage

```

Files flow securely in, get chunked and encrypted, then stored wherever you want — Koofr included.

## � TL;DR: Backups You Control

Plakar + Koofr gives you:

✅ Encrypted, versioned backups
✅ End-to-end encryption (you own the keys)
✅ Deduplication to save space
✅ Zero-trust backup flows
✅ Visual inspection and audit readiness
✅ No cloud vendor lock-in

---

💡 Ready to extend your Koofr privacy?

[Import, store, and export your Koofr files with Plakar →](docs/main/integrations/koofr/)