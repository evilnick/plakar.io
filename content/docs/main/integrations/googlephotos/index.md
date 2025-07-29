---
title: Google Photos integration documentation
description: Import and export your Google Photos library with Plakar using Rclone. Encrypted, deduplicated, and portable backups.
technology_description: This integration uses Rclone’s official Google Photos remote to connect Plakar to your Google Photos account.
categories:
  - integrations
provides:
  - source-connector
  - destination-connector
  - storage-connector
tags:
  - google photos
  - backup
  - storage
  - encryption
  - privacy
stage: testing
date: 2025-07-29
---

# Google Photos Integration

## 1. Introduction

> **Requirements:**
> - **Plakar version**: {{< param "plakar_version" >}}
>   - **Integration version**: {{< param "integration_version" >}}
>   - **Access rights**: Google Photos account with read access for source; write access for destination roles

This integration allows you to import and export your Google Photos library using Plakar’s Rclone plugin system. No extra package is needed beyond Rclone: Plakar calls the Rclone plugin via gRPC to connect and transfer data.

Snapshots are stored in a reposiroty called Kloset, with full deduplication, encryption, and immutability. Google Photos cannot be used as a storage backend for Kloset snapshots.

**Use cases:**
- Cold backup of Google Photos library
  - Long-term archive and legal hold of albums
  - Offline export via `.ptar`
  - Disaster recovery workflows

**Target technologies:**
- **Supported versions**: All Google Photos accounts supported by Rclone
  - **System compatibility**: Compatible with personal and business Google Photos accounts

## 2. Architecture

> Architecture diagram:
```plaintext
Google Photos ← Source Connector → Kloset Store
```

The Google Photos integration uses Plakar's plugin system to interact with Google Photos via Rclone. Plakar communicates with the plugin over gRPC, allowing you to create snapshots and restore them as needed.
Google Photos cannot serve as the storage backend for Kloset snapshots.

**Components:**
- **Source Connector**: read-only connector to extract data from Google Photos
- **Destination Connector**: write connector to restore data into Google Photos

## 3. Installation

Install Rclone and configure a Google Photos remote. See the [Rclone installation page](https://rclone.org/install/) and [Google Photos setup](https://rclone.org/googlephotos/).

## 4. Configuration

Configure your remote in Rclone, then add it to Plakar’s config as shown in the Rclone integration doc.

## 5. Usage

- Import: `plakar at /repo backup @googlephotos`
- Export: `plakar at /repo restore -to @googlephotos <snapshot-id>`

## 6. Limitations

- Google Photos API limitations
- No support for storage backend

## 7. Permissions required

- Rclone must be authorized for your Google account

## 8. Troubleshooting

- Check Rclone logs for errors

## 9. Backup strategy

Follow the 3-2-1 rule: 3 copies, 2 media, 1 offsite.

## 10. Appendix

- [Plakar CLI Reference](/docs/main)
- [Rclone Google Photos documentation](https://rclone.org/googlephotos/)

## 11. FAQ

- Does Plakar back up all versions? No, only the latest.
- Can I restore to another provider? Yes, if supported by Rclone.
