---
title: MinIO Integration Documentation
description: Back up and restore S3-compatible object storage using Plakar with a local or remote MinIO server.
technology_description: This integration uses MinIO’s S3-compatible API to store and retrieve encrypted snapshot data from a Kloset repository.
categories: 
- integrations
tags:
- minio
- s3
- object-storage
- plakar
stage: "stable"
date: 2025-07-22
---

This page guides you through the process of setting up MinIO backups using Plakar.

## Overview

- **Storage target:** MinIO (S3-compatible)
- **Integration Type:** Storage Connector
- **Use Cases:** Immutable backups, rollback recovery, ransomware defense
- **Works with:** SNSD, SNMD, MNMD topologies
- **Included:** Source Connector · Storage Connector · Viewer · Snapshot Engine

---

## Prerequisites

Make sure you have the following before you begin:

- Basic familiarity with MinIO and S3 concepts
- Plakar version `v1.0.2` installed


## Supported Topologies (MinIO)

| Mode      | Reliability     | Recommended For                             |
|-----------|-----------------|----------------------------------------------|
| SNSD      | Low          | Local testing, low-stakes environments       |
| SNMD      | Moderate     | Dev workloads, small business environments   |
| MNMD      | High        | Enterprise-grade backup and restore setups   |


### 1. Configure MinIO as a Storage Backend

MinIO can serve two purposes with Plakar:

1. As a storage backend for your Kloset repository.
2. As a source of backup, allowing Plakar to read and write data to S3-compatible buckets.

First, download and install MinIO from the [official MinIO website](https://min.io/download?platform=kubernetes&installer=helm).

Then, run MinIO locally. The `--console-address` flag exposes a management dashboard at http://localhost:9001.
```bash
minio server ~/minio --console-address :9001
```

This will host your object storage at http://localhost:9000.

### Create a Bucket in MinIO

Visit the MinIO web console at http://localhost:9001 and log in with:
```
Username: minioadmin
Password: minioadmin
```
Create a new bucket (e.g., `mybackups`). This will serve as the Plakar storage backend.

### Set Up AWS CLI for Local MinIO

Configure a new profile to interact with your local MinIO instance using the AWS CLI.
```
aws configure --profile minio-local
```
Use the following credentials when prompted:
```
AWS Access Key ID [None]: minioadmin
AWS Secret Access Key [None]: minioadmin
Default region name [None]: us-east-1
Default output format [None]: json
```
Verify the connection to MinIO:

```
aws --profile minio-local --endpoint-url http://localhost:9000 s3 ls
```
If your server is running and your bucket (`mybackups`) exists, it will be listed.

---

### 2. Connect Plakar to MinIO using the `plakar config`

Use Plakar's [config system](https://docs.plakar.io/en/commands/plakar/v1.0.2/config/index.html) to register MinIO as a named remote repository.

```bash
plakar config repository create minio-s3
plakar config repository set minio-s3 location s3://localhost:9000/mybackups
plakar config repository set minio-s3 access_key minioadmin
plakar config repository set minio-s3 secret_access_key minioadmin
plakar config repository set minio-s3 use_tls false
```

You now have a named repository `@minio-s3` pointing to your MinIO bucket.

Initialize the Plakar Repository on MinIO

```
plakar at @minio-s3 create
```

>*When prompted, enter a new passphrase. Important: do not paste it. Type it manually to avoid encoding issues.*

## Plakar MinIO Remote Configuration Options (Example)

| Command                                                   | Description                                                                 | Required | Value Used                            |
|-----------------------------------------------------------|-----------------------------------------------------------------------------|----------|----------------------------------------|
| `plakar config repository create minio-s3`             | Initializes a named remote repository configuration in Plakar              | ✅ Yes   | `minio-s3`                          |
| `plakar config repository set minio-s3 location s3://localhost:9000/mybackups` | Sets the S3-compatible URL to your MinIO bucket            | ✅ Yes   | `s3://localhost:9000/mybackups`        |
| `plakar config repository set minio-s3 access_key minioadmin` | Access key for your MinIO instance                              | ✅ Yes   | `minioadmin`                           |
| `plakar config repository set minio-s3 secret_access_key minioadmin` | Secret key for your MinIO instance                       | ✅ Yes   | `minioadmin`                           |
| `plakar config repository set minio-s3 use_tls false`  | Disables TLS (useful for local HTTP MinIO setups)                          | ✅ Yes   | `false`                                |
| `plakar at @minio-s3 create`                            | Initializes a new Plakar repository at the configured MinIO location       | ✅ Yes   | `minio-s3` repository created       |

### Sync from Local Repository to MinIO

Once your MinIO remote repository is configured, you can synchronize data from your local Plakar repository (e.g., `plkr-lcl-backups`) to your MinIO-backed remote (`@minio-s3`):
```
plakar at plkr-lcl-backups sync to @minio-s3
```
### What This Does:

- Pushes all local snapshots to the remote MinIO-based repository.
- Ensures data redundancy by storing backups offsite (even if MinIO is local, this mirrors a remote-like workflow).

Useful for air-gapped backups, cloud syncing, or preparing for automated retention policies.

## Test Your Integration

### List available snapshots

Verify that your MinIO-backed Plakar repository is working correctly by listing available snapshots:

```
plakar at @minio-s3 ls
```

### Open the Web Interface
You can also browse your snapshots using Plakar's built-in web UI:
```
plakar at @minio-s3 ui
```
>This will launch a local web interface in your browser (ensure your environment allows GUI-based access if running headless or on a server).

### Restore Snapshots from MinIO

You can retrieve backed-up data from your S3-compatible repository using Plakar’s restore command.

Restore a Single File

```
plakar at @minio-s3 restore <snapshot_id>:path/to/file.txt
```

Or Restore an Entire Snapshot

```
plakar at @minio-s3 restore <snapshot_id>
```

This will restore the full contents of the selected snapshot to your current working directory.

> **Kloset** is Plakar’s snapshot engine that compresses, encrypts, and addresses data chunks by their content hash. The resulting snapshot is immutable, portable, and verifiable.


## What This Integration Does

This integration uses:

- **Storage Connector**: sends deduplicated chunks to MinIO
- **Viewer**: allows inspection of snapshot metadata via CLI
- **Kloset**: Plakar’s core engine for splitting, encrypting, and indexing snapshots


## Related Docs

- [Plakar CLI Reference](https://docs.plakar.io/en/commands/plakar/index.html)
- [How Snapshots Work](https://docs.plakar.io/en/concepts/index.html)
- [Plakar Architecture (Kloset Engine)](https://www.plakar.io/posts/2025-04-29/kloset-the-immutable-data-store/)