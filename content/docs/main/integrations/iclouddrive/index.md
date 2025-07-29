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

# Integration Package: iCloud Drive

## 1. Introduction

This integration allows you to snapshot and restore iCloud Drive data using Plakar to store it in a Kloset store, while minimizing storage usage and ensuring strong data security.
It includes a Storage Connector that lets you persist snapshots to iCloud Drive itself, either from iCloud Drive or from other sources.
A Viewer is also provided to inspect, search, and restore snapshots without requiring full extraction.

**Use cases:**

* Cold backup of iCloud Drive folders
* Long-term archiving and disaster recovery
* Portable export and vendor escape to other platforms

**Target technologies:**

* Supported versions: All iCloud Drive accounts supported by Rclone
* Supported editions: Personal and Business iCloud Drive
* System compatibility: macOS, Linux, Windows via Rclone

**Requirements:**

* Plakar version: >=1.0.3
* Integration version: 0.1.0
* iCloud Drive API credentials configured in Rclone

## 2. Architecture

```
                                Viewer (CLI/UI)
                                  ↑
iCloud Drive ← Source Connector → Kloset Store ←→ Storage Connector → iCloud Drive
                                  ↓
                   iCloud Drive ← Destination Connector → Other compatible resources
```

**Components provided:**

* Source Connector: extract data from iCloud Drive
* Destination Connector: restore snapshots to iCloud Drive
* Storage Connector: persist snapshots inside iCloud Drive as the backend
* Viewer: browse and search snapshots in UI/CLI

## 3. Installation

### 3.1 Prerequisites 

This integration is distributed as an Rclone-powered connector.
You only need Plakar and Rclone installed.

Install Rclone: [https://rclone.org/install/](https://rclone.org/install/)
Configure your iCloud Drive remote: [https://rclone.org/icloud/](https://rclone.org/icloud/)

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

You should now see all the rclone provider listed (which includes iCloud Drive) in the importers, exporters, or klosets:
```plaintext
importers: fs, s3, icloud, ...
exporters: fs, s3, icloud, ...
klosets: fs, s3, ptar, icloud, ...
```


## 4. Configuration

Once Rclone is configured, import it into Plakar.

### 4.1 Source Connector

```bash
rclone config show myicloud | plakar config source import
```

### 4.2 Destination Connector

```bash
rclone config show myicloud | plakar config destination import
```

### 4.3 Storage Connector

```bash
rclone config show myicloud | plakar config store import
```

> Replace `myicloud` with your Rclone remote name.

## 5. Usage

### 5.1 Snapshot

```bash
plakar at @myicloud backup @myicloud
```

### 5.2 Inspection

```bash
plakar at @myicloud ls
plakar at @myicloud cat <snapshot-id>:/path/to/file
plakar at @myicloud ui
```

### 5.3 Restore

```bash
plakar at @myicloud restore -to @myicloud <snapshot-id>
```

> Here `@myicloud` had been used as a source, destination, and store connector.

## 6. Integration-specific behaviors

### 6.1 Limitations

* iCloud Drive API has rate limits — heavy usage may require throttling
* Only the latest version of files are snapshotted
* Shared links and permissions are not preserved in snapshots
* No support for iCloud Photos

### 6.2 Automation

* Schedule snapshots using cron or systemd timers
* Use `.ptar` archives for export/transport

### 6.3 Restore Notes

* Can restore into iCloud Drive or export to S3, filesystem, or other Plakar-compatible destinations

## 7. Troubleshooting

* Use `plakar log` and Rclone logs for diagnosis
* Ensure iCloud Drive remote is authorized

## 8. Backup strategy

Follow 3-2-1 best practice:

* 3 copies (original + 2 backups)
* 2 different storage backends
* 1 offsite (iCloud Drive, filesystem, or S3)

## 9. Appendix

* [Rclone iCloud Drive Docs](https://rclone.org/icloud/)
* [Plakar CLI Reference](/docs/main)
