+++
title = "writing quick sort in asm"
date = 2026-06-22
draft = true
+++

I recently realized something today... I can just do things. So, I implemented quick-sort in `asm` as compared it speed against c inbuilt stdlib to prove `C` is just bloat.

I never written any assembly before, but how hard it be, really? 

I mean a good start will be just write a quick-sort function in `C` and then head over to `godbolt.org`. But, that's cheating.

## Quick Sort

Let's begin by understanding what quick sort does:

+ Recursion
+ ???
+ Profit

Yeah, make sense. 

---

I know we have to call it recursively, kinda the whole point of it. Wait, like do we really have to? I mean, what's recursion if not a loop?

I am sure we can just do a for loop instead of the whole recursion bullcrap.

This is the implementation of quick sort for standard library:
```c
qsort(void *base, size_t nmemb, size_t size, __compar_fn_t compar)

```
I just implemented `int` sorting with not custom comparator. So it dropped to this:

```c
void qsort(int *base, size_t nmemb);
```
Since, I am trying to implement it with iteration I got to think about how am I going to convert 2 line of recursive code using iteration. 

In each of the recursive call, function is put on a stack and only popped off when it's done:

