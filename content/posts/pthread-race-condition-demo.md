+++
title = "What I Learned About Data Races By Writing a pthread Race Demo in C"
date = 2026-06-15T22:08:00+05:30
draft = false
tags = ["c", "pthreads", "concurrency", "systems", "race-condition"]
description = "A small C experiment that made pthread data races, thread scheduling, and undefined behavior feel concrete."
+++

I've been learning more about operating systems and concurrency in C. I came across an interesting idea called [data races](https://en.wikipedia.org/wiki/Race_condition#Data_races) in the context of multi-threaded programming.

The idea is that two or more threads can access the same memory location at the same time, and if there's no synchronization, the result is **undefined**.

In this post, I'll write a small C program that demonstrates data races.

## What Are Threads?

Each program executes as a separate **process**, and each process has its own memory space.

**Threads** are like lightweight mini-processes

Threads belong to the same process, share the same memory space, and can run multiple functions of the same program **concurrently**.

Before diving into data races, let's look at the code.

## Writer Function

```c
#include <string.h>
#include <stdio.h>

#define STRING_SIZE 100

char shared_string[STRING_SIZE] = "lorem ipsum";

void *writer_function(void *arg) {
  int i = 0;
  while (1) {
    strncpy(shared_string, sentences[i], STRING_SIZE - 1);
    shared_string[STRING_SIZE - 1] = '\0';
    i = (i + 1) % NUM_SENTENCES;
    sleep(5);
  }
  return NULL;
}

int main() {
    writer_function(NULL);  // ← Called directly, no threads
}
```

This program creates a global variable `shared_string` and initializes it with a string. The `writer_function` will indefinitely write to it.

If we call the function directly in `main()`, there's **no issue**:

- `main()` runs `writer_function` on the **same thread**.
- Even though `writer_function` writes repeatedly, each write happens **after** the previous one finishes.
- **No concurrency** → **no race**.

## The Multi-Thread Version

```c
#include <pthread.h>
#include <stdio.h>

int main() {
  pthread_t writer_thread_1, writer_thread_2, writer_thread_3, writer_thread_4;

  // Create 4 threads
  pthread_create(&writer_thread_1, NULL, writer_function, NULL);
  pthread_create(&writer_thread_2, NULL, writer_function, NULL);
  pthread_create(&writer_thread_3, NULL, writer_function, NULL);
  pthread_create(&writer_thread_4, NULL, writer_function, NULL);

  FILE *fp = fopen("shared_string.txt", "a");
  for (long k = 0; k < 250000000; k++) {
    fprintf(fp, "%s\n", shared_string);  // ← Main thread READS while others WRITE
  }

  fclose(fp);

  // Wait for all threads to finish
  pthread_join(writer_thread_1, NULL);
  pthread_join(writer_thread_2, NULL);
  pthread_join(writer_thread_3, NULL);
  pthread_join(writer_thread_4, NULL);

  return 0;
}
```
> [!TIP]
> This can be done with a **single writer thread**: one thread calling `writer_function()` and the main thread calling `fprintf()` are enough to cause a data race.
> More threads just make **bad interleavings** more likely, but they aren't required for a race to exist.

Now we have:

| Thread | Role | Access Type |
|--------|------|-------------|
| `writer_thread_1` | `writer_function` | WRITE `shared_string` |
| `writer_thread_2` | `writer_function` | WRITE `shared_string` |
| `writer_thread_3` | `writer_function` | WRITE `shared_string` |
| `writer_thread_4` | `writer_function` | WRITE `shared_string` |
| `main` | `fprintf()` | READ `shared_string` |

**Total:** 5 threads (1 main + 4 writers) all accessing the same `shared_string`.
***
## C be doing C-things
C strings are stored as **null-terminated character arrays**. `strncpy` copies character-by-character until it reaches the null terminator—it's **not atomic**.

```asm
; strncpy copies like this:
mov cl, [source]      ; load 1 byte
mov [dest], cl        ; store 1 byte
inc source            ; advance pointer
inc dest
cmp cl, 0             ; check for null
jne copy_next         ; loop if not null
```

If the CPU interrupts the copy **in the middle**, the string is **partially updated**. If another thread reads/writes this location, it's working with **undefined data**.

This is what we call a **data race**:

| Problem | Explanation |
|---------|-------------|
| **Multiple threads write** | 4 threads writing to `shared_string` simultaneously |
| **Main thread reads** | `fprintf()` reads while writers modify |
| **No synchronization** | No mutex, no atomic, no barrier |
| **Non-atomic operation** | `strncpy` = many instructions, not 1 |


**Definition:** A data race is a software bug that occurs when the program's outcome depends on the **unpredictable order** in which multiple threads/processes access and modify shared resources.

## The things I saw

I made a program that compares each sentence in `shared_string.txt` with the original sentences and logged any variations.

### Output

```text
ERROR 148216796: Programming is the art of telling another human what one want.
ERROR 217102089: Algorithms are the baccritical in low-level system programming.
```

The number is the **line number** in `shared_string.txt`.

| Metric | Value |
|--------|-------|
| Corruption frequency | ~1 per 100 million lines |
| Total lines written | 250,000,000 |
| Observable races | 2 |

Looking at this data one might think big deal, there is a small error once every 100M lines, and sure it might not be a big deal here next post I will demostrate how this can actually change the final result we want.
***
> [!NOTE]
>There might be some errors with `"lorem ipsum"` in the error file. That's the initial value of `shared_string`. This can happen if `fprintf()` writes before `shared_string` has been updated by any writer thread.
***
Source on GitHub: [View on GitHub](https://github.com/LooninS/thread_race_condition_demo)
***
