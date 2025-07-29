---
title: Google Photos integration documentation
description: Import and export your Google Photos library with Plakar using Rclone. Encrypted, deduplicated, and portable backups.
technology_description: This integration uses Rclone’s official Google Photos remote to extract and restore data into a Kloset store.
categories: integrations
tags:
  - google photos
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
  - viewer
---

# Integration Package: Google Photos

## 1. Introduction

This integration allows you to snapshot and restore Google Photos data using Plakar to store it in a Kloset store, while minimizing storage usage and ensuring strong data security.
It includes connectors that let you import and export snapshots to Google Photos itself, either from Google Photos or from other sources.
A Viewer is also provided to inspect, search, and restore snapshots without requiring full extraction.

**Use cases:**

* Cold backup of Google Photos library
* Long-term archiving and disaster recovery
* Portable export and vendor escape to other platforms

**Target technologies:**

* Supported versions: All Google Photos accounts supported by Rclone
* Supported editions: Personal and Business Google Photos
* System compatibility: macOS, Linux, Windows via Rclone

**Requirements:**

* Plakar version: >=1.0.3
* Integration version: 0.1.0
* Google Photos API credentials configured in Rclone

## 2. Architecture

```
                                Viewer (CLI/UI)
                                  ↑
Google Photos ← Source Connector → Kloset Store
                                  ↓
                  Google Photos ← Destination Connector → Other compatible resources
```

**Components provided:**

* Source Connector: extract data from Google Photos
* Destination Connector: restore snapshots to Google Photos
* Viewer: browse and search snapshots in UI/CLI

## 3. Installation

### 3.1 Prerequisites 

This integration is distributed as an Rclone-powered connector.
You only need Plakar and Rclone installed.

Install Rclone: [https://rclone.org/install/](https://rclone.org/install/)
Configure your Google Photos remote: [https://rclone.org/googlephotos/](https://rclone.org/googlephotos/)

```bash
rclone config
```

After you can build and install it in a few seconds using Plakar’s built-in tooling.

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
importers: fs, s3, googlephotos, ...
exporters: fs, s3, googlephotos, ...
klosets: fs, s3, ptar, ...
```

## 4. Configuration

Once Rclone is configured, import it into Plakar.

### 4.1 Source Connector

```bash
rclone config show mygooglephotos | plakar config source import
```

### 4.2 Destination Connector

```bash
rclone config show mygooglephotos | plakar config destination import
```

> Replace `mygooglephotos` with your Rclone remote name.

## 5. Usage

### 5.1 Snapshot

```bash
plakar at @mygooglephotos backup @mygooglephotos
```

### 5.2 Inspection

```bash
plakar at @mygooglephotos ls
plakar at @mygooglephotos cat <snapshot-id>:/path/to/file
plakar at @mygooglephotos ui
```

### 5.3 Restore

```bash
plakar at @mygooglephotos restore -to @mygooglephotos <snapshot-id>
```

> Here `@mygooglephotos` had been used as a source and destination connector.

## 6. Integration-specific behaviors

### 6.1 Limitations

* Google Photos API has rate limits — heavy usage may require throttling
* Only the latest version of files are snapshotted
* Shared links and permissions are not preserved in snapshots
* Cannot be used as a Kloset storage backend

### 6.2 Automation

* Schedule snapshots using cron or systemd timers
* Use `.ptar` archives for export/transport

### 6.3 Restore Notes

* Can restore into Google Photos or export to S3, filesystem, or other Plakar-compatible destinations

## 7. Troubleshooting

* Use `plakar log` and Rclone logs for diagnosis
* Ensure Google Photos remote is authorized

## 8. Backup strategy

Follow 3-2-1 best practice:

* 3 copies (original + 2 backups)
* 2 different storage backends
* 1 offsite (Google Photos, filesystem, or S3)

## 9. Appendix

* [Rclone Google Photos Docs](https://rclone.org/googlephotos/)
* [Plakar CLI Reference](/docs/main)