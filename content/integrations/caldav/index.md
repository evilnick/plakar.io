---
title: "CalDAV"
subtitle: "Sync and back up your calendar events with Plakar"
description: Import and export your calendar data from any CalDAV-compatible server using Plakar. Events are securely stored in `.ics` format and can be restored or synced back at any time.
technology_description: CalDAV is a protocol that allows clients to access and manage scheduling information on a remote server. It’s supported by popular providers like Fastmail, Nextcloud, iCloud, and Google Workspace (via third-party gateways).
categories:
- source connector
- destination connector
tags:
- caldav
- calendar
- backup
stage: testing
date: 2025-07-21
---
## Back up your calendar with Plakar

Plakar’s CalDAV integration lets you import and export calendar events to and from your Plakar repository. It supports any CalDAV-compatible provider and preserves events in full fidelity using the `.ics` format.

Use it to archive your schedule, sync events between systems, or push them back to your calendar server with no data loss.

## Why back up CalDAV calendars?

Calendars often contain critical personal or work-related data, but most providers offer limited options for long-term backups or restorations. Plakar gives you:

- Full snapshot history of your calendar
- The ability to recover deleted or modified events
- A secure and encrypted archive that works across providers

## Supported features

- Import from any CalDAV server (Nextcloud, Fastmail, iCloud, etc.)
- Export `.ics` files from Plakar back to the calendar server
- Support for multiple calendars in one operation
- Secure authentication (supports app passwords)
- Round-trip support with no data loss
- Preservation of metadata including:
    - Event title, time, recurrence rules
    - Attendees, organizer, UID, creation timestamps

## Limitations

- All accessible calendars and events are synced; per-calendar selection is not yet supported
- Export may fail if your account is read-only or lacks write permissions
- Events must be stored in Plakar in `.ics` format to be exported
- Does not yet support partial or time-based filtering
- Services requiring OAuth2 (like Google Calendar) are not supported directly; fill the OAuth2 provider configuration to use a third-party gateway

For usage details, see the [configuration guide]()

## Threats mitigated

- Loss of events due to accidental deletion
- Calendar corruption or sync errors
- Locked-in provider limitations (e.g. platform shutdowns)
- Incomplete manual exports

## Questions and support

Need help or want to report a bug?  
→ [Open an issue on GitHub](https://github.com/PlakarKorp/integration-caldav/issues/new)  
→ [Join our Discord](https://discord.gg/xjkbsWgyDZ) to get support from the team and community
