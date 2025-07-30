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
stage: test
date: 2025-07-29
---

# Integration Package: OpenDrive

## 1. Introduction

This integration allows you to snapshot and restore OpenDrive data using Plakar to store it in a Kloset store, while minimizing storage usage and ensuring strong data security.
It includes a Storage Connector that lets you persist snapshots to OpenDrive itself, either from OpenDrive or from other sources.
A Viewer is also provided to inspect, search, and restore snapshots without requiring full extraction.

**Use cases:**

* Cold backup of OpenDrive folders
* Long-term archiving and disaster recovery
* Portable export and vendor escape to other platforms

**Target technologies:**

* Supported versions: All OpenDrive accounts supported by Rclone
* Supported editions: Personal and Business OpenDrive
* System compatibility: macOS, Linux, Windows via Rclone

**Requirements:**

* Plakar version: >=1.0.3
* Integration version: 0.1.0
* OpenDrive API credentials configured in Rclone

## 2. Architecture

```
                                Viewer (CLI/UI)
                                  ↑
OpenDrive ← Source Connector → Kloset Store ←→ Storage Connector → OpenDrive
                                  ↓
                   OpenDrive ← Destination Connector → Other compatible resources
```

**Components provided:**

* Source Connector: extract data from OpenDrive
* Destination Connector: restore snapshots to OpenDrive
* Storage Connector: persist snapshots inside OpenDrive as the backend
* Viewer: browse and search snapshots in UI/CLI

## 3. Installation

### 3.1 Prerequisites 

This integration is distributed as an Rclone-powered connector.
You only need Plakar and Rclone installed.

Install Rclone: [https://rclone.org/install/](https://rclone.org/install/)
Configure your OpenDrive remote: [https://rclone.org/opendrive/](https://rclone.org/opendrive/)

```bash
rclone config
```

After you can install it in a few seconds using Plakar’s built-in tooling.

**Install the package:**

Run the following command to install the integration:

```bash
plakar pkg add rclone
```

This will generate a portable .ptar archive and install it in your Plakar environment.

**Verify installation:**

Check that the integration appears in your available connectors:

```bash
plakar version
```

You should now see all the rclone provider listed (which includes OpenDrive) in the importers, exporters, or klosets:
```plaintext
importers: fs, s3, opendrive, ...
exporters: fs, s3, opendrive, ...
klosets: fs, s3, ptar, opendrive, ...
```


## 4. Configuration

Once Rclone is configured, import it into Plakar.

### 4.1 Source Connector

To import your rclone config as a source connector (to make backups), run:

```bash
rclone config show myopendrive | plakar source import
```

### 4.2 Destination Connector

To import your rclone config as a destination connector (to restore backups), run:

```bash
rclone config show myopendrive | plakar destination import
```

### 4.3 Storage Connector

To import your rclone config as a storage connector (to store backups in OpenDrive), run:

```bash
rclone config show myopendrive | plakar store import
```

> Replace `myopendrive` with your Rclone remote name.

## 5. Usage

For the following examples, we will use `@myopendrive` as the Rclone remote name configured in Plakar.

First over all, we need to create a Kloset store to hold our snapshots:

```bash
plakar at ./backup create
```

A folder named `backup` will be created in the current directory, which will hold the snapshots.

### 5.1 Snapshot

To back up your OpenDrive data in the recently created Kloset store, use the following command:

```bash
plakar at ./backup backup @myopendrive
```

The last line of the output will show the snapshot ID, which you can use to inspect or restore later.

### 5.2 Inspection

With Plakar, you can inspect your snapshots without extracting them.
You can list or display the contents of the Kloset store:

```bash
plakar at ./backup ls
plakar at ./backup cat <snapshot-id>:/path/to/file
plakar at ./backup ui
```

### 5.3 Restore

To restore a snapshot back to OpenDrive, use the following command:

```bash
plakar at ./backup restore -to @myopendrive <snapshot-id>
```

This will restore the snapshot to your OpenDrive account, making it available in the same structure as it was when backed up.

### 5.4 Storage

To use OpenDrive as a storage backend, you have to create a Kloset store that uses the OpenDrive remote.

```bash
plakar at @myopendrive create
```

This will create a Kloset store in your OpenDrive cloud. And he will be used like any other Kloset store.

## 6. Integration-specific behaviors

### 6.1 Limitations

* OpenDrive API has rate limits — heavy usage may require throttling
* Only the latest version of files are snapshotted
* Shared links and permissions are not preserved in snapshots

### 6.2 Automation

* Schedule snapshots using cron or systemd timers
* Use `.ptar` archives for export/transport

### 6.3 Restore Notes

* Can restore into OpenDrive or export to S3, filesystem, or other Plakar-compatible destinations

## 7. Troubleshooting

* Use `plakar log` and Rclone logs for diagnosis
* Ensure OpenDrive remote is authorized

## 8. Backup strategy

Follow 3-2-1 best practice:

* 3 copies (original + 2 backups)
* 2 different storage backends
* 1 offsite (OpenDrive, filesystem, or S3)

## 9. Appendix

* [Rclone OpenDrive Docs](https://rclone.org/opendrive/)
* [Plakar CLI Reference](/docs/main)

- Backup: `plakar at /repo backup @your_config_name`
- Restore: `plakar at /repo restore -to @your_config_name <snapshot-id>`
- Store: `plakar at your_config_name://kloset create`

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
