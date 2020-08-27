---
layout: post
title:  "Final Submission"
date:   2020-08-23 08:58:08 +1000
categories: 
---

# Summary

Well well well, here we are at the end of GSOC! What a ride it has been, giving
me a chance to improve my skills and contribute in ways I never thought would
be possible. Thanks to all my mentors @acpmnv (Paul), @orbeckst (Oliver) and
@richardjgowers (Richard) for all the help and guidance throughout. 



# Why TNG?

Trajectory storage has proved problematic for the molecular simulation community, due to large file sizes, poor portability and low metadata incorporation. The TNG format designed by GROMACS aims to remove these shortcomings, however it has not seen wide adoption as tooling to support the format is lacking. Creation of new TNG tooling has been hindered by its current design and implementation in older style C code. My [project](https://summerofcode.withgoogle.com/projects/#5116604104310784) centered around improving  implementation and tooling for the TNG format. This was two fold, with the first half of my project working on converting the original library to C++ and the second half on developing some Python bindings.


# TNG time

I started GSOC working on the TNG library with the aim of adding tests,
understanding the API and converting the older C style API it to C++.  I worked
on this for the majority of the bonding period as well as the first and some of
the second coding period.

First I added tests in [!3](https://gitlab.com/gromacs/tng/-/merge_requests/3) which then revealed problems in the base API that were fixed in [!6](https://gitlab.com/gromacs/tng/-/merge_requests/6), [!12](https://gitlab.com/gromacs/tng/-/merge_requests/12)
and [!17](https://gitlab.com/gromacs/tng/-/merge_requests/17).  I then made these additional tests into a formal test suite employing
googletest in [!16](https://gitlab.com/gromacs/tng/-/merge_requests/16), [!20](https://gitlab.com/gromacs/tng/-/merge_requests/20) and [!21](https://gitlab.com/gromacs/tng/-/merge_requests/21). This revealed some more API elements that could be improved which were
touched up in [!22](https://gitlab.com/gromacs/tng/-/merge_requests/22), [!23](https://gitlab.com/gromacs/tng/-/merge_requests/23) and [!25](https://gitlab.com/gromacs/tng/-/merge_requests/25).

Once we had a test suite that could be used to check library correctness on the
major routines we started to work on the TNG library itself.
Key goals were C++ compilation and in turn modernisation of the library
routines to modern C++. I acheived full C++ compilation in a combination of [!26](https://gitlab.com/gromacs/tng/-/merge_requests/26), [!27](https://gitlab.com/gromacs/tng/-/merge_requests/27), [!29](https://gitlab.com/gromacs/tng/-/merge_requests/29), [!33](https://gitlab.com/gromacs/tng/-/merge_requests/33) and [!34](https://gitlab.com/gromacs/tng/-/merge_requests/34). 

From here I started on modernization of the library classes and constructs
to modern C++. These progressed in [!28](https://gitlab.com/gromacs/tng/-/merge_requests/28) which outlined the structure we were aiming
for, [!32](https://gitlab.com/gromacs/tng/-/merge_requests/32) which defined a specific API for blocks, [!35](https://gitlab.com/gromacs/tng/-/merge_requests/35) that updated the
trajectory API and [!36]((https://gitlab.com/gromacs/tng/-/merge_requests/36))
which progressed on the IO elements. These were huge tasks and due to this and some
changing circumstances, these MRs are all still open with more work required. I
plan on working on these into the future but progress will be slow as I do not
have solid blocks of time to dedicate. 

All the work I have done on TNG itself can be found in my TNG
[fork](https://gitlab.com/hugomacdermott/tng) as well as open PRs on the
library [itself](https://gitlab.com/gromacs/tng).


# PyTNG time

From here I switched focus to [PyTNG](https://github.com/MDAnalysis/pytng), a
set of python bindings designed for use by MDAnalysis although technically a
separate library. I worked on PyTNG for most of the second half of GSOC. This required
changing gears a little bit as well as learning Cython, which was initially a
bit of a learning curve for me. 

Firstly I changed the TNG libraries exported with PyTNG itself to
include the bugfixes obtained as part of the earlier improvements to the TNG
library. This was incorporated as part of
[#28](https://github.com/MDAnalysis/pytng/pull/28). I then improved the TNG library
calls in PyTNG in [#29](https://github.com/MDAnalysis/pytng/pull/29).

Following some design discussions, we then moved towards a newer design for the
bindings, so as to be able to read all the blocks available in a TNG file with
maximal speed. This was acheived in merging
[#32](https://github.com/MDAnalysis/pytng/pull/32) and ongoing work in
[#38](https://github.com/MDAnalysis/pytng/pull/38), which are a total
redesign of the whole PyTNG API.

The end result of this is a working implementation that can read any TNG block
as of #32 with improvements close in #38.
I also added docs and examples as
part of [#38](https://github.com/MDAnalysis/pytng/pull/38). Profiling and
timings indicated high performance of the bindings, with the library largely IO
bound at the TNG API (pure C) level.

# What can we do now?

The TNG library now has a regression-test suite and can be compiled as C++ 14 with a modern compiler. The groundwork is also laid for further improvements of the library.

There are now also a set of functional Python bindings for the TNG format that can read any TNG block.

An example of how to use PyTNG to read a TNG file and extract positions is shown below:

```python

   import pytng
   import numpy as np

   with pytng.TNGFileIterator("traj.tng", 'r') as tng:

      # make a numpy array to hold the data using helper function
      # this array will then be updated in-place 
      
      positions = tng.make_ndarray_for_block_from_name("TNG_TRAJ_POSITIONS")
      
      # the TNG API uses regular strides for data deposition, here we stride
      # over the whole trajectory for the frames that have position data
      # where len(tng) is the total number of steps in the file.

      for ts in range(0, len(tng), tng.block_strides["TNG_TRAJ_POSITIONS"]):
         # read the integrator timestep, modifying the current_integrator_step
         tng.read_step(ts)
         # get the data from the requested block by supplying NumPy array which
         # is updated in-place
         tng.current_integrator_step.get_pos(positions)

```

# Looking forward

I plan on extending PyTNG to TNG writing as well as integrating PyTNG into MDAnalysis
following GSOC. I have raised issues in PyTNG to make sure things that still
need to be completed are apparent to people following on. 

# What have I learnt?

Things I have learned in GSOC include

* C++ class design
* C++ for binary IO
* CMake, GoogleTest and how to refactor a large codebase
* Cython and Python/C integration
* Working collaboratively on a diverse global team

# My experience

GSOC was a fantastic experience for me, as I felt very welcome amongst two
communities (MDAnalysis and GROMACS). I hope I was able to give back! I was
constantly challenged throughout and this helped me learn and grow with big
improvements both in my technical skills and approach to computational problem
solving. Thanks all for having me along!





