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
on this for the majority of the bonding period as well as the first coding
period.

First I added tests in !3 which then revealed problems in the base API that were fixed in !6 !12
and !17.  I then made these additional tests into a formal test suite employing
googletest in !16 !20 and !21. This revealed some more API elements that could be improved which were
touched up in !22, !23 and !25.

Once we had a test suite that could be used to check library correctness on the
major routines we then started to work on modernising the TNG API itself
towards C++ compilation and in turn modernisation of the library itself. I
acheived full C++ compilation in a combination of !26, !27, !29, !33 and !34. 

From here I started on modernization of the library classes and constructs
themselves to modern C++. These progressed in !28 which outlined the structure we were aiming
for, !32 which defined a specific API for blocks, !35 that uodated the
trajectory API and !36 which progressed on the IO elements. These were huge
tasks and due to this and some changing circumstances, these are all still open
MRs. I plan on working on these into the future but progress will be slow as I
do not have solid blocks of time to dedicate. 

From here I switched focus a bit to PyTNG, a set of python bindings designed
for use by MDAnalysis although technically a separate library. 

