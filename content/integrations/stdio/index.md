---
title: Stdio
subtitle: Protect any data exposed through standard input
description: Backup from your stdin, restore to your stdout !
technology_description: A filesystem stores and organizes files on devices like hard drives or mounted NAS volumes.
categories:
  - source connector
tags:
- stdio
stage: "coming soon"
date: 2025-05-13
hidden: true
---

# Stdio Integration

## Overview

The **Stdio integration** enables seamless backup and restoration of stdio streams to and from a [Kloset repository](/posts/2025-04-29/kloset-the-immutable-data-store/).

This integration is designed to protect the data on your local hard drives, mounted NAS volumes, and SANs.

## Configuration

The **Stdio integration** does not require any special configuration.

## Example Usage

To back up, you can use the following commands which will copy the content of the stdio stream to a file named `passwd` within the snapshot:

```sh
$ cat /etc/passwd | plakar at /var/backups backup stdin:passwd
```

Restore can be done to a file:

```sh
$ plakar at /var/backups restore fc1b1e94:passwd
```

or can also use an stdout stream:

```sh
$ plakar at /var/backups restore -to stdout:- fc1b1e94:passwd
```




See the [QuickStart guide](https://docs.plakar.io/en/quickstart/index.html) for more examples.

## Questions, Feedback, and Support

Found a bug? [Open an issue on GitHub](https://github.com/PlakarKorp/plakar/issues/new?title=Bug%20report%20on%20Filesystem%20integration&body=Please%20provide%20a%20detailed%20description%20of%20the%20issue.%0A%0A**Plakar%20version**)

Join our [Discord community](https://discord.gg/uuegtnF2Q5) for real-time help and discussions.

## Q&A

**Can you ingest command outputs ?**

Yes, the **Stdio integration** was designed to ingest data from `pg_dump`, `mysql_dump` and similar commands.
