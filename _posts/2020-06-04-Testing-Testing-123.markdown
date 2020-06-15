---
layout: post
title:  "Testing Testing 123"
date:   2020-06-04 08:58:08 +1000
categories: 
---

# Progress

Met with Paul and Richard to outline what we will be doing for the first coding period, which was very productive. 

We agreed on the following.

* Testing is required, but we should aim to move on within a week to data structure and API changes, finish some simpler regression tests and move on.
* If I want to change things in the docs that I found confusing I should just go for it.
* We need to validate against the advantages of the TNG format. Inclusion of a trajectory with bond information into the test suite is a good idea along with a TNG file with some trajectory information. Magnus had some good suggestions related to this in [#6](https://gitlab.com/gromacs/tng/-/issues/9).
* A big next step will be to try and build the code base in C++, omitting the C compiler. Paul indicated that bugs will probably reveal themselves in this stage.
* All new C++ code should go in new source file(s) so we can separate the old and new code bases.


# Code progress

New tests written by me are merged in [!3](https://gitlab.com/gromacs/tng/-/merge_requests/3) and  lots of related bug fixes have gone in over the top eg [!6](https://gitlab.com/gromacs/tng/-/merge_requests/6) through to [!10](https://gitlab.com/gromacs/tng/-/merge_requests/10). Building a test binary allowed me to learn the API and its quirks efficently. Good news is we have identified and eliminated bugs that have been in there for years, so we are already making good progress.

Something we have identified is that a general purpose test binary is inadequate for the complexities of the various tests and trajectories.  Combining this with the advantages of googletest (merged in [(!5)](https://gitlab.com/gromacs/tng/-/merge_requests/5)) has meant we have decided to make a regression testing battery. 

This will prove advantageous for validating the changes to the API we plan on making.
As evidenced by a few MRs going awry, there needs to be regression tests to ensure basic functionality isn't broken by prospective changes.


# Lessons learnt

Be bold, if not many people use the current API or docs, can make some drastic changes without upsetting anyone.

We need better coverage for tests and integration and to move them into the CI/CD config.  Do we want them for every commit or just on merge train (choo choo)?

The TNG frame syntax is a bit confusing (to me) and may need to be changed at some point. At the very lest I will need to add a bit more to the docs.

I've got the hang of the gitlab setup and how to best manage rebasing, pipelines, merge and linking issues. The only gitlab related thing is that MRs for which we want to squash commits (almost all of them) will have the name of the FIRST commit, so think ahead for the first commit in a new branch.


# Next steps

* Change tests to water ensemble for bond information and add a velocities test [#6](https://gitlab.com/gromacs/tng/-/issues/9).
* Finish googletest regression binary [(!16)](https://gitlab.com/gromacs/tng/-/merge_requests/16)
* Change the CI/CD config to run the tests [(!13)](https://gitlab.com/gromacs/tng/-/merge_requests/13)
* Try and register the tests with CTest so they work with make test
* Try and compile the whole thing with the C++ compiler.

