---
layout: post
title: "An Introduction to the Cache Coherence Problem"
description: "How does a computer ensure a value in main memory and all of its caches agree?"
date: 2017-05-31
tags: [computer architecture]
comments: true
share: true
---

As I was finishing up the spring semester in school, I have also been asking around to find a professor who would be interested in either sponsoring an Independent Study or working with me on research on systems programming or operating systems. I have found one professor who is interested in doing research with me on his current project on TLB coherence[^1] and cache coherence. I have been reading papers and a textbook on the topic and I find it pretty interesting, so that is what this blog post will be on.

[^1]: As in [this paper](https://users.cs.duke.edu/~alvy/papers/hpca10_unitd.pdf), I will be referring to the maintenance of coherence between the TLB and page tables as "TLB coherence" because it is very similar to cache coherence, and not "TLB consistency" as is usually used.


## What is cache coherence?

In many modern architectures, multiple CPU cores will have their own caches of system memory. A big problem these architectures face is maintaining coherence between these caches, meaning all of the cores are on the same page on what value at a certain address is the most up-to-date value. This is a complicated issue because each core may update a value in their cache at any time, but may not immediately update the value in main memory or may not immediately notify other cores to update their own caches.

In the example below, cores 1 and 2 read a value `5` from main memory, then later core 1 updates this value in its own cache and in main memory to `10`. Detecting that the value in Core 2's cache is incorrect is the crux of this problem.[^2] This is even harder when the number of CPUs or cores increases. (Note that this example is deliberately simple. It ignores the fact that a core usually has multiple levels of cache, and that blocks of data are cached together and not individual values.) 

[^2]: This example image could also show a different scenario in which cores 1 and 2 read the value `10` from main memory, and later core 2 updates this value to `5` but has not yet updated main memory. Now the issue is that both main memory and core 1's cache need to detect that its values are incorrect.

![Cache incoherence example](/images/2017-05-31-intro-cache-coherence/incoherent.png){: .center-image}


## Cache coherence protocols

Coherency is maintained by following a coherence protocol. A cache controller, which sits between a core and its cache and main memory, communicates with other cache controllers according to its coherence protocol. The main memory controller is the same except it does not interact with any core of its own. Here is an example of a two-core system:

![Cache controller diagram](/images/2017-05-31-intro-cache-coherence/cache-controllers.png){: .center-image}


## The SWMR invariant

Coherence protocols are based on some invariant such that if the invariant holds then the system is coherent, and incoherent otherwise. The simplest invariant is the Single Writer, Multiple Readers (SWMR) invariant, which states that there are two valid possiblities: either a single controller has both read and write privileges on a block in its cache but no other controller has either read or write privileges, or multiple controllers have read privileges but none can write.

It is easy to see that if this invariant holds then cache incoherency is impossible. Imagine the example above where core 1 updates a value in its cache. If two controllers had read privileges to the same block, then neither would have write privileges. In order for core 1's cache controller to update the value in its own cache, it would first have to request to become the Single Writer for that block. By definition, that would mean core 2's cache controller would no longer have permission to read that block from its cache; it is invalidated. When core 2 attempts to read that block in the future, its cache controller would see the invalidation and a cache miss would occur; the read would be fulfilled from system memory. If core 2's cache controller wishes to cache that block, it would have to request core 1's cache controller to go back to the Multiple Readers state.


## Types of protocols

There are many protocol variants but there are two main classes:

 * Snooping protocols: At any given point, for a particular block, one controller (cache or memory controller) has the most up-to-date value for the block and considers itself to be the "owner" of the block. Any cache controllers wishing to read that block issues a broadcast to all other cache controllers for the most up-to-date value; the owner of the block responds and the others ignore the request. These protocols rely on the network to maintain message order. 
 * Directory protocols: The main memory controller is designated the "home" controller, and all cache controllers wishing to read a block first issue a request directly to the home. The home maintains a directory of the owners of each block, and if the home is not the owner, the home unicasts a request to the owner, which responds directly to the original requestor.

Another major design decision independent of whether the protocol is snooping or directory is what to do when a cache writes to a block: 

 * Invalidate protocol: the cache broadcasts for all other caches to invalidate the block
 * Update protocol: the core broadcasts the new value to all other caches to update their values for the block. These are rare however due to the bandwidth necessary to broadcast an entire cache block.


## Additional reading

I would have liked to go into more depth about the design of cache coherence protocols, but the information necessary would not fit well in a blog article. To learn more, I recommend A Primer On Memory Consistency and Cache Coherence.
