---
title: Google Drive integration documentation
description: Back up and restore your Google Drive data, or store your Plakar backups on Google Drive, using the Rclone integration.
technology_description: This integration uses Rclone’s official Google Drive remote to connect Plakar to your Google Drive account securely and efficiently.
categories:
  - integrations
provides:
  - source-connector
  - destination-connector
  - storage-connector
tags:
  - google drive
  - backup
  - storage
  - encryption
  - privacy
stage: test
date: 2025-07-29
---

# Google Drive Integration

## 1. Introduction

> **Requirements:**
> - **Plakar version**: {{< param "plakar_version" >}}
>   - **Integration version**: {{< param "integration_version" >}}
>   - **Access rights**: Google Drive account with read access for source; write access for destination/storage roles

This integration allows you to back up and restore Google Drive data using Plakar’s Rclone plugin system. No extra package is needed beyond Rclone: Plakar calls the Rclone plugin via gRPC to connect and transfer data.

Snapshots are stored in a reposiroty called Kloset, with full deduplication, encryption, and immutability. You can even use Google Drive itself as the storage backend for your Kloset snapshots.

**Use cases:**
- Cold backup of Google Drive cloud storage
  - Long-term archive and legal hold of Drive folders
  - Offline export via `.ptar`
  - Disaster recovery workflows

**Target technologies:**
- **Supported versions**: All Google Drive accounts supported by Rclone
  - **System compatibility**: Compatible with personal and business Google Drive accounts

## 2. Architecture

> Architecture diagram:
```plaintext
Google Drive ← Source Connector → Kloset Store ←→ Storage Connector → Google Drive
                                 ↓
                  Google Drive ← Destination Connector 
```

The Google Drive integration uses Plakar's plugin system to interact with Google Drive via Rclone. Plakar communicates with the plugin over gRPC, allowing you to create snapshots and restore them as needed.
A Google Drive account can also serve as the storage backend for Kloset snapshots, enabling you to leverage Google Drive's cloud storage capabilities directly.

**Components:**
- **Source Connector**: read-only connector to extract data from Google Drive
- **Destination Connector**: write connector to restore data into Google Drive
- **Storage Connector**: used to persist snapshots directly in Google Drive

## 3. Installation

Install Rclone and configure a Google Drive remote. See the [Rclone installation page](https://rclone.org/install/) and [Google Drive setup](https://rclone.org/drive/).

## 4. Configuration

Configure your remote in Rclone, then add it to Plakar’s config as shown in the Rclone integration doc.

To include your configuration in your Plakar config, you can use the following command:


```bash
rclone config show `your_config_name` | ./plakar `source|destination|store` import
```

> Choose `source`, `destination`, or `store` based on your use case.
> Source for backups, destination for restores, and store for Kloset.

## 5. Usage

- Backup: `plakar at /repo backup @your_config_name`
- Restore: `plakar at /repo restore -to @your_config_name <snapshot-id>`
- Store: `plakar at your_config_name://kloset create`

## 6. Limitations

- Google Drive API rate limits
- Shared drives require extra config

## 7. Permissions required

- Rclone must be authorized for your Google account

## 8. Troubleshooting

- Check Rclone logs for errors

## 9. Backup strategy

Follow the 3-2-1 rule: 3 copies, 2 media, 1 offsite.

## 10. Appendix

- [Plakar CLI Reference](/docs/main)
- [Rclone Google Drive documentation](https://rclone.org/drive/)

## 11. FAQ

- Does Plakar back up all versions? No, only the latest.
- Can I restore to another provider? Yes, if supported by Rclone.
