---
layout: post
title:  "Hey buddy, sup?!?"
date: 2013-11-26 21:06:26 +0100
categories: legacy
---
Operating systems should usually deal with the memory management. 
Actually, the management itself includes and implies some common problems, depending on the implementation.
The intuitive problems that the memory manager must address and try to solve are related to efficiency, overhead, complexity, fragmentation and so on. In particular the fragmentation can cause memory wastes, performance degradation and instability issues. Furthermore, fragmentation can be divided in two types: internal and external.
The strategy of the buddy system is to allocate the memory by the means of the concept of block. Each block is simply a contiguous region of memory with size equal to 2^order.
For example, let's consider two blocks of different orders (respectively zero and one for convenience). Let's say that a block with order equal to 0 is equal to **4KiB** (typically the page frame size on **x86**), it's obvious that the block with order 1 is exactly the double (**8KiB**) of the one of order 0.
So theoretically, if we consider **64KiB** (and the minimum block size allowed equal to **4KiB**) of free memory, it means that the buddy memory allocation will consider this chunk of memory as one single block of order **4** (2^4 * 4096).

At this point, the next question that arises is: what about if you need just one page frame of memory?
The buddy allocator basically splits recursively the block in two halves (which will become two blocks of size order-1) until the target size (the requested order) is reached.

So once we call ```get_free_page()```<br/><br/>


![]({{site.baseurl}}/assets/img/Buddy.png)<br/><br/>



Once the buddy system has completed the split, it will allocate the block (marking it as no longer free) returning its associated structure (or the PFN) to satisfy the request.

On the other hand when we need to release the memory block previously allocated, it will be in charge of coalesce recursively the blocks until the initial situation is restored (one big block of order 4).

Modern computers usually come with large amount of RAM, so obviously for practical reasons, it is unlikely to find an implementation of this kind of memory manager where you have one single block of a huge order.

This is mainly due to the fact that the big memory block should be split too many times for memory requests with order equal to zero (common case) making the whole manager not so efficient in certain conditions.

Hence, for this very reason the memory is split in multiple blocks of equal **MAX_ORDER** (let's say 8) which identifies the upper boundary (in terms of size) of the block itself.

[Here]({{site.source_baseurl}}/buddy_allocator) you can find an implementation of the buddy memory manager.

Note that the code is just a draft (don't blame me for stuffs here and there) and part of another project I'm carrying on .

**<<EOF**
