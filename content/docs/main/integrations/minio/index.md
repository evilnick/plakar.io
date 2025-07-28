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

## 1. Introduction

> **Requirements:**
> - **Plakar version**: {{< param "plakar_version" >}}
>   - **Integration version**: {{< param "integration_version" >}}
>   - **Access rights**: IAM user with read access (`s3:GetObject`, `s3:ListBucket`) on the bucket for source; `s3:PutObject` for destination/storage roles

This integration allows you to back up and restore a MinIO bucket using Plakar’s built-in S3 connector. No extra package is needed: MinIO is supported natively as long as it is S3-compatible.

Snapshots are stored in a Kloset store, with full deduplication, encryption, and immutability. You can even use MinIO itself as the storage backend for your Kloset snapshots.

**Use cases:**
- Cold backup of on-premise object storage
  - Long-term archive and legal hold of MinIO buckets
  - Offline export via `.ptar`
  - Disaster recovery workflows

**Target technologies:**

- **Supported versions**: All versions of MinIO that are S3-compatible
  - **System compatibility**: Compatible with local, on-premise and cloud-hosted MinIO servers


## 2. Architecture

> Architecture diagram:
```plaintext
                             Viewer (CLI/UI)
                                 ↑
MinIO ← Source Connector → Kloset Store ←→ Storage Connector → MinIO
                                 ↓
                  MinIO ← Destination Connector → Compatible S3 targets
```

The Minio integration uses Plakar's S3-compatible connectors to interact with MinIO buckets.
It supports both reading from and writing to MinIO, allowing you to create snapshots and restore them as needed.
A Minio bucket can also serve as the storage backend for Kloset snapshots, enabling you to leverage MinIO's object storage capabilities directly.

**Components:**
- **Source Connector**: read-only connector to extract data from a bucket
- **Destination Connector**: write connector to restore data into a bucket
- **Storage Connector**: used to persist snapshots directly in a MinIO bucket
- **Viewer**: optional interface to explore the backup content

## 3. Installation

```bash
plakar version
```

Check that `s3` appears in the available connectors. No extra package installation is needed.

### Configure IAM permissions in MinIO

> Configuration example:
```bash
mc alias set local http://localhost:9000 minioadmin minioadmin
mc admin user add local plakar_user mysecretpassword
mc admin policy create local plakar-readonly-policy readonly-policy.json
mc admin policy attach local --user plakar_user plakar-readonly-policy
```

> Where readonly-policy.json contains:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:ListBucket"],
      "Resource": ["arn:aws:s3:::mybucket", "arn:aws:s3:::mybucket/*"]
    }
  ]
}
```

> To enable restore or use MinIO as storage backend, add `s3:PutObject` to the `Action` list.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::mybucket",
        "arn:aws:s3:::mybucket/*"
      ]
    }
  ]
}
```
MinIO supports fine-grained access control using IAM-style policies. You can assign permissions to users or service accounts using one of the following methods:

***Using the mc CLI (MinIO Client)***

This is the most common and scriptable method. You can:
- Create users with mc admin user add
- Define policies in JSON format compatible with AWS IAM
- Attach policies to users with mc admin policy attach

***Using the MinIO Console (Web UI)***

If you have enabled the admin console, you can:
- Create users via the Identity > Users panel
- Assign predefined or custom policies
- Review and manage access through the UI

## 4. Configuration

### 4.1 Source Connector

```bash
plakar config minio_src create minio_src
plakar config minio_src set minio_src type s3
plakar config minio_src set minio_src uri s3://localhost:9000/mybucket
plakar config minio_src set minio_src access_key minioadmin
plakar config minio_src set minio_src secret_access_key minioadmin
plakar config minio_src set minio_src use_tls false
```

This declares your MinIO bucket as a source. This configuration allows Plakar to read data from the specified MinIO bucket typically used for backups.

TLS is disabled here because test environments typically do not use it.
Plakar fully supports TLS, enable it if your MinIO instance requires secure connections.

### 4.2 Destination Connector

```bash
plakar config minio_dest create minio_dst
plakar config minio_dest set minio_dst type s3
plakar config minio_dest set minio_dst uri s3://localhost:9000/restore-bucket
plakar config minio_dest set minio_dst access_key minioadmin
plakar config minio_dest set minio_dst secret_access_key minioadmin
plakar config minio_dest set minio_dst use_tls false
```

This allows restoring snapshots back to a MinIO bucket.

### 4.3 Storage Connector

```bash
plakar config mini_store create minio_store
plakar config mini_store set minio_store type s3
plakar config mini_store set minio_store location s3://localhost:9000/plakar-kloset
plakar config mini_store set minio_store access_key minioadmin
plakar config mini_store set minio_store secret_access_key minioadmin
plakar config mini_store set minio_store use_tls false
```

This sets up your MinIO bucket to act as a durable Kloset backend.


## 5. Usage

### 5.1 Snapshot: make a backup

> Creates a snapshot from the MinIO source and stores it in the designated Kloset bucket.
```bash
plakar at @minio_store backup @minio_src
```

This command triggers a snapshot of the contents of the configured MinIO bucket.

Plakar uses a Source Connector to:
- List all objects in the bucket using the S3-compatible API, 
- Stream their content securely from the bucket, 
- Break them down into small, content-addressed chunks, 
- Deduplicate, compress and encrypt each chunk on the fly,
- Write them immutably to the Kloset store configured via minio_store.

The resulting snapshot includes:
- The object hierarchy (keys, prefixes) reconstructed as a virtual filesystem,
- All associated metadata (timestamps, content-type, size), 
- A manifest pointing to all chunks, forming a self-describing, verifiable, immutable backup.

The snapshot operation is stateless: only new or changed chunks are written to the store. Existing identical data is reused thanks to global deduplication.

While MinIO does not offer native snapshot isolation, Plakar captures a consistent view of the data as seen during the listing and streaming phase. For consistency-sensitive use cases, consider freezing writes or restoring from versioned buckets.


### 5.2 Inspection


> Lists available snapshots.
```bash
plakar at @minio_store ls
```

> Previews the content of a specific file.
```bash
plakar at @minio_store cat <snapshot-id>:/path/to/file
```

> Opens the interactive snapshot viewer.
```bash
plakar at @minio_store ui
```

Plakar represents your bucket objects as a navigable file and folder tree structure, providing an intuitive way to browse your snapshots. This abstraction makes it easy to search and explore your backed-up data, whether you're using CLI commands or the web-based user interface.


### 5.3 Restore

> Restores the snapshot back into MinIO.
```bash
plakar at @minio_store restore -to @minio_dst <snapshot-id>
```


> Restores the snapshot to a local folder.
```bash
plakar at @minio_store restore -to ./restore <snapshot-id>
```
Plakar offers versatile restoration capabilities for your MinIO snapshots. Data can be restored not only to other MinIO instances or any compatible object store, but also directly to file systems where bucket paths are represented as directories and objects as files. This flexibility ensures your data remains accessible regardless of your target infrastructure.


Parfait. Voici la section **5.4 Using MinIO as a storage backend**, à ajouter après la section 5.3 Restore :

---

### 5.4 Use MinIO as a storage backend

> To create a Kloset store directly in a MinIO bucket, use the following command:
```bash
plakar at s3://localhost:9000/plakar-kloset create
```

> You can now target a Minio kloset store using an alias:
```bash
plakar at @store_store create
```

> Then use it like any other store:
```bash
plakar at @minio_store ls
plakar at @minio_store backup /etc
plakar at @minio_store restore -to ./restored <snapshot-id>
```

This command initializes a new Kloset store directly inside the specified MinIO bucket.

Once created, you can use this bucket as a durable, S3-compatible backend for storing Plakar snapshots.

Plakar does not require any additional server-side software to operate. It treats the MinIO bucket as a passive blob store and uses it to persist the Kloset index, snapshot manifests, and data chunks.


Using MinIO as a Kloset store makes the integration completely self-contained. The same MinIO instance acts as both source/destination of data and as durable snapshot backend.


## 6. Integration-specific behaviors

Voici la même section **6.1 Limitations**, avec les tirets longs (`—`) remplacés par des tirets normaux (`-`), comme tu l'as demandé :

### 6.1 Limitations

While the integration is designed to be as complete and transparent as possible, certain elements of MinIO or S3-compatible storage systems are **not captured** during snapshot operations. This is due to either technical limitations of the API or the deliberate scope of Plakar, which focuses on user data and verifiable content.

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

### 6.2 Permissions required

> To perform a snapshot of a MinIO bucket using Plakar, the configured IAM user must have read access to both the bucket itself and its objects:

```json
{
  "Effect": "Allow",
  "Action": [
    "s3:ListBucket",
    "s3:GetObject"
  ],
  "Resource": [
    "arn:aws:s3:::mybucket",
    "arn:aws:s3:::mybucket/*"
  ]
}
```

> To restore data into a bucket or to persist snapshots using the storage connector, write permissions are required:
```json
{
  "Effect": "Allow",
  "Action": [
    "s3:PutObject"
  ],
  "Resource": [
    "arn:aws:s3:::mybucket/*"
  ]
}
```
You can combine both policies if the same user is used for reading, restoring, and storing.

### 6.3 Restore target considerations

Restore to a namespace or sandbox bucket first to validate contents.

### 6.4 Snapshot consistency and limitations

Plakar relies on the MinIO (S3-compatible) API to scan and snapshot bucket contents. However, object storage systems do not provide snapshot isolation guarantees, meaning that:
- If objects are added, modified or deleted during the snapshot process, the resulting snapshot may be inconsistent.
- There is no atomic point-in-time capture across all objects.

As a result:

The snapshot represents a consistent read of all objects at the time they were listed and fetched, but not a frozen image of the bucket at a single moment in time.

## 7. Troubleshooting

### Credential errors

```bash
plakar at @minio_store ls
```

If you see 403/401 errors, verify access keys, secret keys, and that `use_tls` is correctly set.

### Logs

```bash
docker logs <minio-container-name>
```

For MinIO in Docker, view logs here.


## 9. Backup strategy

The tipical backup strategy for Plakar with MinIO is the standard 3-2-1 rule:
- Keep **3** copies
- On **2** types of media
- At least **1** copy offsite or offline

Define your backup frequency and retention policy based on your business needs:
- **RPO**: how much data can be lost
- **RTO**: how fast you must restore


## 10. Appendix

- [Plakar CLI Reference](/docs/main)
- [Plakar Architecture (Kloset Engine)](https://www.plakar.io/posts/2025-04-29/kloset-the-immutable-data-store/)
- [MinIO Documentation](https://min.io/docs/minio/linux/index.html)

## 11. Frequently asked questions

**Does Plakar back up all object versions?**  
No. Plakar only includes the latest visible version of each object in the bucket at snapshot time. If versioning is enabled in MinIO, older versions are not backed up.

---

**Can I use Plakar to replicate between two MinIO buckets?**  

> This transfers a full snapshot from one bucket to another.
```bash
plakar sync -name <snapshot-id> @minio_prod to @minio_backup
```

Yes. Declare two Kloset stores pointing to your source and target MinIO buckets (e.g. `@minio_prod` and `@minio_backup`), then run a sync command.


---

**Does Plakar support TLS?**  
Yes. TLS is fully supported. In the examples, `use_tls` is disabled only for local development. If your MinIO instance uses a certificate, set `use_tls` to `true`.

---

**Can I restore data from MinIO to another provider (e.g., AWS, Azue, GCP, Wasabi, Scaleway, OVH)?**  
Yes. As long as the target is S3-compatible and correctly configured,
Plakar can restore snapshots using a destination connector well configured for this provider.

---

**How do I export a snapshot from MinIO to a `.ptar` archive?**  

> Use the following command to export:
```bash
plakar at @minio_store export <snapshot-id> -to ./export.ptar
```

> To restore it elsewhere:
```bash
plakar at ./export.ptar restore -to @target
```
Plakar lets you export any snapshot stored in MinIO into a portable .ptar file.
This is useful for cold storage, offline archives, or transferring backups between systems.

---