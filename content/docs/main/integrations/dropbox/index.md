---
title: Dropbox integration documentation
description: Back up and restore your Dropbox data, or store your Plakar backups on Dropbox, using the Rclone integration.
technology_description: This integration uses Rclone’s official Dropbox remote to connect Plakar to your Dropbox account securely and efficiently.
categories:
  - integrations
provides:
  - source-connector
  - destination-connector
  - storage-connector
tags:
  - dropbox
  - backup
  - storage
  - encryption
  - privacy
stage: testing
date: 2025-07-29
---

# Dropbox Integration

## 1. Introduction

> **Requirements:**
> - **Plakar version**: {{< param "plakar_version" >}}
>   - **Integration version**: {{< param "integration_version" >}}
>   - **Access rights**: Dropbox account with read access for source; write access for destination/storage roles

This integration allows you to back up and restore Dropbox data using Plakar’s Rclone plugin system. No extra package is needed beyond Rclone: Plakar calls the Rclone plugin via gRPC to connect and transfer data.

Snapshots are stored in a reposiroty called Kloset, with full deduplication, encryption, and immutability. You can even use Dropbox itself as the storage backend for your Kloset snapshots.

**Use cases:**
- Cold backup of Dropbox cloud storage
  - Long-term archive and legal hold of Dropbox folders
  - Offline export via `.ptar`
  - Disaster recovery workflows

**Target technologies:**
- **Supported versions**: All Dropbox accounts supported by Rclone
  - **System compatibility**: Compatible with personal and business Dropbox accounts

## 2. Architecture

> Architecture diagram:
```plaintext
Dropbox ← Source Connector → Kloset Store ←→ Storage Connector → Dropbox
                                 ↓
                  Dropbox ← Destination Connector
```

The Dropbox integration uses Plakar's plugin system to interact with Dropbox via Rclone. Plakar communicates with the plugin over gRPC, allowing you to create snapshots and restore them as needed.
A Dropbox account can also serve as the storage backend for Kloset snapshots, enabling you to leverage Dropbox's cloud storage capabilities directly.

**Components:**
- **Source Connector**: read-only connector to extract data from Dropbox
- **Destination Connector**: write connector to restore data into Dropbox
- **Storage Connector**: used to persist snapshots directly in Dropbox

## 3. Installation

Install Rclone and configure a Dropbox remote. See the [Rclone installation page](https://rclone.org/install/) and [Dropbox setup](https://rclone.org/dropbox/).

## 4. Configuration

Configure your remote in Rclone, then add it to Plakar’s config as shown in the Rclone integration doc.

## 5. Usage

- Backup: `plakar at /repo backup @dropbox`
- Restore: `plakar at /repo restore -to @dropbox <snapshot-id>`
- Store: `plakar at dropbox://kloset create`

## 6. Limitations

- Dropbox API rate limits

## 7. Permissions required

- Rclone must be authorized for your Dropbox account

## 8. Troubleshooting

- Check Rclone logs for errors

## 9. Backup strategy

Follow the 3-2-1 rule: 3 copies, 2 media, 1 offsite.

## 10. Appendix

- [Plakar CLI Reference](/docs/main)
- [Rclone Dropbox documentation](https://rclone.org/dropbox/)

## 11. FAQ

- Does Plakar back up all versions? No, only the latest.
- Can I restore to another provider? Yes, if supported by Rclone.
