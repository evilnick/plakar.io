---
title: RCLONE
subtitle: Save and restore your cloud data with rclone for Plakar
description: Back up and restore your data from and to a cloud provider (e.g., Google Drive, Google Photos, Onedrive) using the Plakar Rclone integration.
technology_description: Rclone is a command-line program to manage files on cloud storage. It supports various providers like Google Drive, Google Photos, OneDrive, Dropbox, and more.
categories: 
  - integrations
tags:
  - Rclone
  - Backup
  - Restore
  - Google Drive
  - Google Photos
  - Icloud
  - Onedrive
  - Opendrive
stage: test
date: 2025-07-11
---

# Rclone Integration

## Overview

**Rclone** is a command line program to manage files on **cloud storage**. The Plakar Rclone integration enables seamless backup and restoration of a cloud service provided by Rclone to and from a [Kloset repository](/posts/2025-04-29/kloset-the-immutable-data-store/).

> **Why use Rclone with Plakar?**
>
> Rclone is here to simplify the connection between Plakar and your cloud storage. It allows you to back up and restore data from various cloud providers like Google Drive, Google Photos, OneDrive, Dropbox, Amazon S3, Box, Mega, pCloud, WebDAV, and many more — directly to your Kloset repository. 
> Please note that Plakar currently supports only a subset of the cloud services supported by Rclone. See the **Q&A** section below for the list of providers currently supported.

## Configuration

### Temporary Requirement

*The Rclone connector is actually in test. So the config setup is temporary.*

The Rclone integration requires you to have **Rclone** installed on your system. You can install it by following the instructions on the [Rclone installation page](https://rclone.org/install/).

### Temporary Configuration

To create a new Rclone remote configuration, you need to create an Rclone conf. To do so, run the following command:

```bash
$ rclone config
```

And follow the prompts to create a new remote configuration. For the moment Plakar only supports `google drive`, `opendrive`, `iclouddrive`, and `onedrive` as remote configurations.

Once you have created the remote configuration, you will find it in the `~/.config/rclone/rclone.conf` file.

This will look like this:

```ini
[myremote]

type = ...
token = {"..."}
drive_id = ...
drive_type = ...
```

(the fields will be different for each provider)

You need to copy the remote configuration name (in this case `myremote`) and use it in the Plakar configuration.
You need to translate it into YAML format, so it will look like this in the Plakar configuration:

```yaml
myremote:
  type: ...
  token: '{"..."}' # Here you just need to add the quotes around the brackets
  drive_id: ...
  drive_type: ...
```

Before being able to use the Rclone integration, you need to add one last field to the configuration, which is the `location` field.
You need to add one of the following values, depending on the provider you are using:

```yaml
location: 'onedrive://'
location: 'opendrive://'
location: 'drive://'
location: 'iclouddrive://'
```

**Iclouddrive does not include iCloud Photos**

### Example Configuration

Here is an example of a complete Plakar configuration for Rclone:

```yaml
myremote:
  type: onedrive
  token: '{"..."}'
  drive_id: ...
  drive_type: business
  location: 'onedrive://'
```

## Example Usage

Once configured, you can back up or restore data using the Rclone integration with Plakar.

To create the Kloset repository at `/var/backups` and back up the OneDrive cloud configured as `myremote`, run the following command:

```bash
$ plakar at /var/backups create
$ plakar at /var/backups backup @myremote
```

To restore the data from the same Kloset repository to the `myremote` cloud, run:

```bash
# List available backups
$ plakar at /var/backups ls

# Restore a specific file
$ plakar at /var/backups restore -to @myremote fc1b1e94:path/to/file.docx

# Restore the full backup
$ plakar at /var/backups restore -to @myremote fc1b1e94
```

See the [QuickStart guide](https://docs.plakar.io/en/quickstart/index.html) for more examples.

## Questions, Feedback, and Support

Found a bug? [Open an issue on GitHub](https://github.com/PlakarKorp/plakar/issues/new?title=Bug%20report%20on%20Rclone%20integration&body=Please%20provide%20a%20detailed%20description%20of%20the%20issue.%0A%0A**Plakar%20version**)

Join our [Discord community](https://discord.gg/uuegtnF2Q5) for real-time help and discussions.

## Q&A

**Q: Which cloud providers are supported by Plakar’s Rclone integration?**

A: Currently, Plakar supports Google Drive, Google Photos, OneDrive, OpenDrive, and iCloud Drive (excluding iCloud Photos). Other providers supported by Rclone will be added in the future.

**Q: Do I need to install Rclone separately?**

A: At the moment yes, you must install Rclone on your system and configure your remotes before using them with Plakar.

**Q: Which Providers are supported by Rclone?**

A: Rclone supports a wide range of providers, please refer to the [Rclone documentation](https://rclone.org/overview/) for the complete list. However, Plakar currently does not support all of them.
