---
title: "Rclone"
subtitle: "Secure and automated backups for your cloud storage with Rclone in Plakar"
description: Back up and restore your data from your cloud storage or store your back ups on it thanks to Rclone integration on Plakar.
technology_description: Rclone is a command-line program to manage files on cloud storage. It supports various providers like Google Drive, Google Photos, OneDrive, Dropbox, and more.
categories:
  - source connector
  - destination connector
tags:
  - Rclone
  - cloud storage
stage: testing
date: 2025-07-17
---

# Back up your cloud data with Plakar and Rclone

Plakar’s Rclone integration gives you a robust way to protect your cloud files against accidental deletion, corruption, or unauthorized modifications. Files, folders, and metadata are backed up automatically from your cloud provider and stored in secure, immutable snapshots.

Unlike manual downloads or partial exports, Plakar leverages Rclone’s powerful cloud connectors to capture complete, structured representations of your remote data, with full traceability and integrity checks.

---

## Why back up cloud data with Rclone?

Your cloud storage provider keeps your files accessible — but they don’t always guarantee versioned recovery if files are changed, overwritten, or deleted by mistake, ransomware, or misconfigured sync. Rclone simplify the connection between Plakar and your cloud storage, allowing you to back up and restore data from various cloud providers directly to your Kloset repository.

With Plakar + Rclone:

- Data is fetched securely using the official Rclone client
- Snapshots are cryptographically verified and immutable
- Each backup is self-contained, versioned, and browsable
- Your storage backend is fully configurable (local disk or cloud storage)

---

## Supported features

- Full remote file structure backup (folders, files, symlinks if supported)
- Metadata preservation where available (timestamps, checksums)
- Incremental snapshot deduplication to save bandwidth and space
- Restore data to any supported Rclone remote
- Store your backups on any Plakar-supported backend (filesystem, S3, SFTP, etc.)

---

## Limitations

- Only a subset of Rclone’s remotes are fully supported for now (`google drive`, `google photos`, `onedrive`, `opendrive`, `iclouddrive` — iCloud Photos not yet supported, `dropbox` and `proton drive')
- You must install and configure Rclone manually (`rclone config`)
- Initial backups of large remotes may take time; incremental syncs are optimized
- Some provider-specific features (like shared permissions) may not be captured yet

For detailed setup instructions, see the [configuration guide](../../docs/main/integrations/rclone/index.md).

---

## Threats mitigated

- Accidental or malicious file deletion in your cloud account
- Local corruption or ransomware on synced folders
- Permanent deletion after trash expiration
- Account lockout or permission misconfigurations

---

## Questions and support

Need help or want to request support for a new cloud provider?

→ [Open an issue on GitHub](https://github.com/PlakarKorp/integration-rclone/issues/new)  
→ [Join our Discord](https://discord.gg/uuegtnF2Q5) for real-time help and community support
