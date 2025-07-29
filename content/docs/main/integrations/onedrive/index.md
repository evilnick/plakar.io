---
title: OneDrive integration documentation
description: Back up and restore your OneDrive data, or store your Plakar backups on OneDrive, using the Rclone integration.
technology_description: This integration uses Rclone’s official OneDrive remote to connect Plakar to your OneDrive account securely and efficiently.
categories:
  - integrations
provides:
  - source-connector
  - destination-connector
  - storage-connector
tags:
  - onedrive
  - backup
  - storage
  - encryption
  - privacy
stage: test
date: 2025-07-29
---

# OneDrive Integration

## 1. Introduction

> **Requirements:**
> - **Plakar version**: {{< param "plakar_version" >}}
>   - **Integration version**: {{< param "integration_version" >}}
>   - **Access rights**: OneDrive account with read access for source; write access for destination/storage roles

This integration allows you to back up and restore OneDrive data using Plakar’s Rclone plugin system. No extra package is needed beyond Rclone: Plakar calls the Rclone plugin via gRPC to connect and transfer data.

Snapshots are stored in a reposiroty called Kloset, with full deduplication, encryption, and immutability. You can even use OneDrive itself as the storage backend for your Kloset snapshots.

**Use cases:**
- Cold backup of OneDrive cloud storage
  - Long-term archive and legal hold of OneDrive folders
  - Offline export via `.ptar`
  - Disaster recovery workflows

**Target technologies:**
- **Supported versions**: All OneDrive accounts supported by Rclone
  - **System compatibility**: Compatible with personal and business OneDrive accounts

## 2. Architecture

> Architecture diagram:
```plaintext
OneDrive ← Source Connector → Kloset Store ←→ Storage Connector → OneDrive
                                 ↓
                  OneDrive ← Destination Connector → Compatible cloud targets
```

The OneDrive integration uses Plakar's plugin system to interact with OneDrive via Rclone. Plakar communicates with the plugin over gRPC, allowing you to create snapshots and restore them as needed.
A OneDrive account can also serve as the storage backend for Kloset snapshots, enabling you to leverage OneDrive's cloud storage capabilities directly.

**Components:**
- **Source Connector**: read-only connector to extract data from OneDrive
- **Destination Connector**: write connector to restore data into OneDrive
- **Storage Connector**: used to persist snapshots directly in OneDrive

## 3. Installation

Install Rclone and configure a OneDrive remote. See the [Rclone installation page](https://rclone.org/install/) and [OneDrive setup](https://rclone.org/onedrive/).

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

- OneDrive API rate limits
- Shared folders require extra config

## 7. Permissions required

- Rclone must be authorized for your Microsoft account

## 8. Troubleshooting

- Check Rclone logs for errors

## 9. Backup strategy

Follow the 3-2-1 rule: 3 copies, 2 media, 1 offsite.

## 10. Appendix

- [Plakar CLI Reference](/docs/main)
- [Rclone OneDrive documentation](https://rclone.org/onedrive/)

## 11. FAQ

- Does Plakar back up all versions? No, only the latest.
- Can I restore to another provider? Yes, if supported by Rclone.
