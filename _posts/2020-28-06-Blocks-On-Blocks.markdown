---
layout: post
title:  "Blocks On Blocks"
date:   2020-06-28 08:58:08 +1000
categories: 
---

# Progress

Progress continues on our refactor of the block interface in [!32](https://gitlab.com/gromacs/tng/-/merge_requests/32), with almost all the blocks having some kind of base structure laid out as well as the base class for the blocks having its primary methods all laid out. A slight setback in our attempts to compile as CXX has occoured. It turns out the SET_SOURCE_FILE_PROPERTIES *FILE.c* CXX is not effective on all compiler platforms, with clang seeming to recognise it, but warning it as deperecated, while GCC did not seem to listen to it at all. This can be seen when the compile commands are dumped with -DCMAKE_EXPORT_COMPILE_COMMANDS=ON. This was causing some of the merge train CI to fail. 

# real CXX compilation.

Fixing this requires two things primarily, renaming all the C source files to .cpp and fixing all the associated clang-tidy errors that are now identified.
Lots of these involved changing C style casts to proper (ugly) C++ style casts. Lots of these involved using ```reinterpret_cast<T>``` which made me a bit nervous, given that reinterpret cast does not perform any kind of type checking as to whether the cast will be valid. As such I attempted to do some sanity checks on the instances where the casts are used, which was a time consuming task given that there almost 1000 clang-tidy changes required. Additionally, it took some time to figure out that the required integer printing macros eg ```PRId64``` are only included with the ```<inttypes.h>``` C header in our setup. The result of fixing these problems is now in [!33](https://gitlab.com/gromacs/tng/-/merge_requests/33) and [!34](https://gitlab.com/gromacs/tng/-/merge_requests/32)

# Hashing

We considered moving the md5 hashing sources to CXX compilation and did temporarily, however in the end we decided against it as errors in this code will be very ahrd to detect and diagnose as it uses very low level manipulation to create the hash. A guide file for future CXX porting of the MD5 hashing will be the equivalent file in GROMACS, however this is outside the scope of this project for now. 


# Grand Designs

The kind of design I want for the block system is coming together nicely, one problem I have been having is how to make `TngDataBlock` accept multiple types. In the original C code a cast from a void* pointer was used. This is okay, but does not allow the pointer to ever be dereferenced. I think we will need to resort to some kind of templating to resolve this. 


# More tests

I have started to work on unit tests in `regtest_cxx.cpp` which tests the base level interface for the blocks. I am making good progress here and it has proved very usefull as a test bed for the kind of design I want.


# Next steps

Continue to work on the block interface and see how this might stitch  together into the ```TngTrajectory``` interface. I will need to finalise the constructors, detsructors, tests and methods of the blocks (a big task) before we can start to fold it into the main codebase.

