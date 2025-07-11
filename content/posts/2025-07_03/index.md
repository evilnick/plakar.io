---
title: "Cult of the Dead Content-Defined Chunking"
date: 2025-07-11T13:00:00+0100
authors:
- "gilles"
summary: ".tgz made sense in 1994, but today we need archiving that supports deduplication, encryption, S3, and zero trust. here’s why we built .ptar."
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
---

## TL;DR:


## Introduction

Every time your system moves, stores, or processes duplicated data, it’s doing work it doesn’t need to.
That means longer sync times, higher cloud egress fees, slower database queries, bloated containers, over-provisioned caches, and users waiting for things that should’ve been instant.
Multiply that by thousands of files, logs, messages, or binary blobs—and the inefficiency compounds rapidly.
The more data you touch, the more painful and expensive that duplication becomes.

We should know,
duplication is a nightmare when it comes to backing up data,
to transfer that data to a storage, remote or not, and keep it there for extended periods of time.

The solution ? Deduplication.

Deduplication isn’t just for backups. It’s for anything that handles recurring or repetitive data. Think: real-time collaboration tools, object storage systems, build artifact pipelines, CI/CD caches, logging infrastructures, messaging queues, document editors, and package registries. If your users upload revisions, move large files across services, or repeatedly generate similar outputs, you’re likely storing and reprocessing the same data again and again—sometimes byte-for-byte.

By deduplicating at the right layer—whether file-level, block-level, or chunk-level—you avoid wasting resources on what's already known. You free up CPU cycles for meaningful computation, reduce latency across your stack, shrink your operational footprint, and make your systems leaner and faster. And if you're paying per gigabyte, per operation, or per millisecond? You're literally buying back time and money.

So whether you're syncing user files, compressing API payloads, or optimizing data pipelines, deduplication is not an optional optimization—it's a design principle that pays off in every layer of the stack.


## Here comes the `go-cdc-chunkers` package

To help developers build smarter, leaner systems that avoid redundant work, we’re releasing [go-cdc-chunkers]()—an open-source, ISC-licensed library for high-performance Content-Defined Chunking in Go.

This package is designed to make it easy to slice data into variable-sized, content-aware chunks that are resilient to shifts and edits—perfect for deduplication, delta encoding, change tracking, and more.

Whether you're building synchronization tools, blob stores, data pipelines, or just want to avoid wasting time and compute on repeated data, go-cdc-chunkers gives you the primitives you need to chunk content efficiently and predictably.

It’s fast, memory-conscious, and production-ready, with a clean API that fits into streaming and batch workflows alike. We're publishing it not just as a component of our own infrastructure, but as a useful building block for any developer who wants to treat data as a first-class citizen—one that doesn’t need to be handled twice.


## What is deduplication ?

Deduplication is the process of finding similar subsets of data in a larger set.

Why do that, you ask ?

Well, usually to avoid performing redundant operations like expensive computations,
network transfers or even storing it multiple times:
by performing the operation once and reusing its result for all redundant chunks of data,
you save processing unit cycles,
and/or network bandwidth and/or storage I/O and space,
which ultimately translates in less resources consumed and less time spent on unnecessary tasks:
but also makes it possible to fit more operations in a bounded timeframe.


## The limits of compression in deduplication
It's not really deduplication per-se,
but it's worthy of an addition to this article if only to better understand how we came to more modern strategies.

Simply put,
a compressor processes a stream and tries to locate frequently occuring sequences of bytes to replace them with shorter sequences.
To help understand,
I'll craft a very simplified example to grasp the general idea:

```
I have a cat
a beautiful cat
an annoying cat but still a beautiful cat
she does not know she is a cat
but she does cat things
```

From this small text,
we identify the following tokens (bit encoding left for reference):

| Token     | Bit Encoding                                                                       |
| --------- | ---------------------------------------------------------------------------------- |
| I         | `01101001`                                                                         |
| (space)   | `00100000`                                                                         |
| have      | `01101000 01100001 01110110 01100101`                                              |
| a         | `01100001`                                                                         |
| cat       | `01100011 01100001 01110100`                                                       |
| \n        | `00001010`                                                                         |
| beautiful | `01100010 01100101 01100001`<br />`01110101 01110100 01101001`<br />`01100110 01110101 01101100` |
| an        | `01100001 01101110`                                                                |
| an        | `01100001 01101110`                                                                |
| but       | `01100010 01110101 01110100`                                                       |
| still     | `01110011 01110100 01101001 01101100 01101100`                                     |
| she       | `01110011 01101000 01100101`                                                       |
| does      | `01100100 01101111 01100101 01110011`                                              |
| not       | `01101110 01101111 01110100`                                                       |
| know      | `01101011 01101110 01101111 01110111`                                              |
| is        | `01101001 01110011`                                                                |
| things    | `01110100 01101000 01101001 01101110 01100111 01110011`                            |

So the following sentence:

```
a beautiful cat\n
```

is naturally encoded as this 128-bits sequence:

```
01100001 00100000 01100010 01100101   a be
01100001 01110101 01110100 01101001   auti
01100110 01110101 01101100 00100000   ful 
01100011 01100001 01110100 00001010   cat\n
```

But we can use another encoding than the natural one,
and for example decide to encode tokens instead of individual bytes,
using our own mapping table.

For example,
we can use one byte per word,
and as long as we have less than 256 words in our stream,
we could have:




We can then build a table to map long bit encoding sequences into smaller ones to compress,
and use the table to map the smaller ones to the long ones when we decompress.
Since some tokens occur more frequently,
by using a frequency table,
we can assign the smallest sequences to the most occuring tokens and  the long


Some of these tokens appear more frequently than others,
so if we remplace their bit encoding by one that's shorter,
we can encode them using less space:

Some words here have a higher frequency than others,
if we assign them a shorter code then we can encode the same sentence without taking the same space:
shorter codes for high-frequency tokens like space (0) and a (1100), longer codes for rare tokens like things (100001).

| Token     | Freq | Huffman Code |
| --------- | ---- | ------------ |
| (space)   | 20   | `0`          |
| \n        | 5    | `111`        |
| a         | 4    | `1100`       |
| cat       | 4    | `1101`       |
| she       | 3    | `1010`       |
| beautiful | 2    | `10110`      |
| does      | 2    | `10111`      |
| but       | 2    | `10000`      |
| i         | 1    | `100010`     |
| have      | 1    | `100011`     |
| an        | 1    | `100100`     |
| still     | 1    | `100101`     |
| not       | 1    | `100110`     |
| know      | 1    | `100111`     |
| is        | 1    | `100000`     |
| things    | 1    | `100001`     |


With such an encoding,
the sentence:

```
a beautiful cat\n
```

gets translated from these 128 bits:
```
a        (space)  b        e        a        u        t        i        f        u        l        (space)  c        a        t        \n
01100001 00100000 01100010 01100101 01100001 01110101 01110100 01101001 01100110 01110101 01101100 00100000 01100011 01100001 01110100 00001010
```

to these 18 bits:
```
a    (space) beautiful (space) cat  \n
1100 0       10110     0       1101 111
```



## A few deduplication strategy
Deduplication has evolved a lot throughout the years.

For this article, let's assume that we are backing up files with data in them.
The same holds true for objects in an object storage,
or blobs in a database,
we just need a "resource" holding data and files is the simplest to think of.


### Metadata matching

The first approach to deduplication is to look at metadata and decide if it's worth looking into.

For example,
assuming that I'm looking at a data set containing a file of 1TB that I have already processed in the past,
the metadata can inform me that the modification data has not changed,
that the size has not changed,
and depending on my level of confidence in these I can reuse my previous save and avoid having to go through this 1TB of data again.

This is very efficient and nice,
but since it doesn't look at the data at all...
rename the file, update the metadata without changing the content or copy the file so you have another identical copy of it aside with a different name,
and this breaks deduplication.


### Exact-content matching

With this approach,
a content is looked over to find an exact match.

This used to be done with a CRC checksum until cryptographic digests became the norm.

To perform deduplication,
data is passed through a function that produces a content identifier of some sort that can be recorded in an index.
When processing new data,
if the content identifier is already in the index,
then the data was already recorded and we can skip some heavier operations.

This has two short-comings:
- the entire file has to be read before knowing if it's a duplicate
- if a single bit is changed, the entire file is considered as not duplicate

So in our previous 1TB example,
we must first read 1TB of data and compute a digest out of it,
then only when we're done we know if the content was a duplicate or not.
If we just append a new-line to the file...
well, it's a new 1TB file.


### Fixed-size chunking

Now that's a much more interesting approach.

Instead of considering the data as a whole,
it is split into fixed-size chunks that are evaluated individually.
A 1TB file could for example be split into 1024 chunks of 1GB,
a digest could be computed for each of them and recorded in an index to mark them as seen.

When processing new data,
we would split it into chunks of 1GB and compute their digests to looked them up in the index:
if a digest is found,
the chunk is skipped as we already know it,
otherwise it means we either never saw it or at least a bit was altered so the chunk is processed and recorded for future runs to skip it.

The size of chunks vary and is very dependant on the use-cases,
and can even be assigned based on meta-data informations (ie: small chunks for .txt, big chunks for .mpeg).

This method has the advantage that chunks can be efficiently processed as the data does not have to be read byte-by-byte,
but using fixed-buffer reads that can take advantage of many optimizations.

The downside,
well,
that fixed-size method implies that data is seen as a global structure where data exists at static offsets...
add or remove one byte,
and the whole structure beyond that point is shifted,
the offset have not moved but all of the chunks are no longer aligned with their previous offsets and are considered new causing the deduplication to fall apart.


### Content-defined chunking

That's the most beautiful thing in the world.

Except for my kids...and my wife...and my cat.

Content-defined chunking builds upon the idea of fixed-size chunking:
split an input into smaller chunks so that the whole data doesn't have to be pushed in case of a single bit change...
but it doesn't use a global structure and static offsets.

Instead,
it uses a function to process the input and cut it into chunks of varying size...using the content to decide where to insert the cutpoints.
By doing this,
running the function on the same data produces the same cutpoints and therefore a serie of chunks identical between two runs,
but altering a single bit causes a cutpoint to shift and produce a chunk that's different from previous runs.
Since we compute the digests on chunks to record them in an index,
then the old chunks are found in the index and the new chunks aren't,
leading to new records.

If you're unfamiliar with this kind of magic,
you're going to wonder:

> well, once a cutpoint has been shifted, doesn't it shift all subsequent ones ?

And the answer is: no.

The function that processes the input only processes the last N bytes of data,
turns them into a digest of its own,
and looks at it to decide if it should insert a cutpoint or not.

Because this is a rolling digest over the last N bytes,
if a change has caused a new cutpoint to be inserted,
then after we have read N bytes from this new cutpoint...
we are producing the same stream of cutpoints as before.


## FastCDC
XXX


## Keyed CDC

Following a recent paper on attacks that target CDC algorithms,
not only did we introduce mitigations in plakar itself,
but we also introduced a Keyed CDC mode in our go-cdc-chunkers package and added support for a Keyed FastCDC implementation.

Long story short,
for its operations FastCDC relies on a so-called Gear table which contains a set of values that are injected in its decision making for cutpoints.
The values need to be generated randomly to avoid biases,
but once generated they need to remain the same between runs so that identical data produce the same cutpoints:

```go 
// chunkers/kfastcdc/fastcdc_precomputed.go
var G [256]uint64 = [256]uint64{
  0x4d65822107fcfd52,
  0x78629a0f5f3f164f,
  0xd5104dc76695721d,
  [...]
  0x7e23bc6fc8214b8a,
  0xeadaea4753b428d7,
  0xaa80d0564cf20a65,
}
```

These values are supposedly not sensitive,
but they have the disadvantage that cutpoints are predictable by everyone:
if I share a file with you,
you can determine what the cutpoints for that file will be on my machine,
and depending on your use of CDC this can lead to some privacy concerns.

With Keyed FastCDC,
a key is provided upon chunker initialization.
It is used to setup a Keyed BLAKE3 hasher from which an alternate Gear table is derived,
a Keyed Gear table if you will.
Using the same key produces the same Keyed Gear table with similar cutpoints between two runs,
whereas using a different key produces a different table and therefore different cutpoints:
you can no longer predict what the cutpoints for a given file will be for someones' chunker using a key you don't know.

The good part is that this Keyed mode bears _no performance cost_,
it is a fast computation that's only done at chunker initialization,
essentially free.

We are unaware of another implementation providing a similar mechanism,
so...straight from Plakar Korp's lab, here's some R&D for you :-)


## Conclusion

Our package is opensource and distributed under the permissive ISC-license,
so it is free for you to use in any application,
including commercial ones.

Feel free to hop in our Discord channel and ask for help if you want to integrate it somewhere,
make improvements to it,
or add support for new algorithms.

It can be used for a wide range of use-cases,
so we are curious to see what you can build with it !
