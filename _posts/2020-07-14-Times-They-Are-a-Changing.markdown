---
layout: post
title:  "Times They Are a Changing"
date:   2020-07-14 08:58:08 +1000
categories: 
---

# Progress

Refactoring continues apace, with the new block API stabilising as is. For testing purposes many data elements remain public, however a further step will be to hide a lot of the details from the caller. Additionally, something that is not fully clear to me yet is wether `TngGenBlock` should be seperate from the base class. I think this will not become fully clear until I explore the IO in greater detail. The molecule classes have now also been refactored. They expose their inner workings to each other with the  `friend class` mechanism, which is something I need to do for the IO and block elements.

The new trajectory API is coming along nicely and has stabilised somewhat in !35. This was much simpler than the blocks. Key to this was stripping a lot of the excess data members from it, and making it a vessel for blocks, each of which has its own data, reducing the god-class-ness of the previous equivalent struct.

I have started the IO section in the `TngFileReader` and `TngFileWriter` classes. This is where the hard work comes.

# Unexpected turn of events

In some sad news, my mentor Paul is unwell and has had to reduce his involvement in the project quite a lot. This is somewhat of a problem as I need 2 reviewers to merge code into TNG and being my primary mentor is above the call of duty for Magnus. After some discussion with the mentors, I have decided to switch my timeline around a little bit and focus 2/3 on the PyTNG bindings that was originally slated for the 3rd coding period. 1/3 of my time will be used to continue refactoring the TNG C++. This means that Oliver and Richard can help me, which I look forward too.


# Next steps

Switch focus to 2/3 PyTNG and 1/3 TNG itself. Learn some Cython!
