---
title: Dropbox integration
description: Backup and restore your Dropbox with Plakar — secure, portable, and deduplicated.
technology_description: This integration uses the official Dropbox remote via Rclone to extract and restore data into a Kloset store.
categories: integrations
tags:
  - dropbox
  - backup
  - disaster recovery
  - encryption
  - deduplication
  - versioning
  - immutable storage
  - compliance
  - long-term archiving
  - airgapped backup
  - snapshot technology
  - portable format
stage: test
date: 2025-07-26
plakar_version: ">=1.0.3"
integration_version: 0.1.0
resource_type: object-storage
provides:
  - source connector
  - destination connector
  - storage connector
  - viewer
---

# Integration Package: Dropbox

## 1. Introduction

This integration allows you to snapshot and restore Dropbox data using Plakar to store it in a Kloset store, while minimizing storage usage and ensuring strong data security.
It includes a Storage Connector that lets you persist snapshots to Dropbox itself, either from Dropbox or from other sources.
A Viewer is also provided to inspect, search, and restore snapshots without requiring full extraction.

**Use cases:**

* Cold backup of Dropbox folders
* Long-term archiving and disaster recovery
* Portable export and vendor escape to other platforms

**Target technologies:**

* Supported versions: All Dropbox accounts supported by Rclone
* Supported editions: Personal and Business Dropbox
* System compatibility: macOS, Linux, Windows via Rclone

**Requirements:**

* Plakar version: >=1.0.3
* Integration version: 0.1.0
* Dropbox API credentials configured in Rclone

## 2. Architecture

```
                                Viewer (CLI/UI)
                                  ↑
Dropbox ← Source Connector → Kloset Store ←→ Storage Connector → Dropbox
                                  ↓
                   Dropbox ← Destination Connector → Other compatible resources
```

**Components provided:**

* Source Connector: extract data from Dropbox
* Destination Connector: restore snapshots to Dropbox
* Storage Connector: persist snapshots inside Dropbox as the backend
* Viewer: browse and search snapshots in UI/CLI

## 3. Installation

### 3.1 Prerequisites 

This integration is distributed as an Rclone-powered connector.
You only need Plakar and Rclone installed.

Install Rclone: [https://rclone.org/install/](https://rclone.org/install/)
Configure your Dropbox remote: [https://rclone.org/dropbox/](https://rclone.org/dropbox/)

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

You should now see all the rclone provider listed (which includes Dropbox) in the importers, exporters, or klosets:

```plaintext
importers: fs, s3, dropbox, ...
exporters: fs, s3, dropbox, ...
klosets: fs, s3, ptar, dropbox, ...
```


## 4. Configuration

Once Rclone is configured, import it into Plakar.

### 4.1 Source Connector

To import your rclone config as a source connector (to make backups), run:

```bash
rclone config show mydropbox | plakar source import
```

### 4.2 Destination Connector

To import your rclone config as a destination connector (to restore backups), run:

```bash
rclone config show mydropbox | plakar destination import
```

### 4.3 Storage Connector

To import your rclone config as a storage connector (to store backups in Dropbox), run:

```bash
rclone config show mydropbox | plakar store import
```

## 5. Usage

For the following examples, we will use `@mydropbox` as the Rclone remote name configured in Plakar.

First over all, we need to create a Kloset store to hold our snapshots:

```bash
plakar at ./backup create
```

A folder named `backup` will be created in the current directory, which will hold the snapshots.

### 5.1 Snapshot

To back up your Dropbox data in the recently created Kloset store, use the following command:

```bash
plakar at ./backup backup @mydropbox
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

To restore a snapshot back to Dropbox, use the following command:

```bash
plakar at ./backup restore -to @mydropbox <snapshot-id>
```

This will restore the snapshot to your Dropbox account, making it available in the same structure as it was when backed up.

### 5.4 Storage

To use Dropbox as a storage backend, you have to create a Kloset store that uses the Dropbox remote.

```bash
plakar at @mydropbox create
```

This will create a Kloset store in your Dropbox cloud. And he will be used like any other Kloset store.

> Here `@mydropbox` had been used as a source, destination, and store connector.

## 6. Integration-specific behaviors

### 6.1 Limitations

* Dropbox API has rate limits — heavy usage may require throttling
* Only the latest version of files are snapshotted
* Shared links and permissions are not preserved in snapshots

### 6.2 Automation

* Schedule snapshots using cron or systemd timers
* Use `.ptar` archives for export/transport

### 6.3 Restore Notes

* Can restore into Dropbox or export to S3, filesystem, or other Plakar-compatible destinations

## 7. Troubleshooting

* Use `plakar log` and Rclone logs for diagnosis
* Ensure Dropbox remote is authorized

## 8. Backup strategy

Follow 3-2-1 best practice:

* 3 copies (original + 2 backups)
* 2 different storage backends
* 1 offsite (Dropbox, filesystem, or S3)

## 9. Appendix

* [Rclone Dropbox Docs](https://rclone.org/dropbox/)
* [Plakar CLI Reference](/docs/main)
