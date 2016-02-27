---
layout: post
title: "Writing Data"
subtitle: "a guide to choosing & using data formats"
date: 2077-10-22
permalink: writing-data
---

## Introduction

If you were to go hiking up the BC coastline, you may well encounter the majestic scene of sunshine falling on a brown bear as it swims along the coast, and snap a picture for posterity:


> ```0x818030f0  0xd0000000  0x0001ffff  0x002f4c71  0x9002d02c  0xa294b5e0  0xb0000004  0x48008add 0x6294b5d7  0xe0e8102c```

> ```>gi|19343502|ref|NC_003427.1| Ursus arctos mitochondrion, complete genome
> GATCACACATAACTGTGGTGTCATGCATTTGGTATCTTTTAATTTTTAGGGGGGGAACTTGCTATGACTC
> AGCTATGACCGTAAAGGTCTCGTCGCAGTCAAATAATTTGTAGCTGGGCTTATTTATCTTTCACGGGTCG```

> ```C32998861501009969002291971 216442238255351461562-127722120 1110101511 102351411
> 11002291101296110329977020142480110002331247012206502331247012209002331242013301
> 00023312350133011002331225013301200233121101330138023311850133016002220170133016
> 70233116401330172023311540133017802331157013302000233115701```

*A GRIFFIN gamma ray from a nuclear decay; FASTA brown bear genome; and a WOD-format ocean climatology profile for the waters off Penrose Island, BC*

Just like this snapshot might fail to capture the moment for your friends back home, the way we record data is *vital* for successfully using and sharing that data later. But like many skills in the brave new digitally literate world of modern research, most of us have had exactly zero training in writing data, wing it and hope for the best. [Jenny Bryan](https://twitter.com/jennybryan) and [Dean Attali](http://deanattali.com/) recently captured some of the more popular catastrophes this produces in their recent [bad data bingo card generator](http://daattali.com/shiny/bingo/):

![bingo!](/img/bingo.png)

In this post, I'm going to walk through some regret-minimizing strategies for deciding how to record your data, and point out some pitfalls to avoid. Rather than trying to stamp-collect every data format out there, I'm going to focus on *archetypical* formats that will get the job done almost all the time. Many more specific formats are really just reimaginings of these; straying too far from this family comes at a steep price. Also, I'm only going to consider *file formats* in this post; in many cases, we should probably be using a proper database - but that is a whole other post.

## Basic Considerations

No matter what your data represents, there are a few universal considerations that will influence our data-writing decisions:

 - **Size** does in fact matter when it comes to data; depending on your hardware, past about 100 GB or so, you're going to start having a bad time with most plain-text formats; reading and lookups get slow, and any sort of open data sharing becomes increasingly infeasible.
 - **Intended Use** can be a determining factor when other considerations don't provide clear guidance; is this data intended for consumption on the web? Is it going to underly or replace a database? If this data is destined for something more than being directly sucked into a script for your next paper, it pays to plan ahead.
 - **Natural Structure** is exhibited by many datasets. Do you make the same measurements over and over for many samples? Will many of those samples be missing data? Are there hierarchical relationships in your data? Does data arrive from your instrumentation in an unpredictable manner? Like a good tailor, choosing the right fit for your data to begin with can make all the difference.
 - **Culture**, as always, trumps all. If all your colleagues encode their data in Shakespearian-style sonnets, then get your [iambic pentameter toolkit out](https://github.com/rossgoodwin/sonnetizer) - data usage is all about communication and legibility, and you'll have the easiest time if you speak the common tongue.


















 - options:
  - csv (wide or tall): repeated observations of many subjects
  - json, xml: simple hierarchical, web ready
  - netCDF, HDF5: high performance hierarchical
  - byte-stanza: flexible, semi-legible, medium efficiency
  - bit packing: flexible, self-describing
 - decision flow chart
 - last word: respect format orthodoxy
