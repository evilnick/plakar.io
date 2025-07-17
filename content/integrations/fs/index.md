---
title: Filesystem
subtitle: Protect your local files and folders with immutable, encrypted, verifiable backups
description: Back up and restore directories from local or mounted filesystems.
categories: 
- source connector
- destination connector
- viewer
- storage connector
tags:
- Filesystem
- NFS
- SMB
- NAS
- SAN
- NTFS
- EXT4
stage: stable
date: 2025-05-13
---


# Filesystem Integration

## Capabilities

- **Source Connector**: Seamlessly back up filesystems to and from a [Kloset repository](/posts/2025-04-29/kloset-the-immutable-data-store/). Protect data on local hard drives, mounted NAS volumes, and SANs.
- **Destination Connector**: Seamlessly restore filesystems to and from a [Kloset repository](/posts/2025-04-29/kloset-the-immutable-data-store/).
- **Storage Connector**: Host your Kloset repository on any local or mounted filesystem, leveraging the same integration package to persist snapshots in place.
- **Viewer**: Use the Integration Viewer to browse and visualize snapshots taken from local filesystems.

## Overview

The **Plakar Filesystem integration** combines both source and storage connectors in one package. It enables immutable, encrypted, verifiable backups of directories from your local or mounted filesystems.

## Configuration

No special configuration is required for the Filesystem package.

## Example Usage

1. **Create a Kloset repository** on your local filesystem at `/var/backups`:

```bash
$ plakar at /var/backups create
```

2. **Back up** the path `/etc` from your filesystem:

```bash
$ plakar at /var/backups backup /etc
```

3. **Restore** data from the same repository to the current directory:

```bash
# List available backups
$ plakar at /var/backups ls

# Restore a specific file
$ plakar at /var/backups restore fc1b1e94:path/to/file.docx

# Restore the full backup
$ plakar at /var/backups restore fc1b1e94
```

See the [QuickStart guide](https://docs.plakar.io/en/quickstart/index.html) for more examples.

## Questions, Feedback, and Support

- Found a bug? [Open an issue on GitHub](https://github.com/PlakarKorp/plakar/issues/new?title=Bug%20report%20on%20Filesystem%20integration&body=Please%20provide%20a%20detailed%20description%20of%20the%20issue.%0A%0A**Plakar%20version**)
- Join our [Discord community](https://discord.gg/uuegtnF2Q5) for real-time help and discussions.

## Q&A

**Do you store file metadata?**  
Yes, the Filesystem package preserves file metadata, including permissions, timestamps, and ownership, ensuring restored files retain their original attributes.

**Do you follow symlinks?**  
Symlinks are backed up as symlinks only; their targets are not followed by default to avoid unintentional exposure of sensitive files outside the intended directory.

**Do you store extended attributes?**  
Yes, the Filesystem package preserves extended attributes (xattrs) of files and directories.
