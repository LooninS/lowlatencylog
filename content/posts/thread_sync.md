+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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++
title = "Thread Synchronization"
date = 2026-06-18
description = "How hardware makes working with threads less of a nightmare"
tags = ["threads", "synchronization", "hardware", "c"]
categories = ["systems-programming"]
+++

Threads sometimes need to access shared resources at the same time. This part of code is called a **critical section**. This causes race conditions and is a nightmare to debug. The technique used to solve this problem is **Thread Synchronization**.

In my last post, I implemented solutions using flags and Peterson's algorithm, but they **don't work reliably on newer machines**. Since software alone can't solve the issue, **hardware support is needed**.

## Hardware Support for Thread and Process Synchronization

### Memory Barriers (a.k.a. Memory Fences)

A memory barrier ensures that memory operations **before** the barrier are completed and visible before memory operations **after** the barrier are allowed to start.

It's a special instruction that tells the CPU: *"No memory operation after the barrier is allowed until all pipelined memory operations before the barrier are completed."*

In some sense, we're telling the CPU **not to reorder instructions across the barrier**.

Now, we can use this to fix the issue with Peterson's algorithm:

```c
int turn;           // 0 if thread_0's turn, 1 if thread_1's turn
bool flag;       // flag[i]==true if thread_i wants to enter[1]

flag[i] = true;     
asm volatile("mfence" ::: "memory");  // ← Memory barrier
turn = j;
while (flag[j] && turn == j);  // spin wait
// Critical section
flag[i] = false;    
```

This small change solves the entire issue of **CPU reordering instructions**.

>[!Note]
> The `volatile` keyword prevents the compiler from reordering or removing this instruction. The `"memory"` constraint tells GCC that this instruction affects memory, preventing it from moving memory accesses across the barrier.

However, this is where things get interesting.

On **multi-core CPUs**, each core has its own cache, and it's not necessary that a variable stored in one core's cache is immediately available in another core's cache. **Memory barriers also handle this** — ensuring operations are completed and visible across cores.

#### Cost of Memory Barriers

These come with **performance overhead** and aren't particularly intuitive. We usually think in terms of logic when writing code, not the CPU's "dirty business" of moving and shuffling bits.

These are **low-level instructions** and are not used in most code, except by kernel developers.

---

### Atomic Instructions

Atomic instructions are special instructions that execute as a **single indivisible operation**, meaning they can't be:
- Interrupted
- Observed in an incomplete state
- Interleaved with other operations by concurrent threads or processors

For our case, we have **TAS** and **CAS**: **TEST AND SET** and **COMPARE AND SWAP**.

---

#### TEST AND SET

```c
// Software version (NOT atomic!)
bool test_and_set(bool *flag) {
    bool rv = *flag;
    *flag = true;
    return rv;
}
```

This function normally compiles to **multiple instructions**, but the CPU provides an **atomic version** that performs the entire operation in one step, eliminating the possibility of being interrupted mid-way.

The function won't automatically compile to one instruction — instead, the CPU implements the same behavior atomically.

In practice, we write an inline asm wrapper to invoke the hardware instruction:

```C
static inline bool test_and_set(bool *flag) {
    unint8_t old;
    asm volatile(
        "TEST_AND_SET %0, [%1]"
        : "=r"(old)
        : "r"(flag)
        : "memory");
    );
    return old != 0;
```


When the function is called, it reliably compiles to a **single atomic operation**. Even on multi-core systems, it ensures operations happen in some **sequential order**.

Since this operation is used to protect against race conditions, it must provide certain guarantees:
- Any pending memory operations must be completed (operates on most up-to-date data)
- After its execution, its result must be visible to other cores

This doesn't solve the critical section problem directly, but we can use it to implement mutual exclusion with a simple algorithm:

```c
bool lock = false;

while (test_and_set(&lock)); // spin wait
// Critical section
lock = false;
```
---

#### COMPARE AND SWAP (CAS)

```c
int compare_and_swap(int *value, int expected, int new_value) {
    int tmp = *value;
    if (*value == expected) {
        *value = new_value;
    }
    return tmp;
}
```

This is similar to `test_and_set`, but uses arbitrary values instead of just `0` and `1`.

#### Progress and Bounded Waiting

This guarantees **mutual exclusion**, but it doesn't fully solve the critical section problem.

Say we have the above code in an infinite loop:

```c
while (true) {
    while (test_and_set(&lock)); // spin wait
    // Critical section
    lock = false;
}
```

The use of `test_and_set` ensures mutual exclusion, but once a thread releases the lock, it will **immediately try to acquire it again**. Due to timing issues outside our control, it may keep getting priority and **starving other threads**.

A thread may keep getting control before other threads are able to gain it.

Any solution to the critical section problem must ensure:

| Property | Description |
|----------|-------------|
| **Mutual exclusion** | Only one thread in critical section at a time |
| **Progress** | If no thread is in critical section, one waiting thread must enter |
| **Bounded waiting** | A thread waiting to enter must wait at most N attempts |

---

### Bakery Algorithm (Satisfies All Three Properties)

A solution satisfying all these properties:

```c
bool waiting[n];
bool lock;

waiting[i] = true;
bool key = true;

// Spin wait until it's safe to enter the critical section
while (waiting[i] && key) {
    key = test_and_set(&lock);
}
waiting[i] = false;

/* Critical section */

// Search for next waiting thread
j = (i + 1) % n;
while ((j != i) && !waiting[j]) {
    j = (j + 1) % n;
}

if (j == i) {
    lock = false;  // No waiting thread, release lock
} else {
    waiting[j] = false;  // Give turn to waiting thread
}
```

An array is used to keep track of threads waiting to enter the critical section. The `lock` variable is used to check if the critical section is occupied.

To enter the critical section, **2 conditions** must be met, and one depends on an atomic operation:


**The difference:** Unlike before where it was a free-for-all competition for the lock, this enforces a **round-robin order**.

When a thread exits the critical section, it checks if another thread is waiting to enter. If no other thread is waiting, it releases the lock (giving itself a chance to regain control). Otherwise, it gives the turn to that thread by breaking its loop.

---

## Mutex Lock

The code above is much more logical than directly using memory barriers, but this can easily become a **readability nightmare**. Every critical section needs to be wrapped in this synchronization logic.

Since these tools are low-level, the OS provides **higher-level abstractions** to make it easier to write multi-threaded code. The most common abstraction is **Mutex Lock**.

This helps prevent race conditions with two simple calls:

```c
pthread_mutex_lock(&lock);  // Acquire lock
// Critical section
pthread_mutex_unlock(&lock);  // Release lock
```

It doesn't matter how complex the critical section is, we can do the whole synchronization logic in just **two lines of code**.

---
