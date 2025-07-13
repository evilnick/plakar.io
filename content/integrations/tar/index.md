---
title: Tar
subtitle: Import content from tarball files, produce tarballs from snapshots and klosets
description: Import content from tarball files, produce tarballs from snapshots and klosets
technology_description: A filesystem stores and organizes files on devices like hard drives or mounted NAS volumes.
categories:
  - source connector
  - destination connector
  - viewer
tags:
- stdio
stage: "coming soon"
date: 2025-05-13
---

# TAR Integration

## Overview

The **TAR integration** allows the 

This integration is designed to protect the data on your local hard drives, mounted NAS volumes, and SANs.

## Configuration

The **Stdio integration** does not require any special configuration.

## Example Usage

To back up, you can use the following commands which will copy the content of the stdio stream to a file named `passwd` within the snapshot:

```sh
$ plakar at /var/backups backup tar:foobar.tar.gz
```

Restore can be done to a file:

```sh
$ plakar at /var/backups restore -to tar:foobar.tar.gz fc1b1e94
```




See the [QuickStart guide](https://docs.plakar.io/en/quickstart/index.html) for more examples.

## Questions, Feedback, and Support

Found a bug? [Open an issue on GitHub](https://github.com/PlakarKorp/plakar/issues/new?title=Bug%20report%20on%20Filesystem%20integration&body=Please%20provide%20a%20detailed%20description%20of%20the%20issue.%0A%0A**Plakar%20version**)

Join our [Discord community](https://discord.gg/uuegtnF2Q5) for real-time help and discussions.

## Q&A

**Can you ingest command outputs ?**

Yes, the **Stdio integration** was designed to ingest data from `pg_dump`, `mysql_dump` and similar commands.
