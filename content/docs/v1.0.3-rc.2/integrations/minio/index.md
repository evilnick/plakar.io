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
stage: stable
date: 2025-07-30
plakar_version: 1.0.2 or later
integration_version: 1.0.0
resource_type: object-storage
provides:
- source-connector
- destination-connector
- storage-connector
- viewer
---

This documentation assumes Plakar v1.0.2 is installed.

If you are running a newer version, the commands differ slightly as some of the **configuration commands have changed in v1.0.3**. Check the Changelog of v1.0.3 to adapt the commands accordingly.

## Introduction

Plakar's built-in MinIO integration includes three connectors:
* **Storage connector**: to host a Kloset store in a MinIO bucket.
* **Source connector**: to back up any MinIO bucket into an existing Kloset store.
* **Destination connector**: to restore from any Kloset store into a MinIO bucket.

**Use-cases for this integration**

* **Host a Kloset store in a MinIO bucket**, and use it to back up your local filesystem, remote servers, databases, or any other data source supported by Plakar.
* **Backup a MinIO bucket** to any Kloset store, for example hosted on the local filesystem, in a remote SFTP server, or even in another MinIO bucket.
* **Restore a snapshot from a Kloset store into a MinIO bucket**.

**Compatibility**

- This integration is built-in in Plakar and available in all versions. No extra package installation is needed.
- All versions of MinIO are supported.

## Installation

To interact with MinIO, Plakar uses the S3-compatible API of MinIO which is supported natively.

No additional packages or plugins are required.

> The `s3` connectors are built-in, and are always available in your Plakar installation
```bash
$ plakar version
plakar/v1.0.2

importers: fs, ftp, s3, sftp # <-- `s3` is listed here
exporters: fs, ftp, s3, sftp # <-- And here
klosets: fs, http, https, ptar, s3, sftp, sqlite # <-- And here
```

---

### Configure IAM permissions in MinIO

MinIO supports fine-grained access control using IAM-style policies. You can assign permissions to users or service accounts using one of the following methods:

***Option 1: Using the mc CLI (MinIO Client)***

This is the most common and scriptable method. You can:
- Create users with `mc admin user add`
- Define policies in JSON format
- Attach policies to users with `mc admin policy attach`

> Contents of policy.json — Remember to remove comments or the JSON will be invalid
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": ["s3:GetObject", "s3:ListBucket"],
      // To allow restoring into the bucket, you need to add the PutObject action
      // "Action": ["s3:GetObject", "s3:ListBucket", "s3:PutObject"],
      // To allow using the bucket as a Kloset store, you also need to give permissions to create the bucket
      // "Action": ["s3:GetObject", "s3:ListBucket", "s3:PutObject", "s3:CreateBucket"],
      "Resource": ["arn:aws:s3:::plakar-kloset", "arn:aws:s3:::plakar-kloset/*"]
    }
  ]
}
```

> Create the user `plakar_user` with the password `mysecretpassword` and assign the policy `plakar-policy` to it
```bash
$ mc alias set local http://localhost:9000 minioadmin minioadmin
$ mc admin user add local plakar_user mysecretpassword
$ mc admin policy create local plakar-policy policy.json
$ mc admin policy attach local --user plakar_user plakar-policy
```

---

***Option 2: Using the MinIO Console (Web UI)***

If you have enabled the admin console, you can:
- Create users via the `Identity` > `Users` panel
- Assign predefined or custom policies
- Review and manage access through the UI

---

The policy you should attach to the user depends on your use-case:
- **Read-only policy**: If you only need to back up a bucket, use a policy that allows `s3:GetObject` and `s3:ListBucket` actions. Write permissions are not needed.
- **Read-write policy**: If you need to restore a snapshot into a bucket or host a Kloset store in a bucket, use a policy that also includes `s3:PutObject`.


## Storage connector

The storage connector allows you to host a Kloset store in a MinIO bucket. This is useful if you want to use MinIO as a durable, S3-compatible backend for storing Plakar snapshots.

> Host a Kloset store in a MinIO bucket
> ![Host a Kloset to a MinIO bucket](./architecture-store.svg)

---

### Configuration

Use the commands `plakar store repository create <name>` and `plakar store repository set <name> <option> <value>` to configure a MinIO bucket as a Kloset store.

> Configure Plakar to use MinIO to host a Kloset store
```bash
$ plakar store repository create minio_store
$ plakar store repository set minio_store location s3://localhost:9000/plakar-kloset
$ plakar store repository set minio_store access_key minioadmin
$ plakar store repository set minio_store secret_access_key minioadmin
# Only if your MinIO instance does not use TLS
$ plakar store repository set minio_store use_tls false
```

**Configuration options**

| Option | Value |
|--------|-------------|
| `location` | Bucket location in the format `s3://<hostname[:port]>/<bucket-name>`
| `access_key` | Access key for the MinIO instance
| `secret_access_key` | Secret key for the MinIO instance
| `use_tls` | Whether to use TLS for the connection (default: `true`)
| `storage_class` | The storage class to use for objects in the bucket (default: `STANDARD`)

Once configured, use the syntax `plakar at @minio_store` to refer to this store in commands.

---

### Use your Kloset store hosted on MinIO

Once configured, use the Kloset store hosted in MinIO like any other Kloset store.

> Use the Kloset store hosted in MinIO like any other Kloset store
```bash
# Initialize a new Kloset store in the MinIO bucket
$ plakar at @minio_store create
# List all snapshots in the Kloset store
$ plakar at @minio_store ls
# Backup a local folder to the Kloset store
$ plakar at @minio_store backup /etc
# Backup any source connector to the Kloset store
$ plakar at @minio_store backup @myconnector
# Restore a file from a snapshot in the Kloset store
$ plakar at @minio_store restore <snapshot_id>:/etc/passwd
# Run the web UI to browse the Kloset store
$ plakar at @minio_store ui
```

## Source connector

The source connector allows you to create a snapshot of a MinIO bucket and store it in a Kloset store.

The Kloset store can be hosted in any of the supported backends by Plakar (filesystem, SFTP, …), including MinIO itself.

> Backup a MinIO bucket to a Kloset store
> ![Backup a MinIO bucket](./architecture-backup.svg)

---

### Configuration

Use the commands `plakar source remote create <name>` and `plakar source remote set <name> <option> <value>` to configure a MinIO bucket as a source for backups.

> Configure the source connector to back up a MinIO bucket
```bash
$ plakar source remote create minio_src
$ plakar source remote set minio_src location s3://localhost:9000/mybucket
$ plakar source remote set minio_src access_key minioadmin
$ plakar source remote set minio_src secret_access_key minioadmin
# Only if your MinIO instance does not use TLS
$ plakar source remote set minio_src use_tls false
```

**Configuration options**

The configuration options for the source connector are similar to those of the storage connector. See the [Storage connector](#storage-connector) section for details.

---

### Backup a MinIO bucket

To backup a MinIO bucket into a Kloset store, use `plakar at <store> backup @minio_src`.

> Create a Kloset store on the filesystem, and back up a configured MinIO bucket into it
```bash
# Initialize a new Kloset store on the local filesystem
$ plakar at ./my-store create
# Run the backup, assuming @minio_src is configured
$ plakar at ./my-store backup @minio_src
# Or, reference any Kloset store configured
$ plakar at @mystore backup @minio_src
```

## Destination connector 

The destination connector allows you to restore a snapshot from a Kloset store into a MinIO bucket.

The Kloset store location does not matter: it can be hosted on the local filesystem, in a remote SFTP server, or even in another MinIO bucket.

> Restore a snapshot to a MinIO bucket
> ![Restore a backup to a MinIO bucket](./architecture-restore.svg)

---

### Configuration

Use the commands `plakar destination remote create <name>` and `plakar destination remote set <name> <option> <value>` to configure a MinIO bucket as a destination for restores.

> Configure the destination connector to back up a MinIO bucket
```bash
$ plakar destination remote create minio_src
$ plakar destination remote set minio_src location s3://localhost:9000/mybucket
$ plakar destination remote set minio_src access_key minioadmin
$ plakar destination remote set minio_src secret_access_key minioadmin
# Only if your MinIO instance does not use TLS
$ plakar destination remote set minio_src use_tls false
```

**Configuration options**

The configuration options for the destination connector are similar to those of the storage connector. See the [Storage connector](#storage-connector) section for details.

---

### Restore a snapshot to a MinIO bucket

To restore a snapshot from a Kloset store into a MinIO bucket, use `plakar at <store> restore -to @minio_dst <snapshot-id>`.

> Restore a snapshot from a Kloset store into a MinIO bucket
```bash
$ plakar at ./my-store restore -to @minio_dst <snapshot-id>
# Or, reference any Kloset store configured
$ plakar at @mystore restore -to @minio_dst <snapshot-id>
```

---

## Limitations and considerations

### Plakar rich-syntax vs simple-syntax

Plakar supports two formats to specify the Kloset store location and the source or destination connector to operate on:
- The **rich syntax** with `plakar at @<store>` referencing a storage connector, `plakar backup @<source>` referencing a source connector, and `plakar restore -to @<destination>` referencing a destination connector.
- The **simple syntax** with `plakar at <store>` (eg. `plakar at ./mystore`), `plakar backup <source>` (eg. `plakar backup ./mybucket`), and `plakar restore -to <destination>` (eg. `plakar restore -to ./mybucket`).

The rich syntax is the only way to refer to the MinIO storage connector, source connector, or destination connector.

The reason is that only the rich syntax allows to provide the necessary configuration options (like `access_key`, `secret_access_key`, and `use_tls`) to connect to the MinIO instance.

---

### Store MinIO configuration

The MinIO source connector makes a snapshots of a bucket by listing all objects in the bucket and downloading their contents. It **does not** store the bucket configuration itself, such as policies, lifecycle rules, or versioning settings.

Also, if your MinIO instance uses server-side encryption (SSE) for storage-level encryption, the SSE configuration is not part of the data captured by Plakar. However, **Plakar performs its own encryption** before storing any chunk, ensuring data-at-rest security even without relying on SSE.

--- 

### Snapshot consistency and limitations

Plakar relies on the MinIO (S3-compatible) API to scan and snapshot bucket contents. However, object storage systems do not provide snapshot isolation guarantees, meaning that:
- If objects are added, modified or deleted during the snapshot process, the resulting snapshot may be inconsistent.
- There is no atomic point-in-time capture across all objects.

As a result:

The snapshot represents a consistent read of all objects at the time they were listed and fetched, but not a frozen image of the bucket at a single moment in time.

## Troubleshooting

### Credential errors

If you see 401/403 errors, verify access keys, secret keys, and that `use_tls` is correctly set.

> Display the current Plakar configuration
```bash
$ plakar config
```

---

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

Note that this is not specific to MinIO: two Kloset stores can be synchronized regardless of their underlying storage backend, even if they are different (e.g., one on MinIO and the other on a local filesystem).

---

**How to enable or disable TLS?**

Update the configuration option `use_tls` to `true` or `false` depending on whether your MinIO instance uses TLS. For local development, you probably want to disable TLS.

> Enable or disable TLS for the MinIO integration
```bash
# Disable TLS for the Kloset store
$ plakar store repository set minio_store use_tls false
# Disable TLS for the Source or Destination connector
$ plakar source remote set minio_src use_tls false
$ plakar destination remote set minio_dst use_tls false
```

---

**Can I restore data from MinIO to another provider (e.g., AWS, Azue, GCP, Wasabi, Scaleway, OVH)?**  

Yes. Configure the storage connector to point to the MinIO bucket, and set the destination connector to point to the target provider.

---

**How to generate a flat `.ptar` file from a MinIO store?**

Like any other Kloset store, you can use `plakar ptar` to export a Kloset store into a portable `.ptar` file.

> Export `@minio_store` to a `.ptar` file
```bash
$ plakar ptar -o ./export.ptar -k @minio_store
```

---

**How to restore a flat `.ptar` file to a MinIO bucket?**

Run the following command to restore a `.ptar` file into a MinIO bucket configured in the destination connector `@minio_dst`.

> Restore a `.ptar` file to a target
```bash
$ plakar at ./export.ptar restore -to @minio_dst
```

## Appendix

- [Plakar CLI Reference](/docs/main)
- [Plakar Architecture (Kloset Engine)](https://www.plakar.io/posts/2025-04-29/kloset-the-immutable-data-store/)
- [MinIO Documentation](https://min.io/docs/minio/linux/index.html)
