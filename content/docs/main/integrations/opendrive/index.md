---
title: OpenDrive integration documentation
description: Back up and restore your OpenDrive data, or store your Plakar backups on OpenDrive, using the Rclone integration.
technology_description: This integration uses Rclone’s official OpenDrive remote to connect Plakar to your OpenDrive account securely and efficiently.
categories:
  - integrations
provides:
  - source-connector
  - destination-connector
  - storage-connector
tags:
  - opendrive
  - backup
  - storage
  - encryption
  - privacy
stage: testing
date: 2025-07-29
---

# OpenDrive Integration

## 1. Introduction

> **Requirements:**
> - **Plakar version**: {{< param "plakar_version" >}}
>   - **Integration version**: {{< param "integration_version" >}}
>   - **Access rights**: OpenDrive account with read access for source; write access for destination/storage roles

This integration allows you to back up and restore OpenDrive data using Plakar’s Rclone plugin system. No extra package is needed beyond Rclone: Plakar calls the Rclone plugin via gRPC to connect and transfer data.

Snapshots are stored in a reposiroty called Kloset, with full deduplication, encryption, and immutability. You can even use OpenDrive itself as the storage backend for your Kloset snapshots.

**Use cases:**
- Cold backup of OpenDrive cloud storage
  - Long-term archive and legal hold of OpenDrive folders
  - Offline export via `.ptar`
  - Disaster recovery workflows

**Target technologies:**
- **Supported versions**: All OpenDrive accounts supported by Rclone
  - **System compatibility**: Compatible with personal and business OpenDrive accounts

## 2. Architecture

> Architecture diagram:
```plaintext
OpenDrive ← Source Connector → Kloset Store ←→ Storage Connector → OpenDrive
                                 ↓
                  OpenDrive ← Destination Connector
```

The OpenDrive integration uses Plakar's plugin system to interact with OpenDrive via Rclone. Plakar communicates with the plugin over gRPC, allowing you to create snapshots and restore them as needed.
An OpenDrive account can also serve as the storage backend for Kloset snapshots, enabling you to leverage OpenDrive's cloud storage capabilities directly.

**Components:**
- **Source Connector**: read-only connector to extract data from OpenDrive
- **Destination Connector**: write connector to restore data into OpenDrive
- **Storage Connector**: used to persist snapshots directly in OpenDrive

## 3. Installation

Install Rclone and configure an OpenDrive remote. See the [Rclone installation page](https://rclone.org/install/) and [OpenDrive setup](https://rclone.org/opendrive/).

## 4. Configuration

Configure your remote in Rclone, then add it to Plakar’s config as shown in the Rclone integration doc.

## 5. Usage

- Backup: `plakar at /repo backup @opendrive`
- Restore: `plakar at /repo restore -to @opendrive <snapshot-id>`
- Store: `plakar at opendrive://kloset create`

## 6. Limitations

- OpenDrive API rate limits

## 7. Permissions required

- Rclone must be authorized for your OpenDrive account

## 8. Troubleshooting

- Check Rclone logs for errors

## 9. Backup strategy

Follow the 3-2-1 rule: 3 copies, 2 media, 1 offsite.

## 10. Appendix

- [Plakar CLI Reference](/docs/main)
- [Rclone OpenDrive documentation](https://rclone.org/opendrive/)

## 11. FAQ

- Does Plakar back up all versions? No, only the latest.
- Can I restore to another provider? Yes, if supported by Rclone.
