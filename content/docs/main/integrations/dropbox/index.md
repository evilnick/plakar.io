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

After you can build and install it in a few seconds using Plakar’s built-in tooling.
Build the package

**Build the package:**

Run the following command to fetch and compile the integration:

```bash
plakar pkg build rclone
```

This will generate a portable .ptar archive, for example:

`rclone_v0.1.0-devel.xxxxxx_linux_amd64.ptar`

**Install the package:**

Once built, install it locally with:

```bash
plakar pkg add ./path/to/rclone_v0.1.0-devel.xxxxxx_linux_amd64.ptar
```

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

```bash
rclone config show mydropbox | plakar config source import
```

### 4.2 Destination Connector

```bash
rclone config show mydropbox | plakar config destination import
```

### 4.3 Storage Connector

```bash
rclone config show mydropbox | plakar config store import
```

> Replace `mydropbox` with your Rclone remote name.

## 5. Usage

### 5.1 Snapshot

```bash
plakar at @mydropbox backup @mydropbox
```

### 5.2 Inspection

```bash
plakar at @mydropbox ls
plakar at @mydropbox cat <snapshot-id>:/path/to/file
plakar at @mydropbox ui
```

### 5.3 Restore

```bash
plakar at @mydropbox restore -to @mydropbox <snapshot-id>
```

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
