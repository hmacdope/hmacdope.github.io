---
layout: post
title:  "GriTNG My Teeth"
date:   2020-08-14 08:58:08 +1000
categories: 
---

# Progress

A first generation PyTNG interface is now complete. All blocks and their metadata can be read and squished into numpy arrays.
The TNGFileIterator is more minimal than the first try, with most of the memory allocation now passed onto the user.
After consultation with Richard and Oliver, we decided that It was best to force the user to manage their memory rather than make assumptions about what the user may or may not have available.
This means that the data is copied into user supplied numpy arrays which then own the memory, meaning `malloc`, `realloc`, `PyMemMalloc` and `PyMemRealloc` don't need to be called.
I have written new tests in addition to making all the existing tests that were appropriate pass.

We have also decided to stick more closely to the original TNG API spec, with the idea that we don't want to make assumptions about what the user wants to do with their data and instead leave some of the harder decisions up to them. 

Performance concerns have been at the front of my mind. Some profiling of the Cython code reveals that the code is IO bound in most cases, with the pure C `_get_data_current_frame()` routine bottlenecking the speed. On the SSD on my laptop the balance is shifted a bit towards the array copying (see below).

Cumutively the progress I have made means that once the API settles down we can integrate pyTNG into MDAnalysis. How exciting!


# Possible problems?

The conversion of C `void*` data read off disk into the numpy array is currently very inefficent, involving an element by element copy. Instead we should use a memoryview or some kind of raw copy of the C data into the numpy array memory buffer. This will speed up the data transfer and hopefully improve performance in less IO bound scenarios.


# Next steps

Write some docs! In line with my original plan I should write some basic docs over the next week or so, so that people know how to use what I have made. Other than that I should also try and prep for final submission.