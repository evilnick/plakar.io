---
title: Koofr integration documentation
description: Back up and restore your Koofr data, or store your Plakar backups on Koofr, using the Rclone integration.
technology_description: This integration uses Rclone’s official Koofr remote to connect Plakar to your Koofr account securely and efficiently.
categories: integrations
tags:
  - google drive
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

# Integration Package: Koofr

## 1. Introduction

This integration allows you to snapshot and restore Koofr data using Plakar to store it in a Kloset store, while minimizing storage usage and ensuring strong data security.
It includes a Storage Connector that lets you persist snapshots to Koofr itself, either from Koofr or from other sources.
A Viewer is also provided to inspect, search, and restore snapshots without requiring full extraction.

**Use cases:**

* Cold backup of Koofr folders
* Long-term archiving and disaster recovery
* Portable export and vendor escape to other platforms

**Target technologies:**

* Supported versions: All Koofr accounts supported by Rclone
* Supported editions: Personal and Business Koofr
* System compatibility: macOS, Linux, Windows via Rclone

**Requirements:**

* Plakar version: >=1.0.3
* Integration version: 0.1.0
* Koofr API credentials configured in Rclone

## 2. Architecture

```
                                Viewer (CLI/UI)
                                  ↑
Koofr ← Source Connector → Kloset Store ←→ Storage Connector → Koofr
                                  ↓
                   Koofr ← Destination Connector → Other compatible resources
```

**Components provided:**

* Source Connector: extract data from Koofr
* Destination Connector: restore snapshots to Koofr
* Storage Connector: persist snapshots inside Koofr as the backend
* Viewer: browse and search snapshots in UI/CLI

## 3. Installation

### 3.1 Prerequisites 

This integration is distributed as an Rclone-powered connector.
You only need Plakar and Rclone installed.

Install Rclone: [https://rclone.org/install/](https://rclone.org/install/)
Configure your Koofr remote: [https://rclone.org/koofr/](https://rclone.org/koofr/)

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

You should now see all the rclone provider listed (which includes Koofr) in the importers, exporters, or klosets:
```plaintext
importers: fs, s3, koofr, ...
exporters: fs, s3, koofr, ...
klosets: fs, s3, ptar, koofr, ...
```


## 4. Configuration

Once Rclone is configured, import it into Plakar.

### 4.1 Source Connector

```bash
rclone config show mykoofr | plakar config source import
```

### 4.2 Destination Connector

```bash
rclone config show mykoofr | plakar config destination import
```

### 4.3 Storage Connector

```bash
rclone config show mykoofr | plakar config store import
```

> Replace `mykoofr` with your Rclone remote name.

## 5. Usage

### 5.1 Snapshot

```bash
plakar at @mykoofr backup @mykoofr
```

### 5.2 Inspection

```bash
plakar at @mykoofr ls
plakar at @mykoofr cat <snapshot-id>:/path/to/file
plakar at @mykoofr ui
```

### 5.3 Restore

```bash
plakar at @mykoofr restore -to @mykoofr <snapshot-id>
```

> Here `@mykoofr` had been used as a source, destination, and store connector.

## 6. Integration-specific behaviors

### 6.1 Limitations

* Koofr API has rate limits — heavy usage may require throttling
* Only the latest version of files are snapshotted
* Shared links and permissions are not preserved in snapshots

### 6.2 Automation

* Schedule snapshots using cron or systemd timers
* Use `.ptar` archives for export/transport

### 6.3 Restore Notes

* Can restore into Koofr or export to S3, filesystem, or other Plakar-compatible destinations

## 7. Troubleshooting

* Use `plakar log` and Rclone logs for diagnosis
* Ensure Koofr remote is authorized

## 8. Backup strategy

Follow 3-2-1 best practice:

* 3 copies (original + 2 backups)
* 2 different storage backends
* 1 offsite (Koofr, filesystem, or S3)

## 9. Appendix

* [Rclone Koofr Docs](https://rclone.org/koofr/)
* [Plakar CLI Reference](/docs/main)
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

- Koofr API limitations

## 7. Permissions required

- Rclone must be authorized for your Koofr account

## 8. Troubleshooting

- Check Rclone logs for errors

## 9. Backup strategy

Follow the 3-2-1 rule: 3 copies, 2 media, 1 offsite.

## 10. Appendix

- [Plakar CLI Reference](/docs/main)
- [Rclone Koofr documentation](https://rclone.org/koofr/)

## 11. FAQ

- Does Plakar back up all versions? No, only the latest.
- Can I restore to another provider? Yes, if supported by Rclone.
