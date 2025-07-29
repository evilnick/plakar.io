---
title: iCloud Drive integration documentation
description: Back up and restore your iCloud Drive data, or store your Plakar backups on iCloud Drive, using the Rclone integration.
technology_description: This integration uses Rclone’s official iCloud Drive remote to connect Plakar to your iCloud Drive account securely and efficiently.
categories:
  - integrations
provides:
  - source-connector
  - destination-connector
  - storage-connector
tags:
  - icloud drive
  - backup
  - storage
  - encryption
  - privacy
stage: test
date: 2025-07-29
---

# iCloud Drive Integration

## 1. Introduction

> **Requirements:**
> - **Plakar version**: {{< param "plakar_version" >}}
>   - **Integration version**: {{< param "integration_version" >}}
>   - **Access rights**: iCloud Drive account with read access for source; write access for destination/storage roles

This integration allows you to back up and restore iCloud Drive data using Plakar’s Rclone plugin system. No extra package is needed beyond Rclone: Plakar calls the Rclone plugin via gRPC to connect and transfer data.

Snapshots are stored in a reposiroty called Kloset, with full deduplication, encryption, and immutability. You can even use iCloud Drive itself as the storage backend for your Kloset snapshots.

**Note:** iCloud Drive integration does not support iCloud Photos.

**Use cases:**
- Cold backup of iCloud Drive cloud storage
  - Long-term archive and legal hold of iCloud Drive folders
  - Offline export via `.ptar`
  - Disaster recovery workflows

**Target technologies:**
- **Supported versions**: All iCloud Drive accounts supported by Rclone
  - **System compatibility**: Compatible with personal and business iCloud Drive accounts

## 2. Architecture

> Architecture diagram:
```plaintext
iCloud Drive ← Source Connector → Kloset Store ←→ Storage Connector → iCloud Drive
                                 ↓
                  iCloud Drive ← Destination Connector
```

The iCloud Drive integration uses Plakar's plugin system to interact with iCloud Drive via Rclone. Plakar communicates with the plugin over gRPC, allowing you to create snapshots and restore them as needed.
An iCloud Drive account can also serve as the storage backend for Kloset snapshots, enabling you to leverage iCloud Drive's cloud storage capabilities directly.

**Components:**
- **Source Connector**: read-only connector to extract data from iCloud Drive
- **Destination Connector**: write connector to restore data into iCloud Drive
- **Storage Connector**: used to persist snapshots directly in iCloud Drive

## 3. Installation

Install Rclone and configure an iCloud Drive remote. See the [Rclone installation page](https://rclone.org/install/) and [iCloud Drive setup](https://rclone.org/icloud/).

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

- iCloud Drive API limitations
- No support for iCloud Photos

## 7. Permissions required

- Rclone must be authorized for your Apple account

## 8. Troubleshooting

- Check Rclone logs for errors

## 9. Backup strategy

Follow the 3-2-1 rule: 3 copies, 2 media, 1 offsite.

## 10. Appendix

- [Plakar CLI Reference](/docs/main)
- [Rclone iCloud Drive documentation](https://rclone.org/icloud/)

## 11. FAQ

- Does Plakar back up all versions? No, only the latest.
- Can I restore to another provider? Yes, if supported by Rclone.
