---
title: MinIO integration package
description: Backup and restore your MinIO buckets with Plakar; secure, portable, and deduplicated.
technology_description: This integration uses the S3-compatible API of MinIO to extract and restore bucket contents into a Kloset store.
categories:
- integrations
tags:
- minio
- s3-compatible
- object storage
- object storage backup
- cloud-native
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
stage: beta
date: 2025-07-26
plakar_version: 1.0.3 or later
integration_version: 1.0.0
resource_type: object-storage
provides:
- source-connector
- destination-connector
- storage-connector
- viewer
---

## Introduction

> **Requirements**
> - **Plakar version**: {{< param "plakar_version" >}}
> - **Integration version**: {{< param "integration_version" >}}
> - **Access rights**: IAM user with read access (`s3:GetObject`, `s3:ListBucket`) on the bucket for source; `s3:PutObject` for destination/storage roles

Plakar's built-in MinIO integration includes three connectors:
* **Storage connector**: to host a Kloset store in a MinIO bucket.
* **Source connector**: to back up any MinIO bucket into an existing Kloset store.
* **Destination connector**: to restore from any Kloset store into a MinIO bucket.

This integration is built-in and does not require any additional package installation.

> Architecture diagram
```plaintext
                             Viewer (CLI/UI)
                                 ↑
MinIO ← Source Connector → Kloset Store ←→ Storage Connector → MinIO
                                 ↓
                  MinIO ← Destination Connector → Compatible S3 targets
```

**Use cases:**
- Cold backup of your on-premise object storage.
- Long-term archive and legal hold of MinIO buckets.
- Offline export your buckets to a flat `.ptar` file.
- Disaster recovery workflows.

**Compatibility:**

- **Supported versions**: All versions of MinIO.
- **System compatibility**: Compatible with local, on-premise and cloud-hosted MinIO servers.


## Installation

Check that `s3` appears in the available connectors. No extra package installation is needed.

> Available connectors for your Plakar installation
```bash
$ plakar version
plakar/v1.0.2

importers: fs, ftp, s3, sftp # <-- Check that `s3` is listed here
exporters: fs, ftp, s3, sftp # <-- And here
klosets: fs, http, https, ptar, s3, sftp, sqlite # <-- And here
```

---

### Configure IAM permissions in MinIO

MinIO supports fine-grained access control using IAM-style policies. You can assign permissions to users or service accounts using one of the following methods:

***Option 1: Using the mc CLI (MinIO Client)***

This is the most common and scriptable method. You can:
- Create users with mc admin user add
- Define policies in JSON format compatible with AWS IAM
- Attach policies to users with mc admin policy attach

> Contents of readonly-policy.json
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:ListBucket"],
      // Or, to allow writes for restoring snapshots
      // "Action": ["s3:GetObject", "s3:ListBucket", "s3:PutObject"],
      "Resource": ["arn:aws:s3:::mybucket", "arn:aws:s3:::mybucket/*"]
    }
  ]
}
```

> Setup MinIO policy using the CLI
```bash
$ mc alias set local http://localhost:9000 minioadmin minioadmin
$ mc admin user add local plakar_user mysecretpassword
$ mc admin policy create local plakar-readonly-policy readonly-policy.json
$ mc admin policy attach local --user plakar_user plakar-readonly-policy
```

---

***Option 2: Using the MinIO Console (Web UI)***

If you have enabled the admin console, you can:
- Create users via the Identity > Users panel
- Assign predefined or custom policies
- Review and manage access through the UI


## Configuration

Depending on your needs, you can configure Plakar to use MinIO either as a Kloset store backend, as a source for backups, as a destination for restoring snapshots, or a combination of all three.

---

### Storage Connector

Configure the storage connector, to host a Kloset store in a MinIO bucket.

> Configure the storage connector to host a Kloset store in MinIO
```bash
$ plakar config repository create minio_store
$ plakar config repository set minio_store location s3://localhost:9000/plakar-kloset
$ plakar config repository set minio_store access_key minioadmin
$ plakar config repository set minio_store secret_access_key minioadmin
# Only if your MinIO instance does not use TLS
$ plakar config repository set minio_store use_tls false
```

Use the syntax `plakar at @minio_store` to refer to this store in commands, for example `plakar at @minio_store ls` to list snapshots.

---

### Source Connector

Configure the source connector, to backup a MinIO bucket.

> Configure the source connector to back up a MinIO bucket
```bash
$ plakar config remote create minio_src
$ plakar config remote set minio_src location s3://localhost:9000/mybucket
$ plakar config remote set minio_src access_key minioadmin
$ plakar config remote set minio_src secret_access_key minioadmin
# Only if your MinIO instance does not use TLS
$ plakar config remote set minio_src use_tls false
```

Use the syntax `@minio_src` to refer to this source in commands, for example `plakar backup @minio_src` to create a snapshot of the bucket.

---

### Destination Connector

Configure the destination connector, to restore a snapshot into a MinIO bucket.

> Configure the destination connector to restore a snapshot into a MinIO bucket
```bash
$ plakar config remote create minio_dst
$ plakar config remote set minio_dst location s3://localhost:9000/restore-bucket
$ plakar config remote set minio_dst access_key minioadmin
$ plakar config remote set minio_dst secret_access_key minioadmin
# Only if your MinIO instance does not use TLS
$ plakar config remote set minio_dst use_tls false
```

Use the syntax `@minio_dst` to refer to this destination in commands, for example `plakar restore -to @minio_dst <snapshot-id>` to restore a snapshot into the bucket.


## Usage

### Backup a MinIO bucket to a Kloset store hosted on MinIO

This commands performs a backup of the MinIO bucket configured in the source connector `@minio_src`, and stores it in the Kloset store configured in the storage connector `@minio_store`.

> Store a snapshot of `@minio_src` in `@minio_store`
```bash
$ plakar at @minio_store backup @minio_src
```

Plakar uses a Source Connector to:
- List all objects in the bucket using the S3-compatible API, 
- Stream their content securely from the bucket, 
- Break them down into small, content-addressed chunks, 
- Deduplicate, compress and encrypt each chunk on the fly,
- Write them to the Kloset store configured via minio_store.

The resulting snapshot includes:
- The object hierarchy (keys, prefixes) reconstructed as a virtual filesystem,
- All associated metadata (timestamps, content-type, size), 
- A manifest pointing to all chunks, forming a self-described, verifiable, immutable backup.

The snapshot operation is stateless: only new or changed chunks are written to the store. Existing identical data is reused thanks to global deduplication.

While MinIO does not offer native snapshot isolation, Plakar captures a consistent view of the data as seen during the listing and streaming phase. For consistency-sensitive use cases, consider freezing writes or restoring from versioned buckets.

---

### Inspect your snapshots

Plakar represents your bucket objects as a navigable file and folder tree structure, providing an intuitive way to browse your snapshots.

This abstraction makes it easy to search and explore your backed-up data, whether you're using CLI commands or the web-based user interface.

> List available snapshots
```bash
$ plakar at @minio_store ls
```

> Stream the contents of a file in a snapshot
```bash
$ plakar at @minio_store cat <snapshot-id>:/path/to/file
```

> Open the web UI
```bash
$ plakar at @minio_store ui
```

---

### Restore a snapshot to a MinIO bucket, or to a local folder

Plakar offers versatile restoration capabilities for your MinIO snapshots.

Data can be restored not only to other MinIO instances or any compatible object store, but also directly to file systems where bucket paths are represented as directories and objects as files.

This flexibility ensures your data remains accessible regardless of your target infrastructure.

> Restore a snapshot to the MinIO bucket `@minio_dst`
```bash
$ plakar at @minio_store restore -to @minio_dst <snapshot-id>
```

> Restore a snapshot to the local folder `./restore`
```bash
$ plakar at @minio_store restore -to ./restore <snapshot-id>
```

---

### Create a Kloset store in a MinIO bucket

This command initializes a new Kloset store directly inside the specified MinIO bucket.

Once created, you can use this bucket as a durable, S3-compatible backend for storing Plakar snapshots.

Plakar does not require any additional server-side software to operate. It treats the MinIO bucket as a passive blob store and uses it to persist the Kloset index, snapshot manifests, and data chunks.


Using MinIO as a Kloset store makes the integration completely self-contained. The same MinIO instance acts as both source/destination of data and as durable snapshot backend.

> Create a Kloset store in a MinIO bucket
```bash
$ plakar at $minio_store create
```

> Then use it like any other store:
```bash
plakar at @minio_store ls
plakar at @minio_store backup /etc
plakar at @minio_store restore -to ./restored <snapshot-id>
```



## Integration-specific behaviors

### Limitations

While the integration is designed to be as complete and transparent as possible, certain elements of MinIO or S3-compatible storage systems are **not captured** during snapshot operations.

This is due to either technical limitations of the API or the deliberate scope of Plakar, which focuses on user data and verifiable content.

**Not included in the snapshot:**

* **Bucket-level IAM policies**
  Access control rules and IAM policies defined at the bucket level are managed externally to the object data itself. These policies must be preserved or reapplied separately when restoring to a new bucket or environment.
* **Lifecycle rules**
  Automatic object expiration, tiering, or transition rules configured in MinIO are not part of the snapshot and will not be preserved. After restore, you must manually reconfigure lifecycle settings if needed.
* **Server-side encryption (SSE) configuration**
  If your MinIO instance uses server-side encryption (SSE) for storage-level encryption, the SSE configuration is not part of the data captured by Plakar. However, **Plakar performs its own encryption** before storing any chunk, ensuring data-at-rest security even without relying on SSE.

**Included in the snapshot:**

* **All user-stored objects**
  Every object listed in the bucket at the time of snapshot - regardless of size, storage class, or naming convention - is backed up in full.
* **Object metadata**
  Standard metadata such as:

  * Timestamps (modification time, upload date)
  * Object size
  * MIME type / content-type
  * User-defined metadata headers

This ensures that object structure and identity are faithfully restored across environments or clouds.

---

### Restore target considerations

Restore to a namespace or sandbox bucket first to validate contents.

---

### Snapshot consistency and limitations

Plakar relies on the MinIO (S3-compatible) API to scan and snapshot bucket contents. However, object storage systems do not provide snapshot isolation guarantees, meaning that:
- If objects are added, modified or deleted during the snapshot process, the resulting snapshot may be inconsistent.
- There is no atomic point-in-time capture across all objects.

As a result:

The snapshot represents a consistent read of all objects at the time they were listed and fetched, but not a frozen image of the bucket at a single moment in time.

## Troubleshooting

### Credential errors

If you see 403/401 errors, verify access keys, secret keys, and that `use_tls` is correctly set.

```bash
$ plakar at @minio_store ls
```


---

### MinIO with Docker

Use `docker logs` to view MinIO logs if running in a container.


```bash
$ docker logs <minio-container-name>
```

## Backup strategy

The tipical backup strategy for Plakar with MinIO is the standard 3-2-1 rule:
- Keep **3** copies
- On **2** types of media
- At least **1** copy offsite or offline

Define your backup frequency and retention policy based on your business needs:
- **RPO**: how much data can be lost
- **RTO**: how fast you must restore


## Appendix

- [Plakar CLI Reference](/docs/main)
- [Plakar Architecture (Kloset Engine)](https://www.plakar.io/posts/2025-04-29/kloset-the-immutable-data-store/)
- [MinIO Documentation](https://min.io/docs/minio/linux/index.html)

## Frequently asked questions

**Does Plakar back up all object versions?**  

No. Plakar only includes the latest visible version of each object in the bucket at snapshot time.

If versioning is enabled in MinIO, older versions are not backed up.

---

**Can I use synchronize a Kloset store hosted in MinIO with to another MinIO bucket?**

Yes.

Declare two Kloset stores pointing to your source and target MinIO buckets (e.g. `@minio_prod` and `@minio_backup`), then run a sync command.

> Transfer a snapshot from a Kloset store in MinIO to another one
```bash
$ plakar at @minio_prod sync <snapshot-id> to @minio_backup
```
> Transfer all the snapshots of a Kloset store to another one
```bash
$ plakar at @minio_prod sync to @minio_backup
```

---

**Does Plakar support TLS?**  

Yes. TLS is fully supported.

In the examples, `use_tls` is disabled only for local development.

If your MinIO instance uses a certificate, set `use_tls` to `true`.

---

**Can I restore data from MinIO to another provider (e.g., AWS, Azue, GCP, Wasabi, Scaleway, OVH)?**  

Yes.

---

**How to generate a flat `.ptar` file from a MinIO store?**

Like any other Kloset store, you can use `plakar ptar` to export a Kloset store into a portable `.ptar` file.

> Export `@minio_store` to a `.ptar` file
```bash
$ plakar ptar -o ./export.ptar -k @minio_store
```

---

**How to restore a `.ptar` file to a MinIO bucket?**

Run the following command to restore a `.ptar` file into a MinIO bucket configured in the destination connector `@minio_dst`.

> Restore a `.ptar` file to a target
```bash
$ plakar at ./export.ptar restore -to @minio_dst
```
