---
layout: post
title:  "Py In The Sky"
date:   2020-08-01 08:58:08 +1000
categories: 
---

# Progress

Progress continues apace on PyTNG, with some very promising strides in getting
the blocks read and smushed into numpy arrays with a minimum of copying.
We can now "spool" the TNG trajectory into numpy arrays, ie read every present
block into the new `TngDataBlock` datastructure, which comes with its own
Cython memory management etc. This is a really good step and 


# Exposing myself 

Next steps are two fold. Firstly we need to figure out how to seek the TNG file
easily. Currently the number of total data frames and their location is not known when we first
start the "spool" operation so we cannot acquire just certain sections of the
file. Secondly need to figure out how to expose the blocks to the user. The
data available at each timepoint is not homogenous and can vary depending on
the stride with which they are deposited. As such im not sure exactly how to
iterate, should we be iterating over the available time points for each data
member or should we only have the blocks available at each time point that are
actually there. Perhaps some guidance on what is required on the MDA side would
be useful.


# Possible problems?

None so far, just getting the balance right between TNG++ and PyTNG has been a
bit tricky. There are only so many hours in the day and PyTNG has required a
lot of focus to learn a Cython and apply it quickly.

# Next steps

Figure out how to seek a TNG file efficently so that we can access file
elements quickly without looping over the whole file or dumping the whole
thing into memory. Also need to decide how to expose the block interface to
users. How do we deal with only some attributes being available at differing intervals?
