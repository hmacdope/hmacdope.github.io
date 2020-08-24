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
@richardjgowers (Richard) for all the help and guidance.

The purpose of this blog post is to summarize the work I have done and to indicate what is still
left to do. I will break it down chronologically so that what I have done can
be followed easily.

# TNG time

I started GSOC working on the TNG library, with the aim of adding tests,
understanding the API and converting the older C style API it to C++.  I worked
on this for the majority of the bonding period as well as the first and half of
the second coding periods.

First I added tests in [!3](https://gitlab.com/gromacs/tng/-/merge_requests/3) which then revealed problems in the base API that were fixed in [!6](https://gitlab.com/gromacs/tng/-/merge_requests/6) [!12](https://gitlab.com/gromacs/tng/-/merge_requests/12)
and [!17](https://gitlab.com/gromacs/tng/-/merge_requests/17).  I then made these additional tests into a formal test suite employing
googletest in [!16](https://gitlab.com/gromacs/tng/-/merge_requests/16) [!20](https://gitlab.com/gromacs/tng/-/merge_requests/20) and [!21](https://gitlab.com/gromacs/tng/-/merge_requests/21). This revealed some more API elements that could be improved which were
touched up in [!22](https://gitlab.com/gromacs/tng/-/merge_requests/22), [!23](https://gitlab.com/gromacs/tng/-/merge_requests/23) and [!25](https://gitlab.com/gromacs/tng/-/merge_requests/25).

Once we had a test suite that could be used to check library correctness on the
major routines we then started to work on modernising the TNG API itself
towards C++ compilation and in turn modernisation of the library itself. I
acheived full C++ compilation in a combination of [!26](https://gitlab.com/gromacs/tng/-/merge_requests/26), [!27](https://gitlab.com/gromacs/tng/-/merge_requests/27), [!29](https://gitlab.com/gromacs/tng/-/merge_requests/29), [!33](https://gitlab.com/gromacs/tng/-/merge_requests/33) and [!34](https://gitlab.com/gromacs/tng/-/merge_requests/34). 

From here I started on modernization of the library classes and constructs
themselves to modern C++. These progressed in [!28](https://gitlab.com/gromacs/tng/-/merge_requests/28) which outlined the structure we were aiming
for, [!32](https://gitlab.com/gromacs/tng/-/merge_requests/32) which defined a specific API for blocks, [!35](https://gitlab.com/gromacs/tng/-/merge_requests/35) that updated the
trajectory API and !36 which progressed on the IO elements. These were huge
tasks and due to this and some changing circumstances, these are all still open
MRs. I plan on working on these into the future but progress will be slow as I
do not have solid blocks of time to dedicate. 

All the work I have done on TNG itself can be found in my TNG
[fork](https://gitlab.com/hugomacdermott/tng) as well as open PRs on the
library [itself](https://gitlab.com/gromacs/tng).


# PyTNG time

From here I switched focus a bit to
[PyTNG](https://github.com/MDAnalysis/pytng), a set of python bindings designed
for use by MDAnalysis although technically a separate library. This required
changing gears a little bit as well as learning Cython, which was initially a
bit of a learning curve for me. 

Firstly I changed the raw TNG libraries exported with the PyTNG itself to
reflect the bugfixes obtained as part of the earlier improvements to the TNG
library. This was incorporated as part of
[#28](https://github.com/MDAnalysis/pytng/pull/28). I then improved the TNG
calls in the library itself in [#29](https://github.com/MDAnalysis/pytng/pull/29).

Following some design discussions, we then moved towards a newer design for the
bindings, so as to be able to read all the blocks available in a TNG file with
maximal efficency. This was acheived in merging
[#32](https://github.com/MDAnalysis/pytng/pull/32) and ongoing work in
[#38](https://github.com/MDAnalysis/pytng/pull/38), which are a total
redesign of the whole PyTNG API.

The end result of this is a working implementation that can read any TNG block
as of #32 with improvements in #38.
I also added docs and examples as
part of [#38](https://github.com/MDAnalysis/pytng/pull/38). Profiling and
timings indicated high performance of the bindings, with the library largely IO
bound at the TNG API (pure C) level.

I plan on extending to TNG writing as well as integrating into MDAnalysis
following GSOC. I have raised issues in PyTNG to make sure things that still
need to be completed are apparent to people following on.

# What have I learnt

Things I have learned in GSOC include

* C++ class design
* C++ for binary IO
* CMake, GoogleTest and how to refactor a large codebase
* Cython and Python/C integration
* Working collaboratively on a diverse global team

# My Experience

GSOC was a fantastic experience for me, as I felt very welcome amongst two
communities (MDAnalysis and GROMACS). I hope I was able to give back! I was
constantly challenged throughout and this helped me learn and grow with big
improvements both in my technical skills and approach to computational problem
solving. Thanks all for having me along!





