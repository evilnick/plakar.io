---
title: Proton Drive integration documentation
description: Back up and restore your Proton Drive data, or store your Plakar backups on Proton Drive, using the Rclone integration.
technology_description: This integration uses Rclone’s official Proton Drive remote to connect Plakar to your Proton Drive account securely and efficiently.
categories:
  - integrations
provides:
  - source-connector
  - destination-connector
  - storage-connector
tags:
  - proton drive
  - backup
  - storage
  - encryption
  - privacy
stage: test
date: 2025-07-29
---

# Integration Package: Proton Drive

## 1. Introduction

This integration allows you to snapshot and restore Proton Drive data using Plakar to store it in a Kloset store, while minimizing storage usage and ensuring strong data security.
It includes a Storage Connector that lets you persist snapshots to Proton Drive itself, either from Proton Drive or from other sources.
A Viewer is also provided to inspect, search, and restore snapshots without requiring full extraction.

**Use cases:**

* Cold backup of Proton Drive folders
* Long-term archiving and disaster recovery
* Portable export and vendor escape to other platforms

**Target technologies:**

* Supported versions: All Proton Drive accounts supported by Rclone
* Supported editions: Personal and Business Proton Drive
* System compatibility: macOS, Linux, Windows via Rclone

**Requirements:**

* Plakar version: >=1.0.3
* Integration version: 0.1.0
* Proton Drive API credentials configured in Rclone

## 2. Architecture

```
                                Viewer (CLI/UI)
                                  ↑
Proton Drive ← Source Connector → Kloset Store ←→ Storage Connector → Proton Drive
                                  ↓
                   Proton Drive ← Destination Connector → Other compatible resources
```

**Components provided:**

* Source Connector: extract data from Proton Drive
* Destination Connector: restore snapshots to Proton Drive
* Storage Connector: persist snapshots inside Proton Drive as the backend
* Viewer: browse and search snapshots in UI/CLI

## 3. Installation

### 3.1 Prerequisites 

This integration is distributed as an Rclone-powered connector.
You only need Plakar and Rclone installed.

Install Rclone: [https://rclone.org/install/](https://rclone.org/install/)
Configure your Proton Drive remote: [https://rclone.org/protondrive/](https://rclone.org/protondrive/)

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

You should now see all the rclone provider listed (which includes Proton Drive) in the importers, exporters, or klosets:
```plaintext
importers: fs, s3, protondrive, ...
exporters: fs, s3, protondrive, ...
klosets: fs, s3, ptar, protondrive, ...
```


## 4. Configuration

Once Rclone is configured, import it into Plakar.

### 4.1 Source Connector

```bash
rclone config show myprotondrive | plakar config source import
```

### 4.2 Destination Connector

```bash
rclone config show myprotondrive | plakar config destination import
```

### 4.3 Storage Connector

```bash
rclone config show myprotondrive | plakar config store import
```

> Replace `myprotondrive` with your Rclone remote name.

## 5. Usage

### 5.1 Snapshot

```bash
plakar at @myprotondrive backup @myprotondrive
```

### 5.2 Inspection

```bash
plakar at @myprotondrive ls
plakar at @myprotondrive cat <snapshot-id>:/path/to/file
plakar at @myprotondrive ui
```

### 5.3 Restore

```bash
plakar at @myprotondrive restore -to @myprotondrive <snapshot-id>
```

> Here `@myprotondrive` had been used as a source, destination, and store connector.

## 6. Integration-specific behaviors

### 6.1 Limitations

* Proton Drive API has rate limits — heavy usage may require throttling
* Only the latest version of files are snapshotted
* Shared links and permissions are not preserved in snapshots

### 6.2 Automation

* Schedule snapshots using cron or systemd timers
* Use `.ptar` archives for export/transport

### 6.3 Restore Notes

* Can restore into Proton Drive or export to S3, filesystem, or other Plakar-compatible destinations

## 7. Troubleshooting

* Use `plakar log` and Rclone logs for diagnosis
* Ensure Proton Drive remote is authorized

## 8. Backup strategy

Follow 3-2-1 best practice:

* 3 copies (original + 2 backups)
* 2 different storage backends
* 1 offsite (Proton Drive, filesystem, or S3)

## 9. Appendix

* [Rclone Proton Drive Docs](https://rclone.org/protondrive/)
* [Plakar CLI Reference](/docs/main)

- Backup: `plakar at /repo backup @your_config_name`
- Restore: `plakar at /repo restore -to @your_config_name <snapshot-id>`
- Store: `plakar at your_config_name://kloset create`

## 6. Limitations

- Proton Drive API limitations

## 7. Permissions required

- Rclone must be authorized for your Proton Drive account

## 8. Troubleshooting

- Check Rclone logs for errors

## 9. Backup strategy

Follow the 3-2-1 rule: 3 copies, 2 media, 1 offsite.

## 10. Appendix

- [Plakar CLI Reference](/docs/main)
- [Rclone Proton Drive documentation](https://rclone.org/protondrive/)

## 11. FAQ

- Does Plakar back up all versions? No, only the latest.
- Can I restore to another provider? Yes, if supported by Rclone.
