---
title: SFTP
subtitle: Securely back up and restore remote servers over the secure SFTP protocol
description: Back up and restore directories to and from remote servers over SFTP.
categories:
  - source connector
  - destination connector
  - storage connector
  - viewer
tags:
  - SFTP
  - Linux
  - BSD
  - NAS
  - Synology
  - Qnap
stage: stable
date: 2025-05-13
---

# SFTP Integration

## Capabilities

- **Source Connector**: Seamlessly back up remote filesystems over SSH to and from a [Kloset repository](/posts/2025-04-29/kloset-the-immutable-data-store/).
- **Destination Connector**: Restore files and directories from a [Kloset repository](/posts/2025-04-29/kloset-the-immutable-data-store/) back to your remote server via SFTP.
- **Storage Connector**: Host a Kloset repository on a remote SFTP server, persisting snapshots directly on your target system.
- **Viewer**: Use the Integration Viewer to browse and visualize snapshots stored on an SFTP host.

## Overview

The **Plakar SFTP integration** combines both source and storage connectors in one package. It enables immutable, encrypted, verifiable backups and restores of remote directories over SSH/SFTP.

## Configuration

No special configuration is required to use SFTP with the `plakar` CLI. For convenience, you can define a named remote:

```bash
$ plakar config remote create mysftp
$ plakar config remote set mysftp location sftp://<host>/<optional_path>
```

Supported remote options:

- **`location`**: SFTP URL (`sftp://<host>/<path>`).
- **`insecure_ignore_host_key`**: Disable host key verification (default: false).
- **`identity_file`**: SSH identity file path; Plakar reads `~/.ssh/config` or falls back to `~/.ssh/id_rsa`, etc.

## Example Usage

### Using direct SFTP URL

1. **Back up** `/etc` from a remote server at `1.2.3.4`:

```bash
$ plakar at /var/backups backup sftp://1.2.3.4/etc
```

2. **Restore** a specific file back to the server:

```bash
# List available backups
$ plakar at /var/backups ls

# Restore a specific file
$ plakar at /var/backups restore -to sftp://1.2.3.4/tmp fc1b1e94:path/to/file.conf
```

### Using a named remote

Assuming you have in `~/.ssh/config`:

```text
Host myserver
  HostName 1.2.3.4
  User root
  IdentityFile ~/.ssh/id_rsa
```

Configure and use the remote:

```bash
$ plakar config remote create mysftp
$ plakar config remote set mysftp location sftp://myserver

$ plakar at /var/backups backup @mysftp/etc
$ plakar at /var/backups restore -to @mysftp/tmp fc1b1e94:path/to/file.conf
```

See the [QuickStart guide](https://docs.plakar.io/en/quickstart/index.html) for additional examples.

## Questions, Feedback, and Support

- Found a bug? [Open an issue on GitHub](https://github.com/PlakarKorp/plakar/issues/new?title=Bug%20report%20on%20SFTP%20integration&body=Please%20provide%20a%20detailed%20description%20of%20the%20issue.%0A%0A**Plakar%20version**)
- Join our [Discord community](https://discord.gg/uuegtnF2Q5) for real-time help.

## Q&A

**How do I configure username, port, or identity file?**  
Use your SSH config (`~/.ssh/config`) to set `HostName`, `User`, `Port`, and `IdentityFile` for your SFTP host.

**How can I avoid SSH passphrase prompts?**  
Use an `ssh-agent` to cache your passphrase; Plakar will connect via `$SSH_AUTH_SOCK` if available.

**Can I disable host key checks?**  
Yes, set `insecure_ignore_host_key=true` on your remote.

