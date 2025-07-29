---
title: Koofr integration documentation
description: Back up and restore your Koofr data, or store your Plakar backups on Koofr, using the Rclone integration.
technology_description: This integration uses Rclone’s official Koofr remote to connect Plakar to your Koofr account securely and efficiently.
categories:
  - integrations
provides:
  - source-connector
  - destination-connector
  - storage-connector
tags:
  - koofr
  - backup
  - storage
  - encryption
  - privacy
stage: testing
date: 2025-07-29
---

# Koofr Integration

## 1. Introduction

> **Requirements:**
> - **Plakar version**: {{< param "plakar_version" >}}
>   - **Integration version**: {{< param "integration_version" >}}
>   - **Access rights**: Koofr account with read access for source; write access for destination/storage roles

This integration allows you to back up and restore Koofr data using Plakar’s Rclone plugin system. No extra package is needed beyond Rclone: Plakar calls the Rclone plugin via gRPC to connect and transfer data.

Snapshots are stored in a reposiroty called Kloset, with full deduplication, encryption, and immutability. You can even use Koofr itself as the storage backend for your Kloset snapshots.

**Use cases:**
- Cold backup of Koofr cloud storage
  - Long-term archive and legal hold of Koofr folders
  - Offline export via `.ptar`
  - Disaster recovery workflows

**Target technologies:**
- **Supported versions**: All Koofr accounts supported by Rclone
  - **System compatibility**: Compatible with personal and business Koofr accounts

## 2. Architecture

> Architecture diagram:
```plaintext
Koofr ← Source Connector → Kloset Store ←→ Storage Connector → Koofr
                                 ↓
                  Koofr ← Destination Connector
```

The Koofr integration uses Plakar's plugin system to interact with Koofr via Rclone. Plakar communicates with the plugin over gRPC, allowing you to create snapshots and restore them as needed.
A Koofr account can also serve as the storage backend for Kloset snapshots, enabling you to leverage Koofr's cloud storage capabilities directly.

**Components:**
- **Source Connector**: read-only connector to extract data from Koofr
- **Destination Connector**: write connector to restore data into Koofr
- **Storage Connector**: used to persist snapshots directly in Koofr

## 3. Installation

Install Rclone and configure a Koofr remote. See the [Rclone installation page](https://rclone.org/install/) and [Koofr setup](https://rclone.org/koofr/).

## 4. Configuration

Configure your remote in Rclone, then add it to Plakar’s config as shown in the Rclone integration doc.

## 5. Usage

- Backup: `plakar at /repo backup @koofr`
- Restore: `plakar at /repo restore -to @koofr <snapshot-id>`
- Store: `plakar at koofr://kloset create`

## 6. Limitations

- Koofr API limitations

## 7. Permissions required

- Rclone must be authorized for your Koofr account

## 8. Troubleshooting

- Check Rclone logs for errors

## 9. Backup strategy

Follow the 3-2-1 rule: 3 copies, 2 media, 1 offsite.

## 10. Appendix

- [Plakar CLI Reference](/docs/main)
- [Rclone Koofr documentation](https://rclone.org/koofr/)

## 11. FAQ

- Does Plakar back up all versions? No, only the latest.
- Can I restore to another provider? Yes, if supported by Rclone.
