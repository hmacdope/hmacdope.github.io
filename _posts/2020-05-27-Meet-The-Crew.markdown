---
layout: post
title:  "Meet The Crew"
date:   2020-05-27 08:58:08 +1000
categories: 
---

# Meeting the community

Its been a busy week! First among some exciting things was meeting the GROMACS crew at the fortnightly developer teleconference. Its an open roundtable where everyone can discuss issues and ideas related to the GROMACS codebase.
Something that struck me is that *detail matters*. Making detailed decisions as a large group of developers is difficult but required and talking through the changes in all the gory detail is a necessary step. 

Paul (my mentor) introduced me to all the other developers and gave me a chance to ask some questions to the dev community. I took the chance to ask about the TNG format in the GROMACS format in general. My aim was to understand the following:

* How many people actually use TNG as it stands?
* What are the advantages?
* What are the pain points?

The answers I got were as follows (as far as I could tell).

* Not many people use TNG as it is
* Compressed storage of velocities are a big advantage!
* Pain points are lack of usability, high level API and GROMACS integration.
* Crucially, people indicated that they would use it if the above pain points were fixed.

These have not modified my overall goals, but have instead reinforced the need to achieve them as I laid out originally.
Key to this is improving ease of usability.



# Code progress

clang-tidy and CI are all finally merged and setup in [!1](https://gitlab.com/gromacs/tng/-/merge_requests/1) and [!2](https://gitlab.com/gromacs/tng/-/merge_requests/5). These will provide me with the base environment I need to work seamlessly in the GROMACS project. 

Progress on tests continues with [!3](https://gitlab.com/gromacs/tng/-/merge_requests/3). This MR is proving a staging ground for debugging and testing against GROMACS trajectories. Currently I am quite reliant on Magnus (original developer of TNG) for bugfixes to do with the actual reading of trajectories. An example of this is MR [!6](https://gitlab.com/gromacs/tng/-/merge_requests/6). This fixed [#4](https://gitlab.com/gromacs/tng/-/issues/4) and improved [#5](https://gitlab.com/gromacs/tng/-/issues/4). I aim reduce my reliance on Magnus for a deep understanding of the C code, as Magnus is not an actual mentor and I will need to be able to do this by myself. 

Tools that will help me with this that I have picked up

* Valgrind
* GDB
* Okteka

Valgrind has been especially useful in finding problems in trajectory reading.

I have also been exploring using googletest which implements fixtures and an automated testing framework. My MR [(!5)](https://gitlab.com/gromacs/tng/-/merge_requests/5) puts the googletest framework into TNG so that we can use it to do all our testing. A little snippet of how that might work is shown below.

# Code snippet

```c++
#include "tng/tng_io.h"
#include "gtest/gtest.h"

TEST(ArgonTests, trajectory_open) {
    tng_trajectory_t traj;
    tng_function_status stat;
    stat = tng_util_trajectory_open("./example_files/argon_npt.tng", "r", &traj);
    ASSERT_EQ(stat, TNG_SUCCESS);
}
```
This can then be linked against `gtest_main` which runs the tests by calling all the relevant macros. The following snippet shows how this is done in CMake.

```cmake
add_subdirectory(gtest)
enable_testing()
include_directories(${gtest_SOURCE_DIR}/include ${gtest_SOURCE_DIR})

add_executable(tng_io_low_level tng_io_low_level.cpp)
target_link_libraries(tng_io_low_level tng_io)
target_link_libraries(tng_io_low_level gtest gtest_main)
```

# Lessons learnt

Making a mess of my testing MR [(!3)](https://gitlab.com/gromacs/tng/-/merge_requests/3) was a bit of a wakeup call in taking your time and planning your feature, branch and MR properly. I branched off Paul's feature branch and then made an upstream target branch manually that I then struggled to rebase onto master. In future branching off master and merging back into master seems to be the smartest option.

# Next steps

* Figure out cause of failed position read in [#5](https://gitlab.com/gromacs/tng/-/issues/5)
* Merge final high level tests [(!3)](https://gitlab.com/gromacs/tng/-/merge_requests/3)
* Merge googletest [(!5)](https://gitlab.com/gromacs/tng/-/merge_requests/5) and then refactor tests to use the testing framework.

