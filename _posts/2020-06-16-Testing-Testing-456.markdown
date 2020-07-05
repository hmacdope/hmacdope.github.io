---
layout: post
title:  "Testing Testing 456"
date:   2020-06-14 08:58:08 +1000
categories: 
---

# Progress

Following on from meeting Paul and Richard it has been a busy week of coding.
We have spent the majority of this week finalizing the test suite, integrating it into the CI and most importantly identifying and fixing bugs in the TNG codebase itself. We have also made some progress on C++ conversion, looking at portability of the whole codebase, deciding on a standard etc. I will address each of these individually below.

# Testing

Based on suggestions of last week, I generated 2 new test trajectories. Firstly, WaterTrjconv.tng, which has been put through gmx trjconv. Trjconv spits out a TNG file with slightly modified properties, including a whole new frame set per frame, so we felt it was important to test for this. Additionally we have WaterUncompressedVelsForces.tng which has positions, forces and velocities in an uncompressed form.

I have written **extensive** regression tests for these and the existing example files (~50 total) as part of the new regression testing binary. I also registered them with CMake so that `make test` runs the test suite with Ctest. How to do this is shown below

```cmake
Include(GoogleTest)
enable_testing()
gtest_discover_tests(TARGET ... SUBDIRECTORY ...)

```

This along with extended tests was merged as part of [(!21)](https://gitlab.com/gromacs/tng/-/merge_requests/21)

# Something is bugging me

In both good and bad news, adding a lot of new tests has revealed several bugs in the TNG code, which look like they have been there for a while. Some are to do with reading the correct frame data and offsets and return the wrong coordinates, velocities and forces. Needless to say glad we found them. Magnus has been very kind and fixed a whole heap of them, but as I mentioned before, I would like to be able to do this. A particularly troublesome section of code has been the correct alignment of data to read in `tng_gen_data_vector_interval_get()` which is the TNG reading workhorse. Once again, I need to wrap my head around it so I can reduce reliance on Magnus.

# I'm a convert?

Converting the current codebase to C++ is starting in baby steps. First you have to teach CMake that you want your .c files compiled by the CXX compiler by using.

```cmake
set_source_properties(SOURCE ... LANGUAGES CXX)
```
Then the annoying bit, you have to fix all the non ISO C++ conforming code. Some things that have proven troublesome are things like the following macro


```c++
void DECLSPECDLLEXPORT* Ptngc_warnmalloc_x(size_t size, char* file, int line);

#define warnmalloc(size) Ptngc_warnmalloc_x(size, __FILE__, __LINE__)

```

This macro is used a lot in the compression sources and performs a `void*` to `T*` implicit cast which has deprecated in C++ as being unsafe. In C++ this requires an explicit cast, something like changing the macro to:


```c++
void DECLSPECDLLEXPORT* Ptngc_warnrealloc_x(void* old, size_t size, const char* file, int line);

```
and including an explicit cast with `static_cast(T*)`

```c++
unsigned int*  dict = static_cast<unsigned int*>(warnmalloc(0x20004 * sizeof *dict));
```

After discussing this with Paul, we decided not to waste any time fiddling with the compression routines, which will be slow and error prone, with limited tests to back me up. Instead that brings me to:

# Next steps

Now for the fun/exciting/hardest part. **Refactor the actual TNG library to be proper C++.**
Tests are now at a point where I will know if I break anything. First I will compile everything as CXX and then build new classes from there! Exciting times.

