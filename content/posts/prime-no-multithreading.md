+++
title = "Calculating Number of Primes using Multithreading"
date = 2026-06-16
description = "Understanding race condition by writing a multithreaded prime number calculator"
tags = ["c", "pthreads", "multithreading", "concurrency", "race-condition"]
+++

In the last post, I wrote about how to create threads and problems caused by them (race conditions).

In this post, I'll show how to calculate the number of primes using parallelism. The idea is simple: use an algorithm to check if a number is prime, and increment a counter if it is. Then I can run multiple threads on different ranges and calculate the number of primes using multithreading.

Let's look at the following code

```C
typedef struct {
  int start;
  int end;
} Range;

int counter = 0;

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
            counter++;
        }
    }
    return NULL;
}
```
The `isPrime` function checks if a number is prime, and `primeLoop` increments the global `counter` for each prime it finds in its range.

Now let's create a thread for each range and pass the range as an argument to the thread function
```C
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

    printf("Total primes: %d\n", counter);
    return 0;
}

```
However, running this code gives different results each time:
```bash

~ via C v16.1.1-gcc on ☁️   
❯ ./prime 
Total primes: 132

~ via C v16.1.1-gcc on ☁️   
❯ ./prime
Total primes: 95

~ via C v16.1.1-gcc on ☁️   
❯ ./prime
Total primes: 115
```

The correct answer is **168 primes** between 1 and 1000. Our program is way off, and the results vary. This is exactly what I discussed in the last post: **race conditions**.

## What the heck is going on?
It's important to understand that each line of C code doesn't necessarily compile to a single machine instruction.

For example, this simple code:
```C
a = b + c;
```
Simple right? All we are doing is adding two variables together and assigning the result to another variable.
However, the compiler might translate this to something like:
```asm
mov     edx, DWORD PTR [rbp-4]
mov     eax, DWORD PTR [rbp-8]
add     eax, edx
mov     DWORD PTR [rbp-12], eax
mov     eax, 0
```
One line of code needed 5 instructions to complete.

Our program is a tiny bit more complicated than this. 
Let's look at the following code:
```C
void *primeLoop(void *arg) {
    Range *r = (Range *)arg;
    for (int i = r->start; i < r->end; i++) {
        if (isPrime(i)) {
            counter++;
        }
    }
    return NULL;
}
```
Every line here translates into multiple machine instructions, and the CPU can interrupt execution at any point and switch to another thread.

Say `counter = 10`. `thread1` successfully checks that one of its numbers is prime, but the CPU interrupts `thread1` **before** `counter++` completes. The other threads run normally and update the counter 4 times, so `counter` becomes 14. When `thread1` resumes, it reads the old value (10), increments it, and writes back 11. So we **lost** 3 updates.

> Note: The CPU saves a thread's state when it's interrupted so it can resume from the exact point. This is why `thread1` still "thinks" `counter` is 10 even though it changed in the meantime.

This is a classic data race: multiple threads write to the same variable (`counter`) without synchronization.

The key takeaway: multithreading can make programs faster, but it introduces new problems. You need to think about what happens when multiple threads access the same data.
