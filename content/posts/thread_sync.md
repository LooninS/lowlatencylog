+++
title = "Thread Synchronization"
date= 2026-06-18
description = "How hardware make working with threads less of a nighmare"
draft = true
tags = ["threads", "synchronization", "hardware","c"]
+++

Threads sometimes need to access shared resources, at the same time. This part of code is called _critical section_. This cause race conditions, and is a nightmare to debug. The techinque used to solve this problem is **Threads Synchronization**

In last post, I implemented to resolve the issue by using flags and Peterson's algorithm, but they are reliable on newer machines. 
Since software can't solve the issue, hardware support is needed.

## Hardware Support for Threads and Process Synchronization
### Memory Barriers(a.k.a. Memory Fences)
It ensures memory operations before the barrier are completed and visible before memory operations after the barrier are allowed to start.
It's and special type of instruction that tells CPU no memory operation after the barrier are allowed until all pipelined memory operations before the barrier are completed.

### Atomic Instructions 
