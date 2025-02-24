---
author: "Hugo MacDermott-Opeskin"
title: "How Geoscience Can Help Fix Molecular Simulation’s Data Problem"
date: "2025-02-22"
description: "We can and should do better"
ShowToc: false
ShowBreadCrumbs: false
---


**TLDR:** Molecular simulation can enter its big-data era if we solve some software challenges and go cloud-native. Geoscience has faced many of these challenges and we can learn from their approach.

Atomistic molecular simulations (otherwise known as molecular dynamics) have advanced significantly from the [first simulations](https://journals.aps.org/pr/abstract/10.1103/PhysRev.136.A405) of liquid argon with a Lennard-Jones potential in 1964\. With [Moore’s law](https://en.wikipedia.org/wiki/Moore%27s_law) driving compute ever cheaper and software and expertise becoming democratised, [more people than ever](https://pmc.ncbi.nlm.nih.gov/articles/PMC6209097/) are using molecular simulation to drive insight and innovation in their scientific domain.

However, our field has yet to fully embrace modern software and big-data practices. While we leverage ever faster accelerators, novel algorithms, and machine learning (ML) at all stages of the simulation pipeline, we have not made the same progress in managing, sharing, and analyzing data at scale.

## Where we are now

In my opinion, our primary challenge is that  **we largely don’t treat our data in a [FAIR](https://www.go-fair.org/fair-principles/) compliant manner conducive to big-data approaches.** In the era of the open internet and with the promise of novel ML driven analysis approaches, we should already have vast repositories (TBs-PBs) of open-access FAIR compliant simulation data (first simulations \~60 years ago\!). However in reality it is [simply not there](https://elifesciences.org/reviewed-preprints/90061v1) in large enough volumes, consistently usable formats and findable locations to drive the kind of large-scale meta-analyses and ML approaches that could push molecular simulation forward.

I am far from the first person to talk about this [issue](https://arxiv.org/abs/2407.16584), and there are (and have been) some valiant efforts to rectify this problem (in particular the [MDDB effort](https://mddbr.eu/)). Kudos to these people, as this remains a challenging technical and socio-cultural problem. Part of our issue I (personally) think comes from our heritage adjacent to chemistry, a field somewhat notorious for [poor data deposition practices](https://chemrxiv.org/engage/chemrxiv/article-details/676190f981d2151a020807c8) and a focus on blockbuster papers over big-data open research (sorry chemists). Additionally, when simulations that are now routine took months of supercomputer time, the focus was understandably on improving performance rather than scalable analysis.

In 2025 I am going to label the current state of affairs a poor effort from our field as a whole, with signs of improvement on the horizon.  However the point of this article is not to chastise, but to argue that we are **lacking some fundamental software tools that could democratise large scale open molecular simulation data.** In other words, we need to cross some technical hurdles first that might make the socio-cultural part less painful. 

## What can we learn from geoscience?

We are not the first field to face these challenges. Many in the computational and observational geosciences [have recognised](https://www.earthdata.nasa.gov/news/feature-articles/making-earth-science-data-fair#:~:text=FAIR%20principles%20state%20that%20metadata,know%20how%20to%20access%20data.) that FAIR data is a primary output at least as much as the written word. The sheer volumes of open geoscience data available from satellites, sensors and models (often provided by large governmental institutions such as NOAA, NASA, NCAR etc) has spurred a shift to a more cloud-native open-data approach, typified in the [Pangeo](https://www.pangeo.io/)  project which has pioneered methodologies for big-data geoscience analysis using open source tools. Pangeo and related endeavours focus on breaking down barriers to big-data analysis of geoscience data by producing [Analysis Ready Cloud Optimised](https://www.frontiersin.org/journals/climate/articles/10.3389/fclim.2021.782909/full) (ARCO) research data for general consumption over the open internet. I highly recommend reading [this paper](https://ieeexplore.ieee.org/document/9354557) and also [this one](https://www.frontiersin.org/journals/climate/articles/10.3389/fclim.2021.782909/full) for a comprehensive view.

Let's break down what this means a little bit more. In the Pangeo vision, data consumers shouldn’t have to download and organise large volumes of data on their local networks (up to many TBs in the geosciences case) instead they should be able to use the data directly from an online source (e.g via HTTP on S3 or similar). Data should be analysis ready and not need further post-processing and duplicated scientific [toil](https://sre.google/sre-book/eliminating-toil/) to gain insight. Importantly data should be ready for parallel computing workflows out of the box and support efficient indexing and access across all available dimensions, including at the dataset level.

These data characteristics have many advantages including: 

* FAIR, ML ready scientific data  
* Metadata rich catalogs that embed data in scientific context  
* Lower barriers to participation (no supercomputer required to start analyzing data)  
* Reproducible and collaborative with consistent data sources  
* Scalable larger-than-memory [parallel analysis through object stores](https://gallery.pangeo.io/repos/earthcube2020/ec20_abernathey_etal/cloud_storage.html) (e.g S3, GCS Azure Blob)  [can achieve 50GB/sec+ throughputs](https://dl.acm.org/doi/10.1145/3127479.3128601), and is in effect almost infinitely scalable  
* Cost efficiency and access to [data-proximate computing](https://docs.coiled.io/blog/data-proximate-computing.html) can minimize transfer and compute costs  
* Reduction in duplicated effort and compute across the ecosystem  
* Easier to cross link metadata rich information via relational database entries


This has been enabled by an open source software stack built on a few key foundations, some originating from within the geosciences community, others more cross platform (full list [here](https://www.pangeo.io/)):

* [Zarr](https://zarr.dev/): High performance tensor storage  
* [Dask](https://www.dask.org/):  Parallel computing in Python  
* [Intake](https://github.com/intake/intake): Data cataloging and access  
* [Xarray](https://xarray.dev/): Frontend package that simplifies reading and analysing large scale array data

Don’t believe how cool Pangeo is? Check out the [gallery](%20https://gallery.pangeo.io/index.html) and see these principles in action. Honourable mention, the Pangeo team has also developed [Pangeo Forge](https://pangeo-forge.org/), a platform for developing ARCO data recipes from traditional data sources. 

## Where molecular simulation falls short

Let's take stock of where molecular simulation is currently sitting on some of these axes.  
A typical analysis workflow in molecular simulation might involve downloading a compressed (e.g XTC, DCD, AMBER NetCDF or one of [40+ other formats](https://userguide.mdanalysis.org/stable/formats/index.html)) trajectory in its entirety, inferring its contents by squinting at its filename and then desperately cross referencing with a paper (probably behind a paywall), loading it into a frontend analysis package (e.g [MDAnalysis](https://www.mdanalysis.org/), [MDTraj](https://www.mdtraj.org/1.9.8.dev0/index.html) or similar) and then going from there. This workflow lacks nearly all the advantages listed above **despite basically being very similar under the hood.** All we are doing is reading in large 3D arrays of coordinates and some information about the simulation topology. This is already very clunky let alone wanting to do this reproducibly at scale.

Current shortcomings include:

* **Lack of cloud-native file formats:**  Of the 40+ formats in use, few support cloud-native (+- chunked) access (exceptions: NetCDF, HDF5-based formats like H5MD)  
* **Minimal built-in parallel computing support:** Effective parallel analysis of arrays requires [chunked data](https://pythonspeed.com/articles/mmap-vs-zarr-hdf5/), which is uncommon. Support for larger than memory datasets and lazy array computing paradigms is also lacking  
* **Poor remote indexing and slicing**: Data usually needs to be downloaded before analysis, introducing inefficiencies and dependencies on local data layout  
* **Sparse metadata-rich, machine-readable catalogs**: Data often lacks clear scientific context alongside raw data that can be read by a machine  
* **Limited open internet storage of large datasets**: Some progress has been made, but adoption is inconsistent. Lack of a specialist platform likely has not helped (think how much of a difference the PDB has made to structural biology\!) Even when these datasets are availble they can't be indexed and analysed efficently

Bit of a flop from us here in my opinion. Pangeo and other efforts have proven that  it's not that hard to store a 3D array of coordinates in a cloud native way, so I really don’t think we have any good excuses for not having analysis ready “simulation lakes” (analogous to [data lakes](https://en.wikipedia.org/wiki/Data_lake)) available. 

## MVP?

What is an MVP as we move towards a better paradigm for molecular simulation? Quick ballpark estimate:

* Store a large (100+ >500GB) series of simulations all on cloud (e.g S3)

* Run parallel analysis on a cross simulation aggregated property, efficently leveraging distributed computing

* This analysis is repeatable directly from source data outside a cloud environment in a relatively small Jupyter notebook


This doesn't sound too far fetched and hits most of the ARCO goals. To get there and build for the future we need to take some practical software steps.

## Practical steps forward

1. **Adopt a cloud-native, chunked, sliceable, parallel-ready file format.**  
   At the risk of falling into the [standards](https://xkcd.com/927/) trap we need to work with a cloud native chunked, sliceable, parallel analysis ready file format. The best hope for this is a Zarr native format or HDF5 based format like [H5MD](https://www.nongnu.org/h5md/). 

   Shameless self plug, I have already contributed to some exploratory work in this domain with a [Zarr native trajectory format](https://github.com/Becksteinlab/zarrtraj/tree/main) based on the H5MD specification that can also read H5MD directly ([under review at JOSS)](https://github.com/Becksteinlab/zarrtraj/blob/main/joss_paper/draft_under_review.pdf). In this paradigm you can slice trajectory frames remotely (e.g from S3) without needing to send any more than a few frames over wire. Importantly using a chunked format you can also analyse this data in a massively parallel manner using Dask. We would love to get your feedback if this interests you\!

2. **Develop metadata-rich catalogs to contextualize simulations.**  
   We need to prepare metadata rich catalogs that link simulations to their scientific context. These have to be machine readable. [Intake](https://www.youtube.com/watch?v=oyZJrROQzUs) would be the perfect vehicle to do this. As a chemistry related example there is already an RDKit driver for intake, see [here](https://github.com/greglandrum/intake-rdkit) and informative blog post [here](https://greglandrum.github.io/rdkit-blog/posts/2023-03-09-using-intake-for-chemistry.html). I have a few ideas in this space, get in touch if interested.

3. **Enhance frontend package cooperation and performance prioritization.**  
   Tools like MDAnalysis, MDTraj and related need to be ready to work with cloud-native innovation. Indeed I would argue that these packages, as some of the most central, cross-platform surfaces in the ecosystem may need to lead this change. 

4. **Improve parallel analysis capabilities.**  
   We need to up our game on parallel analysis. [Dask](https://www.dask.org/), [Apache Beam](https://beam.apache.org/) and related parallel computing libraries that can work with chunked formats are preferable. Even better if these capabilities can be integrated into frontend packages like MDAnalysis and MDTraj.

5. **Learn from the Pangeo ecosystem.**  
   The Pangeo stack [is evolving and obviously is not perfect](https://matthewrocklin.com/pangeo-2.0.html), we should collaborate, cross pollinate and monitor how things shake out.

6. **Shift our cultural mindset toward valuing FAIR data.**  
   We need to change our culture to value FAIR data more than it currently does, projects like MDDB may be great enablers of this.

**The future of molecular simulation (?)**

In my opinion, molecular simulation can have its big-data era if we work on addressing some of these listed above (including our culture). **The good news is that a lot of the technology is already there, we just have to use it effectively.**

Given the demonstrated impact of molecular simulation in areas like drug discovery and materials research, we should strive for better as a community so we can deliver better  scientific outcomes. Just as breakthrough technologies in protein structure prediction  ([Alphafold](https://www.nature.com/articles/s41586-021-03819-2) and related) were built on top of the data-at-scale paradigm enabled by the PDB, we can open the door to future transformational advancements if we get our act together.


Finally, this is just my perspective based on my experiences. If any organizations have already solved these issues internally, please reach out—I’d love to learn what it took\! If you have any more general feedback or thoughts I would love to hear them as well.



**Bonus: Geoscientists and molecular modellers working together on some rather wonky molecules**

![Geoscience and molecular simulation](/geo_molecular.png)
