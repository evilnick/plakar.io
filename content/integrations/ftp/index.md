---
title: FTP
subtitle: Protect your FTP servers
description: Backup from a remote FTP server, restore to a remote FTP server !
technology_description: A filesystem stores and organizes files on devices like hard drives or mounted NAS volumes.
categories:
  - source connector
  - destination connector
  - viewer
tags:
- ftp
stage: "stable"
date: 2025-05-13
---

# FTP Integration

## Overview

The **FTP integration** provides support for the legacy, though still around, FTP protocol.

It can either backup from a public FTP server or restore to an authentication-less FTP server, a local NAS for example.

## Configuration

The **FTP integration** does not require any particular configuration.


## Example Usage

To back up the path `/pub/PlakarKorp` from FTP server located at `example.org`, you can use the following command:

```sh
$ plakar at /var/backups backup ftp://example.org/pub/PlakarKorp
```

To restore data to authentication-less FTP server located at `example.org`, use the following command:

```sh
$ plakar at /var/backups restore -to ftp://example.org/pub/PlakarKorp fc1b1e94
```

See the [QuickStart guide](https://docs.plakar.io/en/quickstart/index.html) for more examples.

## Questions, Feedback, and Support

Found a bug? [Open an issue on GitHub](https://github.com/PlakarKorp/plakar/issues/new?title=Bug%20report%20on%20Filesystem%20integration&body=Please%20provide%20a%20detailed%20description%20of%20the%20issue.%0A%0A**Plakar%20version**)

Join our [Discord community](https://discord.gg/uuegtnF2Q5) for real-time help and discussions.

## Q&A

**Do you store file metadata?**

The **FTP integration** cannot guarantee preservation of metadata,
as each FTP server may expose more or less information,
but will preserve all of the exposed metadata.
