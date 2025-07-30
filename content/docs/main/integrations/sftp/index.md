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
```
plakar version
```
***Verify SFTP Support:***
Check that SFTP is listed in your available connectors by running:

```bash
importers: fs, ftp, s3, sftp
exporters: fs, ftp, s3, sftp
klosets: fs, http, https, ptar, s3, sftp, sqlite
```
You should see `sftp` listed under **importers**, **exporters**, and **klosets**, for example:

This verifies integration is now ready for use.

## 4. Configuration

No special installation is required to use SFTP with Plakar.

### 4.1 Using direct SFTP URLs

```bash
# Create a Kloset store on a remote SFTP server
plakar at sftp://user@1.2.3.4:/var/backups create

# Backup a remote directory into that store
plakar at sftp://user@1.2.3.4:/var/backups backup sftp://user@1.2.3.4:/etc
```
Direct URLs are the simplest way to use SFTP with Plakar:

Direct URLs are fully self‑contained and require no prior configuration, ideal for ad‑hoc or one‑off operations.

### 4.2 Using a named remote

```
Host myserver
    HostName 1.2.3.4
    User root
    IdentityFile ~/.ssh/id_rsa
```

For frequent backups or easier command reuse, define a named remote.
If you have the server in `~/.ssh/config`:

```
plakar config remote create mysftp
plakar config remote set mysftp location sftp://myserver
```

Configure the remote in Plakar:


## 5. Usage

### 5.1 Snapshot: make a backup

```
plakar at @mysftp_store backup @mysftp/etc
```
Back up `/etc` from a remote server using a named remote:

Plakar streams files over SSH/SFTP, deduplicates, compresses, encrypts, and stores immutable chunks in the Kloset store.

### 5.2 Inspection

```
plakar at @mysftp_store ls
```
List snapshots in the Kloset store:

```
plakar at @mysftp_store cat <snapshot-id>:/etc/ssh/sshd_config
```
Preview a file in a snapshot:

```
plakar at @mysftp_store ui
```
Open the interactive viewer:

### 5.3 Restore

```
plakar at @mysftp_store restore -to @mysftp/tmp <snapshot-id>
```
Restore a snapshot to a remote directory using a named remote:

```
plakar at @mysftp_store restore -to ./restored <snapshot-id>
```
Restore a snapshot to the local filesystem:

### 5.4 Use SFTP as a storage backend
```
plakar at @mysftp_store create
```
Create a Kloset store on the remote SFTP server:
```
plakar at @mysftp_store backup @mysftp/etc
```
Backup a remote directory into that store:
```
plakar at @mysftp_store restore -to ./restored <snapshot-id>
```
Restore a snapshot locally:
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