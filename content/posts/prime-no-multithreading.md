+++
title = "Calculating Number of Primes using Multithreading"
date = 2026-06-16
description = "Understanding race conditions by writing a multithreaded prime number calculator"
tags = ["c", "pthreads", "multithreading", "concurrency", "race-condition"]
+++

In the last post, I wrote about how to create threads and problems caused by them (race conditions).

In this post, I'll show how to calculate the number of primes using multithreading. The idea is simple:

1. Use an algorithm to check if a number is prime
2. Increment a counter if it is
3. Run multiple threads on different ranges
4. Sum up the total primes

Let's look at the code.

## The Code

```c
typedef struct {
  int start;
  int end;
} Range;

volatile int counter = 0;

bool isPrime(int n) {
    if (n <= 1) return false;
    if (n == 2 || n == 3) return true;
    if (n % 2 == 0 || n % 3 == 0) return false;

    for (int i = 5; i * i <= n; i += 6) {
        if (n % i == 0 || n % (i + 2) == 0) return false;
    }
    return true;
}

void *primeLoop(void *arg) {
    Range *r = (Range *)arg;
    for (int i = r->start; i < r->end; i++) {
        if (isPrime(i)) {
            counter++;  // <-- BUG: Not atomic!
        }
    }
    return NULL;
}
```

The `isPrime` function checks if a number is prime, and `primeLoop` increments the global `counter` for each prime it finds in its range.

Now let's create threads for each range:

```c
int main() {
    pthread_t thread1, thread2, thread3, thread4;

    Range r1 = {1, 250};
    Range r2 = {251, 500};
    Range r3 = {501, 750};
    Range r4 = {751, 1000};

    pthread_create(&thread1, NULL, primeLoop, &r1);
    pthread_create(&thread2, NULL, primeLoop, &r2);
    pthread_create(&thread3, NULL, primeLoop, &r3);
    pthread_create(&thread4, NULL, primeLoop, &r4);
    
    // Wait for all threads to finish
    pthread_join(thread1, NULL);
    pthread_join(thread2, NULL);
    pthread_join(thread3, NULL);
    pthread_join(thread4, NULL);

    printf("Total primes: %d\n", counter);
    return 0;
}
```

## Guessing might be better
However, running this code gives **different results each time**:

```bash
❯ ./prime
Total primes: 132

❯ ./prime
Total primes: 95

❯ ./prime
Total primes: 115
```

The correct answer is **168 primes** between 1 and 1000. Our program is way off, and the results vary. This is exactly what I discussed in the last post: **race conditions**.

## What the Heck Is Going On?

### Non-Atomic Operations

It's important to understand that each line of C code doesn't necessarily compile to a single machine instruction.

For example:
```c
a = b + c;
```

Simple right? We're just adding two variables and assigning the result. But the compiler might translate this to:

```asm
mov     edx, DWORD PTR [rbp-4]   ; load b
mov     eax, DWORD PTR [rbp-8]   ; load c
add     eax, edx                  ; add them
mov     DWORD PTR [rbp-12], eax  ; store to a
mov     eax, 0                    ; return 0
```

One line of code = **5 instructions**.

The counter could be increment is not an atomic operation.
```c
volatile int counter = 10;
counter++;  // NOT atomic!
```
This expands to **3 assembly instructions**:

```asm
; counter++ = LOAD → ADD → STORE
mov eax, [counter]    ; 1. LOAD counter from memory (eax = 10)
add eax, 1            ; 2. ADD 1 to register (eax = 11)
mov [counter], eax    ; 3. STORE back to memory (counter = 11)
```

**The CPU can interrupt between ANY of these steps!**

---


### What can go wrong?

Let's trace what happens when `counter = 10`:

| Time | Thread 1 | Thread 2 | Thread 3 | Thread 4 | `counter` |
|------|----------|----------|----------|----------|-----------|
| t1 | `LOAD counter` (eax=10) | | | | 10 |
| t2 | `add eax, 1` (eax=11) | | | | 10 |
| t3 | **STOP** | `LOAD counter` (ebx=10) | | | 10 |
| t4 | | `add ebx, 1` (ebx=11) | | | 10 |
| t5 | | `STORE counter` (11) | `LOAD counter` (ecx=11) | | 11 |
| t6 | | | `add ecx, 1` (ecx=12) | | 11 |
| t7 | | | `STORE counter` (12) | `LOAD counter` (edx=12) | 12 |
| t8 | | | | `add edx, 1` (edx=13) | 12 |
| t9 | | | | `STORE counter` (13) | `LOAD counter` (eax=10) | 13 |
| t10 | | | | | `eax=10` (old!) |
| t11 | **GO** `STORE counter` (11) | | | | **11** ← **LOST 3 UPDATES!** |

| Metric | Value |
|--------|-------|
| Final counter | `11` |
| Expected counter | `14` (10 + 4 primes) |
| Lost updates | `3` |

> **Note:** The CPU saves a thread's state when interrupted so it can resume from the exact point. This is why `thread1` still "thinks" `counter` is `10` even though it changed to `13` in the meantime.

This is what a race condition looks like:

| Problem | Explanation |
|---------|-------------|
| **Multiple threads** | 4 threads writing to `counter` simultaneously |
| **Same variable** | All threads access `volatile int counter` |
| **No synchronization** | No mutex, no atomic, no barrier |
| **Non-atomic operation** | `counter++` = 3 instructions, not 1 |

## Key Takeaway

Multithreading can make programs faster, but it introduces new problems. You need to think about what happens when multiple threads access the same data.


