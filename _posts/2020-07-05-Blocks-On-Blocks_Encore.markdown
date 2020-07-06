---
layout: post
title:  "Blocks On Blocks Encore"
date:   2020-07-05 08:58:08 +1000
categories: 
---

# Progress

We have now fixed all the aforementioned problems with CXX compilation of `tng_io.cpp`, making sure that all our CI is happy and that all the requisite C constructs have been transferred to C++. Again progress on our block interface continues in [!32](https://gitlab.com/gromacs/tng/-/merge_requests/32), with the plan I have nearing completion, with the exception of the Read() and Write() methods, which are the hardest and most important. I now need to start thinking about how to do a `TngTrajectory` interface which I am playing around with in my own branch (no MR yet). I have also made good progress on the design of the blocks

# I am unique

Paul indicated to me that modern C++ and GROMACS use smart rather than raw pointers, and that I should too. The primary ones being ```std::unique_ptr<T>``` and ```std::shared_ptr<T>```. Unique_prt requires ownership transfer, while shared_ptr is reference counted so that it is auto deleted when the final reference goes out of scope. The primary use of these two is to avoid the use of the `new` and `delete` keywords as much as possible. An for unique_ptr is given below.

```c++

auto ptr = std::make_unique<TngBlock>(a,b,c,d); // make a unique_ptr
std::vector<std::unique_ptr<TngBlock>> storage; // create a container
storage.emplace_back(ptr)  // X this will not work as we are implicitly copying
storage.emplace_back(std::move(ptr)) // this will work, we have transferred ownership
```

# Cut me from the mould

The problem of how to work with heterogenous data types for conatiners has arisen which i have decided to solve by foraying into some templating. Basically, this solves the problem of having to do some kind of `void*` cast as well as the problem of complicated flags and logic that are currently carried around to indicate data types.
How this is done is shown below with the constructor declaration for `TngDataBlock`. 

```c++
template<typename T> // type for data array
TngDataBlock<T>::TngDataBlock(const int64_t&     id,
                              const std::string& name,
                              const char&        datatype,
                              const char&        dependency,
                              const int64_t&     first_frame_with_data,
                              const int64_t&     n_frames,
                              const int64_t&     n_values_per_frame,
                              const int64_t&     stride_length,
                              const int64_t&     codec_id,
                              const int64_t&     last_retrieved_frame,
                              const double&      compression_multiplier,
                              T*                 values) :
```

This allows a TngDataBlock of type `T` to be declared with a corresponding set of values in a `T*` array, eg one can declare a ```TngDataBlock<double>``` if one has an array of doubles to store.

# Clientele

We had a quick meeting with my mentors late last week in which we discussed timelines and development for the project. We decided that we would like to implement a python layer as soon as we are done with the C++ refactoring, which means i really need to get a move on, as the idea for  redesign I have will require some quite deep manipulation. Rochard has said he will help me write some Cython bindings and this will help me also clarify what kind of external interface I want.  


# Next steps

Continue to work on the block interface and Trajectory interface, including test code in the unit tests. Start some reading on the Cython aspect
