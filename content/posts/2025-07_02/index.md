---
title: "Teach Plakar How To Backup"
date: 2025-07-09T20:00:00+0100
authors:
- "gilles"
summary: "want to craft a ptar archive but you don't need a full-fledged backup solution ? here comes kapsul, our ptar-specific tool, providing all you need from building to restoring and inspecting."
categories:
 - technology
 - backup
featured-scope:
 - hero-page
tags:
 - plakar
 - ptar
 - backups
 - archive
 - kapsul
---

## TL;DR:

Wouldn't it be nice if you could backup pretty much anything service  you use with the same tool ?

I mean,
having snapshots of your filesystem,
but also of your Google Drive,
your e-mail account at work and why not the calendar setup with your partner to organize daily chores.


---

## Short summary

> @himynameisnico — 7/10/25, 8:15 AM
> Hello guys ! how are you ? It's possible use plakar with OneDrive?

> @riki — 7/11/25, 2:12 AM
> Hi guys, is it possible to use Google drive as a repo for Plakar?

By now,
if you read our previous articles,
you should know that Plakar is built upon Kloset - our engine that abstracts data storage into full-featured snapshots.

One of the features of Kloset is to provide programming interfaces to extend its support.

{{<github repo="plakarKorp/go-kloset-sdk">}}

---
## What are integrations ?

An integration is a package that provides a collection of connectors to access, backup, analyze, inspect and restore specific resources.

For a more specific example,
our s3 integration provides a storage connector to store a Kloset on an S3 bucket,
but also a source connector to backup an S3 bucket and a destination connector to restore to an S3 bucket.

Kloset defines a set of programming interfaces,
and an integration can implement one or many of these interfaces,
allowing Plakar to support new resources to backup.

The nicest part about this ?

The integrations are standalone executables,
interfacing with Plakar through inter-process communication:
you can write your integrations and are not required to involve us in the process if you follow the interfaces.

You can write opensource integrations,
release their code and contribute these to the community...
or you can find a niche service,
build a closed-source integration for it,
and sell your integration knowing that the complex parts of the backups are handled by our engine and that you already have a user-base that could use it.


---
## Let's go technical now ...

Integrations are ptar archives containing a manifest and standalone executables.

The manifest describes what services the integration provides (ie: a store connector, a source connector, ...),
and these services are really (one or many) standalone executables that expose a GRPC server over a socketpair.




The reason we went for that model is tied to security,
simplicity and flexibility.

Security-wise,
each integration process has its own memory space,
can't access secrets consumed by other processes,
if the integration has a nasty bug that allows a takeover of the process...
well it's limited to that process.



---

## Conclusion

A week ago,
we released `ptar` as a `plakar` subcommand and some people thought it should be a separate tool.
A week later,
you have a new standalone tool, `kapsul`, that's ISC-licensed, open-source, free,
and that lets you build your own little secure vaults in seconds.

In case we need to state the obvious,
we care about users <3

![](BYE.png)
