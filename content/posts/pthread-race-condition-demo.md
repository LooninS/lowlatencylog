+++
title = "What I Learned About Data Races By Writing a pthread Race Demo in C"
date = 2026-06-15T22:08:00+05:30
draft = false
tags = ["c", "pthreads", "concurrency", "systems", "race-condition"]
description = "A small C experiment that made pthread data races, thread scheduling, and undefined behavior feel concrete."
+++

I've been learning more about operating systems and concurrency in C. I came across an interesting idea called [data races](https://en.wikipedia.org/wiki/Race_condition#Data_races) in the context of multi-threaded programming.

The idea is that two or more threads can access the same memory location at the same time, and if there's no synchronization, the result is undefined.  
This doesn't happen *very* often in practice, in the sense that CPUs are fast and the scheduler can keep threads somewhat separated, but when it does happen, the behavior is unpredictable.

In this post, I'll write a small C program that demonstrates the idea of data races.

## What are threads?

Each program is executed as a separate process, and each process has its own memory space.
Threads are like lightweight mini-processes. They belong to the same process, share the same memory space, and can run multiple functions of the same program concurrently.

Before diving into what data races are, let's look at this code:

```c
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
    writer_function(NULL);
}
```

This program creates a global variable called `shared_string` and initializes it with a string. The `writer_function` will indefinitely write to `shared_string`. If we call the function directly in `main()`, there won't be an issue:

- `main()` runs `writer_function` on the **same thread**.
- Even though `writer_function` writes to the same memory location repeatedly, each write happens after the previous one finishes.
- There's no concurrency, so there's no race.

Now let's look at what happens when we create multiple threads.

```c
int main() {
  pthread_t writer_thread_1, writer_thread_2, writer_thread_3, writer_thread_4;

  pthread_create(&writer_thread_1, NULL, writer_function, NULL);
  pthread_create(&writer_thread_2, NULL, writer_function, NULL);
  pthread_create(&writer_thread_3, NULL, writer_function, NULL);
  pthread_create(&writer_thread_4, NULL, writer_function, NULL);

  FILE *fp = fopen("shared_string.txt", "a");
  for (long k = 0; k < 250000000; k++) {
    fprintf(fp, "%s\n", shared_string);
  }
  fclose(fp);
  return 0;
}
```

This program creates 4 threads and runs `writer_function` in each of them.

All 4 threads share the same memory space as the main process, so they all access the same `shared_string`. Each thread is trying to update `shared_string`, and the CPU can switch between them at any point. Because there is no synchronization, writes can be interrupted, and another thread (or the main thread) might read the variable while it's only partially updated. That's why unsynchronized access to shared memory is a problem.

This is a **data race**:

- Multiple threads write to the same memory location (`shared_string`).
- The main thread reads from that same memory location and writes the result to a file.
- There is no mutex or other synchronization to order these accesses.

This is a race between the threads with each one is trying to write to `shared_string` before the CPU interrupts and switches to another thread.
So a thread may write to shared_string at the same time as another thread is writing to it, or while the main thread is reading it. The writes can interleave in unpredictable ways, and the value of shared_string at any moment depends on the exact timing of thread scheduling.

Note that the file writing itself is still sequential: `fprintf` is called in order by the main thread. But the **data being written** may be corrupted because `shared_string` might be partially updated when we read it.

## What I actually observed

I made a small program to demonstrate this. It writes a set of pre-determined sentences to a file. The 4 threads are writing to the same memory location that `fprintf` is reading from.

Then I wrote another small program to compare the output file against the original sentences. I got errors like:

```text
ERROR 148216796: Programming is the art of telling another human what one want.
ERROR 217102089: Algorithms are the bacritical in low-level system programming.
```

The number is the line number in `shared_string.txt`. That's how rare the visible corruption is in this setup (roughly once every 100 million lines), even though the CPU performs billions of operations per second. The _probability_ of a particular bad interleaving is low, but the sheer number of operations adds up.

Note: There might be some errors with `"lorem ipsum"` in the error file. That's the initial value of `shared_string`. This can happen if `fprintf()` writes to the text file before `shared_string` has been updated by any writer thread.

Also, this can be done with a single writer thread: one thread calling `writer_function()` and the main thread calling `fprintf()` are enough to cause a data race, because they both access the same memory location (`shared_string`) without synchronization. More threads just make bad interleavings more likely, but they aren't required for a race to exist.
