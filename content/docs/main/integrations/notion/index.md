---
title: Notion Integration documentation
description: Backup content from your Notion workspace directly into Plakar — fast, structured, and API-driven.
technology_description: This integration uses the Notion API to retrieve structured JSON representations of your workspace and pages.
categories: 
- integrations
tags:
- notion
stage: "beta"
date: 2025-07-07
---

## Overview

> Restoration is not supported yet — but planned in a future release.

The **Notion integration** allows you to back up your Notion pages or entire workspace into a Plakar repository using the official Notion API.

All content is fetched via the API and stored as structured JSON, including page metadata, content blocks, and hierarchical relationships.


## Configuration

- A valid [**Notion API token**](https://www.notion.com/my-integrations) (`ntn_xxx`)
- The integration must be **shared with each page** you want to back up  
  → See [Notion’s developer guide](https://developers.notion.com/docs/getting-started#step-1-create-an-integration) for how to create and share integrations

## Example Usage

```sh
$ plakar at /var/backups create
$ plakar at /var/backups backup notion:// token=$NOTION_API_TOKEN
```

To create the Kloset repository at ```/var/backups``` and back up your Notion content, assuming the following env variable ```NOTION_API_TOKEN``` contained your shared API token, you can use the following command:



## Questions, Feedback, and Support

Found a bug? Suggestion? [Open an issue on GitHub](https://github.com/PlakarKorp/integration-notion/issues/new).

Join our [Discord community](https://discord.gg/xjkbsWgyDZ) for real-time help and discussions.