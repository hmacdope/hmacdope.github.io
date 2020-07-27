---
layout: post
title:  "Cycles of Cyzzling Cython"
date:   2020-07-23 08:58:08 +1000
categories: 
---

# Progress

The switch to PyTNG has been fun but also challenging, as switching to a new language at short notice has required some adjustment in both mindset and practicality. In good news, my experience with TNG itself has allowed me to rapidly make strides in changing the interface and adding test cases.  I have developed a very rough prototype that reads coordinates, velocities and forces if present. This is validated by a range of new test cases and test files that match those in the C++ TNG library. The result of my handiwork can be seen in  [#29](www.github.com/MDAnalysis/pytng/pull/29).

This gets the job done but is far from satisfactory for reasons I will outline below. One upshot is that there are a whole bunch of new test cases for later modifications.

# Blocks 

The counting of "frames" by MD steps means that you have to do a whole bunch of useless blank reads. A TNG file can have as many potentially arbitrary blocks as it wants and it is difficult/wasteful to keep track of all of them. Instead we should try and spool off the blocks sequentially! That makes much much more sense as it will give us data when present only and exactly the types we want, all we need is a matching cdef cython class to hold the information and a way to pull them out of the file.

This sounds easy right? A draft PR of the start of this can be found in [#32](www.github.com/MDAnalysis/pytng/pull/32).

# Possible problems?

How exactly to spool the blocks off the file is something I still need to work on. There are lots of possible functions that seem to do similar things. At the core of most of them are twin calls to `tng_block_read_header` and `tng_block_read_next`, but I am a bit unclear about what exact state the `tng_trajectory_t` struct needs to be in to call this correctly. This is because the blocks do not contain (obvious) information linking them together (I would love to be wrong here). Instead as far as I can tell the position is just kept track of with an `int64_t` counter of the file position and a struct that marks the current `trajectory_frame_set`.

Watch this space.


# Numpy 

Once we have the data off the block we will need to cast it to a numpy array. This can be done with the following code which is a bit ugly but gets the job done.

```cython
    #declare mem wrapper and array
    cdef MemoryWrapper wrap
    cdef float* array = NULL
    
    #wrap python memory and bind it to the values at C level
    wrap = MemoryWrapper(dim1 * dim2 * sizeof(float)) # deals with ref counts, PyMemMalloc and PyMemFree
    values = <float*> wrap.ptr
    function_that_binds_ptr_2_ref_counted_float_array(&values)

    #declare numpy things
    cdef np.ndarray reshaped_npy_arr
    cdef npy_intp_dims dims[2] # dimension holder
    cdef int err
    cdef int ndim = 2
    
    #set the dims 
    dims[0] = dim1
    dims[1] = dim2
    
    #bind the values to the numpy array
    reshaped_npy_arr = PyArray_SimpleNewFromData(nd, dims, NPY_FLOAT, wrap.ptr)
    Py_INCREF(wrap) #increment reference counter to take new binding into account

```

# Next steps

Keep working on the way to spool blocks off with pyTNG, may need some ideas from richard on this. 
Magnus has also kindly given me some feedback on my TNG++ code which I will incorporate and move forward with. 
