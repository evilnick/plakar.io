---
title: SFTP integration
description: Securely back up and restore remote directories over SFTP with Plakar — encrypted, verifiable, and portable.
technology_description: This integration leverages the SFTP protocol over SSH to securely transfer and verify remote directories into a Plakar Kloset for backup and restoration.
categories:
  - integrations
tags:
  - sftp
  - ssh
  - secure file transfer
  - remote directories
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
date: 2025-07-30
plakar_version: ">=1.0.3"
integration_version: "0.1.0"
resource_type: filesystem
provides:
  - source connector
  - destination connector
  - storage connector
---

## 1. Introduction
This integration enables **backing up, restoring, and storing** remote directories using Plakar’s built-in SFTP connector over SSH. SFTP is supported natively, requiring only that the target system has SSH and SFTP access enabled.

Snapshots are stored in a Kloset store with full deduplication, encryption, and immutability, whether hosted locally or on an SFTP-accessible remote server. All data transfers are encrypted end-to-end, and Plakar automatically verifies each snapshot to ensure data integrity.

**Use cases:**
- Secure, encrypted backups of remote Linux, BSD, or application servers
- Hosting Kloset stores on remote SFTP servers for offsite or air‑gapped backup
- Disaster recovery and rapid restoration of server directories over SSH
- Automated compliance backups of sensitive or regulated data
- Centralized archiving of distributed infrastructure into a single Plakar Kloset

**Target technologies:**
- Supported versions: Any system with OpenSSH 7.0+ or equivalent SFTP implementation
  - System compatibility: Compatible with on-premise, cloud-hosted, or hybrid Linux servers.
 - Unsupported variants:
    - Legacy SSH servers below 7.0
    - Proprietary or vendor-specific SFTP implementations that do not follow standard SSH protocol

## 2. Architecture

**Components provided:**

- **Source Connector**: Extracts files and directories from remote servers over SFTP for secure, verifiable backups into a Kloset store.
- **Destination Connector**: Restores Plakar snapshots to the original SFTP server or any compatible SSH-accessible resource.
- **Storage Connector**: Supports hosting Kloset stores on SFTP servers, enabling offsite, encrypted, and air‑gapped snapshot storage.
- **Viewer**: Accessible via the Plakar CLI or UI, allowing users to browse, search, and verify snapshots stored on SFTP-hosted Kloset stores.

## 3. Installation
This integration is bundled natively with Plakar. No additional package installation is required.

***Verify SFTP Support:***
Check that SFTP is listed in your available connectors by running the command ```plakar version```.

```bash
importers: fs, ftp, s3, sftp # <--- SFTP LISTED
exporters: fs, ftp, s3, sftp # <--- SFTP LISTED
klosets: fs, http, https, ptar, s3, sftp, sqlite # <--- SFTP LISTED
```
You should see `sftp` listed under **importers**, **exporters**, and **klosets**, for example:

This verifies integration is now ready for use.

## 4. Configuration

No special installation is required to use SFTP with Plakar.

### 4.1 Using direct SFTP URLs

```bash
# Create a Kloset store on the local SFTP server
plakar at sftp://sftpuser@localhost/uploads create

# Backup data into the SFTP store
plakar at sftp://sftpuser@localhost/uploads backup /home/<user>/Documents
```
Direct URLs are the simplest way to use SFTP with Plakar:

Direct URLs are fully self‑contained and require no prior configuration, ideal for ad‑hoc or one‑off operations.

### 4.2 Configure and use a named remote

```bash
plakar config remote create mysftp
plakar config remote set mysftp location sftp://local-sftp/uploads

# Backup to the named SFTP remote
plakar at @mysftp backup /home/<user>/Documents
```
This allows using `sftp://local-sftp` instead of the full `sftp://sftpuser@localhost.`

> (optional) create an SSH host alias
```
Host local-sftp
    HostName localhost
    User sftpuser
    IdentityFile ~/.ssh/id_ed25519_plakar
```


### 4.3 Ensure passwordless SSH access

```bash
#  Must connect without a password for Plakar to work reliably.
ssh-keygen -t ed25519 -f ~/.ssh/id_ed25519_plakar
sftp -i ~/.ssh/id_ed25519_plakar sftpuser@localhost
```

> Test passwordless login with
```
sftp -i ~/.ssh/id_ed25519_plakar sftpuser@localhost
```


## 5. Setup backup model with SFTP Integration

When using Plakar with an SFTP backend, backups can be organized using two **operational models**

- **Push Model:**  Each source server **initiates** its own backup to the SFTP storage.
- **Pull Model:** A central backup server **collects** data from sources and stores it in a Kloset repository.

Plakar’s flexibility comes from its **connector architecture**:
- **Source Connectors:** Where data originates (local paths, SFTP servers, or other remotes)
- **Storage Connectors:** Where the Kloset repository resides (local FS, SFTP, S3, MinIO)
- **Destination Connectors:** Where data is restored (local FS, SFTP, or cloud storage)

The separation allows administrators to choose between distributed (push) or centralized (pull) backup strategies, depending on infrastructure scale and operational preferences.

## 5.1  Push Model (Example)
```bash
# On each source server, configure a repository pointing to the SFTP storage:
plakar config repository create mybackup
plakar config repository set mybackup location sftp://backup.tld.com/var/backups

# Trigger the backup process independently from the source server:
plakar at @mybackup backup /home
```
In the push model, each source server initiates its own backup to the central SFTP Kloset repository.

## 5.2 Push Model (Example)
```bash
# On backup.tld.com (central node)
plakar config remote create server1
plakar config remote set server1 location sftp://server1.tld.com

plakar config remote create server2
plakar config remote set server2 location sftp://server2.tld.com

# Pull data from all servers
plakar at /var/backups backup @server1
plakar at /var/backups backup @server2
```
In the push model, each source server independently initiates its own backup to the central SFTP Kloset repository. 

Backup server orchestrates all pulls. This simplifies retention management, monitoring, and scheduling centrally.


### 5.3 Restoring Snapshots (Destination Connectors)

```bash
# Restore locally
plakar at sftp://sftpuser@localhost/uploads restore -to ./restored <snapshot-id>

# Restore to SFTP
plakar at sftp://sftpuser@localhost/uploads restore -to sftp://sftpuser@localhost/restored <snapshot-id>

# Restore to S3 (example)
plakar at sftp://sftpuser@localhost/uploads restore -to s3://my-bucket/restored <snapshot-id>
```

Plakar restores snapshots to any supported destination connector:
- Local filesystem
- SFTP paths
- S3 or MinIO buckets



## 6. Integration-specific behaviors
### 6.1 Limitations

**The following are not included in SFTP snapshot backups:**
- System-wide configuration (e.g., SSH server settings, firewall rules)
- User accounts and permissions on the remote host
- Active processes or running services
- Monitoring or telemetry configurations
- Any server-side scripts or binaries outside the backed-up directories

**Only the following are backed up:**
- Files and directories accessible via the configured SFTP path
- File metadata such as timestamps, permissions, and sizes


### 6.2 Restore behavior specifics

**Recommended best practices for restoring SFTP snapshots:**
- Always restore to a temporary or namespaced directory (e.g., `sftp://myserver/restore-preview/`) to avoid overwriting live data directly
- Verify restored data integrity and completeness before promoting it to production paths
- Use Plakar’s verification commands or checksum tools to validate snapshot correctness
- Consider restoring snapshots to local filesystem first for inspection before pushing to remote servers

## 7. Troubleshooting

**Credential / SSH errors**
If you see `Permission denied` or `Connection refused`:
- Verify SSH key, username, and remote host configuration.

**Host key issues**
**Host key verification failed**, Add the host to `~/.ssh/known_hosts` or set `insecure_ignore_host_key=true`.

**Permission denied on paths**
Ensure the SFTP user has read/write access to the source or destination directories.

## 8. Backup Strategy
- Schedule daily or weekly snapshots with cron or timers.
- Keep multiple snapshots for rollback and auditing.
- Store backups on a separate SFTP store or local FS for safety.
- Periodically verify snapshots with `plakar ls` or `plakar verify`.

## 9. Appendix
[Plakar CLI Reference](https://www.plakar.io/docs/main/)
[Plakar Architecture (Kloset Engine)](https://www.plakar.io/posts/2025-04-29/kloset-the-immutable-data-store/)
[OpenSSH / SFTP Documentation](https://man.openbsd.org/sftp.1)

## Frequently Asked Questions

**How do I configure username, port, or identity file?**
Use your SSH config (**~/.ssh/config**) to set **HostName**, **User**, **Port**, and **IdentityFile** for your SFTP host.

**How can I avoid SSH passphrase prompts?**
Use an **ssh-agent** to cache your passphrase; Plakar will connect via **$SSH_AUTH_SOCK** if available.

**Can I disable host key checks?**
Yes, set **insecure_ignore_host_key=true** on your remote.