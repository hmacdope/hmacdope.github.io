---
layout: post
title:  "Looking Outside In"
date:   2020-05-19 08:58:08 +1000
categories: 
---

# Go with the flow

This week so far has been an exercise in figuring out how things are going to work. All going smoothly so far, communicating well with my mentors and working on raising issues and implementing new tests in the GROMACS TNG repo. Getting used to a large codebase has been a bit of a challenge although documentation has helped a lot in this regard. Currently working only with the high level API and implementing some more realistic testing scenarios for reading trajectories.

Some challenges have included

* Differentiating between GROMACS and TNG library errors/failures.
* The maturity of the high level API is unclear. I think the low level API is more thoroughly tested.
* Writing tests without a testing framework is labour intensive, I think we will switch to Google Test and Google Mock.
* Getting used to the build system, for mixed C/CXX compilation.

# Lessons learnt

* **If in doubt gmx dump it out**

gmx dump is your best friend when it comes to debugging trajectories, as it can dump any file straight to STDOUT. Don't assume anything about a TNG trajectory until you have a look at the info in gmx dump!

* Its probably not GROMACS

At least so far there have been several instances where it is unclear whether there is a problem with trajectory read or trajectory write.
So far after investigation the culprit is almost always my code or something about TNG (mostly just me).

* Work in the evening.

Far easier to work in the evening when I can get good feedback quickly and trade back and forth.

* Watch those pointers!

Pointers never fail to confuse me, this is more of a comment than any particular lesson.
Just be careful and take your time. 

# Next steps

* Get high level read tests merged, have to wait for Clang-tidy branch to be merged first.
* Separate C and CXX compilers in the CMake setup.
* Explore GoogleTest, or at the very least look at GROMACS make test, doing it all by hand is a pain.
* Start on low level read tests.
* Attend the GMX dev meeting and introduce myself.









