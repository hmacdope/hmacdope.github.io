---
layout: post
title:  "Starting GSoC"
date:   2020-05-13 08:58:08 +1000
categories: 
---

# Intro

This is an intro to my GSoC project as well as an update for where we are up to!
I submitted two applications to MDAnalysis for GSoC, but indicated my preference to work on a really exciting cross-team project.
Lucky for me, I was accepted for that project, titled **TNG: the trajectory Format for the future of simulation**. My GSoC page can be found [here](https://summerofcode.withgoogle.com/projects/#5116604104310784). A brief summary of the project is given below:

The TNG format was developed several years ago as part of the GROMACS consortium, with the aim that it would replace the compressed .xtc format and very disk intensive uncompressed .trr format. The TNG format provided some key advantages over these older trajectory storage mechanisms. Many of these center around capturing additional information that accompany the simulation, encouraging  **reproducibility**. Additionally the format is designed to be **extensible** and **flexible**, so that any arbitrary data can be stored, in stark contrast to other trajectory data formats.

Sadly, the TNG format did not see wide adoption, as active development of the API halted. This was exacerbated by the fact that GROMACS pushed aggressively towards C++ in their codebase while the TNG API is written in C. Additionally, some of the high level features of the TNG API never reached maturity, meaning complicated low level calls to the API are used in GROMACS rather than the proper abstractions.

This is where I come in. Both GROMACS and MDAnalysis decided to partner up to work towards bettering the TNG API with the dual goals of  simplicity and better abstractions. As such my project will involve refactoring the TNG API, both porting to C++ and improving usability.

I'm super excited to (hopefully) make this a reality!

# Our first meeting

We had our first video conference meeting last night with both GROMACS and MDAnalysis people.
Here are some of the key aspects to come out of that, both philosophical and practical.

* How are we going to communicate clearly and across two communities that have different cultures and practices?

This is key to ensuring open source principles are followed. The GROMACS dev list is a bit neglected, so we decided to use the new [GitLab TNG repo](https://gitlab.com/gromacs/tng) as our primary form of communication. We also discussed the importance of flagging early if communication channels are not working.

**the link above is where the majority of the work I will be doing will be located**


* How to contribute to the community?

As I will be primarily interacting with the GROMACS community, I fall outside the MDAnalysis community forums and developer list. The above helps to address some of this, but we also decided that I should attend the fortnightly GROMACS dev meeting, if only to learn by osmosis.

* Figure out the best way to get feedback fast!

What hours are mentors available so that I don't need to wait 12 hrs for a code review.

# Practical things

Some practical/technical things we discussed:

* work component by component, don't try and rip down the whole codebase and start again, this will be impossible given time constraints, wasteful and error prone. 
* working with mixed C++/C is easy, use C++ compiler and call C routines as `extern "C"` 
* work top down, refactoring the high level interface first will be easiest followed by folding in or eliminating sections of low level interface
* work out the tools! need to figure out the CI/CD on GitLab as well as how to get feedback

# Next steps

What are we going to do next!

* read the all the exiting API docs and find what is missing.
* make a new test case to get used to high level API and explore how CI/CD pipeline works.
* attend the GMX dev meeting and introduce myself.

Thats all for now! Quite a lot to chew on.








