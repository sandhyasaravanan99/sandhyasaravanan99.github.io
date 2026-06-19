---
title: "ORAM Series: PathORAM"
summary: "Encrypting your data is not enough. You need Oblivious RAM, and PathORAM was one of the first practical ones."
math: true
---

## Why hiding the data isn't enough

Suppose you've encrypted every block of data you store on an untrusted server. The server can't read your bytes. But it *can* watch which **block locations** you read and write, and in what order. This sequence of block locations aka the **access pattern**, leaks more than you'd think. For example, suppose the server holds an encrypted medical database with an index on the disease column, so each disease's entry sits in its own block. When the hospital app looks up a patient's diagnosis, it probes that disease's block. The entries are encrypted, but the index has quietly turned "which disease" into "which block", and which block is exactly what the server sees. Knowing that diabetes is the most common diagnosis, hypertension the next, and so on, the server just counts how often each block is read: the most-read block is diabetes, the next is hypertension, and the whole block-to-disease mapping falls out, no decryption needed. Now every patient's lookup reveals their diagnosis.

![The ORAM setting: a trusted client reads and writes encrypted data on an untrusted server.](/assets/img/blog/oram-setting.svg){: .post-image }

Oblivious RAM (ORAM) is the primitive that closes this gap: it makes the access pattern seen by the server *independent* of the logical addresses you actually requested.

<!-- TODO: a concrete leakage example — e.g. binary search vs linear scan, or a database index lookup — to make "access pattern leaks" visceral. -->

## What ORAM promises (and what it costs)

<!-- TODO: state the security goal precisely: for any two equal-length request sequences, the physical access patterns are computationally indistinguishable. -->

<!-- TODO: the cost axis — overhead measured as physical accesses per logical access, client storage, server storage. Set up why we care about the log factors Path ORAM achieves. -->

## The setup: client, server, and a tree

<!-- TODO: introduce the binary tree of buckets on the server, each bucket holding Z blocks, N logical blocks, tree of height L = log N. -->

<!-- TODO: client-side state: the position map (block -> leaf) and the stash. -->

## The invariant that makes it work

The whole scheme hangs on a single invariant:

> Every block is stored somewhere on the path from the root to the leaf it is currently assigned to.

<!-- TODO: unpack why this invariant is both easy to maintain and exactly what makes accesses oblivious — every access reads a full root-to-leaf path, which looks uniformly random to the server. -->

## The access algorithm

<!-- TODO: walk through read/write as one operation:
  1. look up the leaf x for block a in the position map
  2. remap a to a fresh random leaf
  3. read the entire path to x into the stash
  4. update the block if writing
  5. write the path back, greedily pushing blocks as deep as the invariant allows
-->

## Why it stays oblivious

<!-- TODO: the key argument — because every block gets a fresh random leaf on each access, the path read on any access is uniform and independent of history. Tie back to the security definition. -->

## The stash, and why it doesn't overflow

<!-- TODO: intuition + the result that stash overflow probability is negligible for Z >= some small constant. Cite the Path ORAM analysis. -->

## Costs, recursion, and where this goes next

<!-- TODO: bandwidth overhead O(log N) blocks per access; the position map is itself O(N) and gets stored recursively in a smaller ORAM, giving O(log^2 N) overhead. -->

<!-- TODO: teaser for the next post in the series. -->

## References

<!-- TODO: Stefanov et al., "Path ORAM: An Extremely Simple Oblivious RAM Protocol" (CCS 2013). -->
