---
layout: post
title: "Writing Data"
subtitle: "an introduction to choosing & using data formats"
date: 2016-03-06
permalink: writing-data
---

## Introduction

If you were to go hiking up the BC coastline, you may well encounter the majestic scene of sunshine falling on a brown bear as it swims along the coast, and snap a picture for posterity:

```
0x818030f0  0xd0000000  0x0001ffff  0x002f4c71  0x9002d02c  0xa294b5e0  0xb0000004  0x48008add 0x6294b5d7  0xe0e8102c

>gi|19343502|ref|NC_003427.1| Ursus arctos mitochondrion, complete genome
GATCACACATAACTGTGGTGTCATGCATTTGGTATCTTTTAATTTTTAGGGGGGGAACTTGCTATGACTC
AGCTATGACCGTAAAGGTCTCGTCGCAGTCAAATAATTTGTAGCTGGGCTTATTTATCTTTCACGGGTCG

C32998861501009969002291971 216442238255351461562-127722120 1110101511 102351411
11002291101296110329977020142480110002331247012206502331247012209002331242013301
00023312350133011002331225013301200233121101330138023311850133016002220170133016
70233116401330172023311540133017802331157013302000233115701
```

*A GRIFFIN gamma ray from a nuclear decay; FASTA brown bear genome fragment; and an (imitation) WOD-format ocean climatology profile for the waters off Penrose Island, BC.*

Just like this snapshot might fail to capture the moment for your friends back home, the way we record data is *vital* for successfully using and sharing that data later. But like many skills in the brave new digitally literate world of modern research, most of us have had exactly zero training in writing data, so we wing it and hope for the best. [Jenny Bryan](https://twitter.com/jennybryan) and [Dean Attali](http://deanattali.com/) recently captured some of the more popular catastrophes this produces in their recent [bad data bingo card generator](http://daattali.com/shiny/bingo/):

![bingo!]({{ site.baseurl }}/img/bingo.png)

In this post, I'm going to walk through some regret-minimizing strategies for deciding how to record your data, and point out some pitfalls to avoid. Rather than trying to stamp-collect every data format out there, I'm going to focus on *archetypical* formats that will get the job done almost all the time. Many more specific formats are really just reimaginings of these; straying too far from this family comes at a steep price. I'm only going to consider *file formats* in this post; in many cases, we should probably be using a proper database, and in all cases, there's much ado to be had about *metadata*, but those will be covered in forthcoming posts.

## Basic Considerations

No matter what your data represents, there are a few universal considerations that will influence our data-writing decisions:

 - **Size** does in fact matter when it comes to data; depending on your hardware, past about 100 GB or so, you're going to start having a bad time with most plain-text formats; reading and lookups get slow, and any sort of open data sharing becomes increasingly infeasible.
 - **Intended Use** can be a determining factor when other considerations don't provide clear guidance; is this data intended for consumption on the web? Is it going to underly or replace a database? If this data is destined for something more than being directly sucked into a script for your next paper, it pays to plan ahead.
 - **Natural Structure** is exhibited by many datasets. Do you make the same measurements over and over for many samples? Will many of those samples be missing data? Are there hierarchical relationships in your data? Does data arrive from your instrumentation in an unpredictable manner? We'll examine strategies for all of these in this post; like a good tailor, choosing the right fit for your data to begin with can make all the difference.
 - **Culture**, as always, trumps all. If all your colleagues encode their data in Shakespearian-style sonnets, then get your [iambic pentameter toolkit out](https://github.com/rossgoodwin/sonnetizer) - data usage is all about communication and legibility, and you'll have the easiest time if you speak the common tongue.

We'll revisit these considerations below, but first, let's take a tour of some of the options available to us for writing data.

## Data Archetypes

Deciding 'how to encode my data' feels like trying to order lunch at a diner with a zillion nearly-identical sounding things on the menu; we can make this a lot easier by narrowing our choices to a few greatest hits. In each of the broad categories that follow, I list a few 'patron saints' that exemplify but in no way exhaust each format, and some strengths and weaknesses of each.

### 1. Delimited Text

**Patron saints:** CSV, fixed-width columns  
**Strengths:** Simple, human-readable  
**Weaknesses:** Large files, many pitfalls, hard to record complicated structures

Delimited text is maybe the most obvious way to record data, particularly if you make the same fixed observations (columns) of an unknown or extensible set of subjects (rows) - a situation that describes a great swath of all experimental science. The 'columns' and 'rows' language comes from the fact that this is how a spreadsheet will represent this information, but there's nothing really intrinsic about that 'view' of the data; delimited text all shares the same structure: repeated text structures containing individual facts separated by a pre-defined delimiter.

The classic is of course the CSV, or comma-separated value file; the 'repeated text structures' in this case are the lines in the file, and the 'pre-defined delimiter' is a comma. CSVs are probably familiar to just about everyone, but other file formats fit this category too, if we're willing to get a bit abstract; the fixed-width column format does away with the commas or other explicit delimiters, by separating facts on a line via their position; the first *n* characters make the first column, the next *m* characters make the next column, and so forth. For my bioinformatics friends, FASTA actually belongs to this family, too - a new 'row' occurs every `>` character, the 'name' column ends with a space, and the 'description' column ends with a newline - it looks much different, but it's all still pre-defined text delimitation.

While delimited text is probably the simplest strategy to understand, its apparent simple structure makes it very easy to abuse. A few orthodoxies will help you avoid pain later:

 - **One fact, one column:** Avoid the temptation to mush multiple facts together into one piece of data; the canonical example of this is the date. To understand why this is a problem, tell me: what date does '01-02-03' represent? Remember: columns are free. If we had a year column, a month column, and a day column, we would have a much easier time parsing information later.
 - **Do not annotate data** in the same column as the data; common examples of this are appending special characters to the data (`*` to indicate a footnote or `?` to indicate possibly problematic data). Remember, you're going to want to analyze whole columns of data at once in software; if they don't all obey the same format, you'll find yourself in parsing hell later ('if `*` do this, if `?` do that...'). R actually weakly enforces this in how it reads CSVs as dataframes; for a detailed discussion of the disasters that data annotations cause in R, see [my recent addition to Software Carpentry's R lesson](https://github.com/swcarpentry/r-novice-gapminder/blob/gh-pages/04-data-structures-part1.Rmd#data-types). Again: columns are free. Make an 'annotations' column, and editorialize to your heart's content.
 - **Choose an unambiguous null token for missing data**. Think back to kindergarten: if you don't have anything nice to say, don't say anything at all (except `NULL`). When data is missing, do *not* replace it with something that could even remotely possibly be interpreted as data. Canonical bad behavior here is found on [Null Island](https://en.wikipedia.org/wiki/Null_Island), a place of singular significance in everything from [voter demographics](http://ideasillustrated.com/blog/2012/01/12/wisconsin-voters-banished-to-null-island/) to [Atlantic vulcanology](http://blog.geogarage.com/2015/04/about-null-island-at-000-n-000-e.html) every time someone uses `0` to represent a missing longitude and latitude. If you're planning on ingesting this data in a particular language, see that language's conventions for null characters; when in doubt, just say `NULL`.
 - **Have a unique identifier for each line**; data science nerds call this a *primary key*, and it is a column whose value uniquely identifies the row. Common examples are dates and times, serial numbers, or even simple row numbers. Note that this unique identifier might be formed by the combination of multiple columns, such as in the example of date and time. Why? See below.
 - **Use a consistent delimiter, including whitespace**. Avoid the temptation to add variable amounts of whitespace after your delimiter; this is often done to make columns line up when looking at the raw text file by eye. It is far more important that your data be legible to a computer than legible to your eyes, since looking at data by eye is a great way to get false impressions about it, particularly as it grows in size. If you insist on looking at your data as a table, RStudio will happily render it as one.

> It is more important that your data be legible to a computer than legible to you.

Speaking of null characters, a regular CSV might make sense if it looks like this:

```
name,coat,weight,likes_string
Frisco,calico,2.1,TRUE
Pixel,black,5.0,FALSE
Pika,tortoise,3.2,TRUE
```

But what if there's a lot of missing data in your study (your cats declined to report their weight or string preferences, for example):

```
name,coat,weight,likes_string
Frisco,calico,NULL,NULL
Pixel,NULL,5.0,NULL
Pika,tortoise,NULL,TRUE
```

That's a lot of `NULL` bloating our data; in this case, it may make sense to record our data in *long format* instead:

```
name,variable,value
Frisco,coat,calico
Pixel,weight,5.0
Pika,coat,tortoise
Pika,likes_string,TRUE
```

Long format is characterized by writing one measurement per line (with many lines for a single subject), while the *wide format* we began with writes one subject per line (with many measurements on that line). Long format has the advantage of making those missing values implicit, avoiding blowing up our file sizes on disk or our data frames in memory during analysis. It's also our first example of why those unique identifiers matter; take off the `name` column above, and there's no longer any way to tell which pieces of data belong to the same cat.

One of the places where simple delimited text files start to get hairy is with datasets that have a variable amount of measurements. For example, what if you're into recording information about groups of things *and* information about each member of that group - say the group of birds in a given tree, and something about each individual bird. One strategy is to make rows for the smallest unit of things you're measuring (in this case, each bird):

```
tree_number,tree_type,tree_height,bird_number,bird_type,bird_color
0,poplar,6,0,parrot,red
0,poplar,6,1,cockateel,blue
0,poplar,6,2,crow,black
1,spruce,10,3,chickadee,brown
1,spruce,10,4,crow,black
1,spruce,10,5,crow,black
1,spruce,10,6,crow,black
```

I guess this is ok, but we had to repeat ourselves *a lot* - the information for a given tree is copied for each bird in that tree. This is going to make our data unnecessarily large and slow to work with (and kind of hard to make sense of by eye). Instead, what if we had two files:

`trees.csv`:

```
tree_number,tree_type,tree_height
0,poplar,6
1,spruce,10
```

and `birds.csv`:  

```
bird_number,tree_number,bird_type,bird_color
0,0,parrot,red
1,0,cockateel,blue
2,0,crow,black
3,1,chickadee,brown
4,1,crow,black
5,1,crow,black
6,1,crow,black
```

We no longer repeat the tree information for every bird, saving us space and speeding up our analysis. Instead, we use the unique identifier `tree_number` as a shorthand in the `birds.csv` table to associate each bird with the tree we found it in. When we use the unique identifier from one table in another in this way, we call this a *foreign key*; this logic underlies basic database construction and usage, but it works exactly the same way when recording data in simple files.

### 2. Hierarchical Data

**Patron saints:** JSON, HDF5  
**Strengths:** Flexible, web-ready, easy to represent complicated structures  
**Weaknesses:** Files can be either large (JSON) or non-human-readable (HDF5)

#### JSON

At the end of the last section, we encountered some bird data that didn't fit nicely into a single table in a single file. We explored how to solve this by introducing 'primary keys' and 'foreign keys'; this is a good solution, but it can get awful complicated, awful fast. A simpler way to encode *hierarchical data*, or data where some facts naturally 'belong' to other facts (like birds belonging to a particular tree), is via JSON.

JSON is [fully specified here](http://www.json.org/), but for those who find those flow charts as inscrutable as I do, let's walk through an example. A simple JSON object might look like:

```
{
  "myNumber": 0
}
```

Wrap the whole thing in `{}`, give each variable a name between `""`, and assign a value after a `:`. `myNumber` in this example is called a *key*, and `0` is its *value* - that's why JSON belongs to a broader class of things called *key-values stores*, common in many languages (pythonistas in the audience will recognize this as identical to a pythonic dictionary). We can also store strings:

```
{
  "myNumber": 0,
  "myWord": "bananas"
}
```

Note the `,` that appeared after the former line; JSON also holds arrays (*lists* in some languages):

```
{
  "myNumber": 0,
  "myWord": "bananas",
  "someStuff": [22, "calico"]
}
```

Note that JSON arrays can contain mixed data types, like a list in R. But where this actually gets useful, is when we note that JSON objects can contain *other JSON objects* as values:

```
{
  "myNumber": 0,
  "myWord": "bananas",
  "someStuff": [22, "calico"],
  "cats": {
    "Pika": {
      "color": "tortoise",
      "weight": 5
    },
    "Pixel": {
      "color": "black",
      "weight": 3.4
    }
  }
}
```

We can keep nesting JSON in JSON recursively forever, for all the data we want to record as 'belonging to' another piece of data. Our birds example from the last section might look like:

```
{
  "trees": [
    {
      "type": "poplar",
      "height": 6,
      "residents": [
        {
          "species": "parrot",
          "color": "red"
        },
        {
          "species": "cockateel",
          "color": "blue"
        },
        {
          "species": "crow",
          "color": "black"
        }
      ]
    },
    {
      "type": "spruce",
      "height": 10,
      "residents": [
        {
          "species": "chickadee",
          "color": "brown"
        },
        {
          "species": "crow",
          "color": "black"
        },
        {
          "species": "crow",
          "color": "black"
        },
        {
          "species": "crow",
          "color": "black"
        }
      ]
    }
  ]
}
```

(I snuck in an example there of how JSON objects are valid members of arrays, too, as in the `"residents"` key). JSON has a number of advantages:

 - **The web lives on JSON.** If you want to make a moderate amount of data openly usable on the web, particularly for visualizations or interactive tools, JSON may well be your best choice. This is because it's very easy for web apps to consume JSON; the 'JS' in 'JSON' stands for 'JavaScript', which is where the standard emerged from. JSON powers all kinds of open data, like [the weather in London](http://api.openweathermap.org/data/2.5/weather?q=London,uk&appid=44db6a862fba0b067b1930da0d769e98), [who is studying cats using R on GitHub](https://api.github.com/search/repositories?q=cats+language:R&sort=stars&order=desc), or [where you can rent a bike in NYC](http://www.citibikenyc.com/stations/json/).
 - **Broad language support**: most every language has pre-made packages for both reading and writing JSON. See the `json` [package in Python](https://docs.python.org/2/library/json.html), `jsonlite` [in R](https://cran.r-project.org/web/packages/jsonlite/index.html), and many others.
 - **Easy to remix**: The nestable nature of JSON makes it really easy to combine datasets; if you have two blobs of JSON, you can put one inside the other and be guaranteed to still have valid JSON. Also note the absence of a rectangular format lets us have implicit `NULL`s like the long form tables discussed above, so we don't have to worry that adding a key to one thing will create a lot of blank spots in all similar things, like might happen when adding a new column to a table.
 - **Easy to add metadata**: I want to write a whole post about metadata, but one quick comment today: *where the heck are you supposed to put your metadata in a csv*, or any data frame-like object? (Best current answer: see the [CSVY](http://csvy.org/) spec, which seeks to add yaml frontmatter to CSV files - though this still doesn't solve the in-memory side of the problem - see [this issue over at Pandas](https://github.com/pydata/pandas/issues/2485) for what happens when you stare into this abyss too long). JSON blissfully eliminates this problem: feel free to tack on a `"metadata"` key, and assign it a JSON object with everything you need to understand the associated data. Actually, this strategy is exactly what sits behind [JSON-LD](http://json-ld.org/), an exciting strategy for metadata that I will dig deeper into in future.
 - **Easy to build on top of**: As long as you can avoid getting lost in bracket hell (which we all do from time to time), JSON is pretty simple and unopinionated, which makes it a good candidate for building standard data formats on top of. My favorite example of this is [GeoJSON](http://geojson.org/), a specification for encoding map data in JSON. GeoJSON has become so successful and widespread, that there is a huge ecology of tools out there that will consume it to do neat things like magic; GitHub, for example, will take [plain GeoJSON in a repo](https://raw.githubusercontent.com/mozillascience/studyGroupLessons/master/whereWeAre.geojson) and automatically render it into [a lovely map](https://github.com/mozillascience/studyGroupLessons/blob/master/whereWeAre.geojson).

If you're meeting JSON for the first time today (or even if you're not), take advantage of automatic JSON proofreading tools like [this one](http://jsonlint.com/), to help make sure you got all those brackets and commas in the right place.

#### HDF5

JSON is great for human scales of data. But like any plain text format, things are going to become glacially slow when we begin to leave the gigabyte scale and start to approach terabytes of data. For more advanced users, HDF5 might be the hierarchical data format du jour; unlike JSON, HDF5 stores its data in binary, non-human readable format, helping to keep storage manageable, and enforces a bit more strictness on how data is represented (tables, of which there can be many, are strictly homogeneous in type, and this multiple-table paradigm makes it conceptually similar to the birds example from the end of the CSV section above, or to SQL-like database usage, if that's familiar). HDF5 excels at subsetting and random-access of data, meaning if you want to quickly find and grab a small chunk of a large dataset, this is the format for you.


### 3. Structured Packings

**Patron saints:** [World Ocean Database ASCII format](http://data.nodc.noaa.gov/woa/WOD/DOC/wodreadme.pdf), [GRIFFIN event format](https://rawgit.com/wiki/GriffinCollaboration/GRSISort/technical-docs/GRIFFIN_Event_Format.pdf)   
**Strengths:** compact, flexible, appropriate for large data in unpredictable circumstances   
**Weaknesses:** difficult or impossible to read by eye, corruptable even in cases of minor data loss.

#### Character Stanzas

One of the nice things about JSON was how easily flexible it is - it can accommodate variable amounts and types of data from subject to subject in a way that a rigid CSV struggles with. Unfortunately, JSON can get really *big* - like long form tables, every time we want to talk about a variable, we have to list that variable's name as a key, which could blow up the size of our data real quick. HDF5 is much more compact, but is totally unreadable to humans. One middle road is to encode our data in a way that is conceptually similar to JSON, but in a much terser format that I call a *character stanza*.

Character stanzas trim the fat of JSON by preceding each piece of data with an indication of how many characters compose that data. So instead of saying

```
"species": "chickadee"
```

The same information might appear in the file like

```
9chickadee
```

telling me that to get the next piece of data, I need to read the next 9 characters, which come out to `chickadee`. But this alone isn't enough; character stanzas also need a specifying document to go with them that explain how big the variable-size-indicators are (how did I know to only read the `9` in the last example? What if it was followed by numerical data instead of a word?), and what order the variables come in. For the bird example above, I might specify my format as follows:

 - Variable size indicators are always one character long
 - Variables in order:
  - number of trees
  - sub-stanza for each tree, where an individual tree stanza looks like:
    - type
    - height
    - number of residents
    - sub-stanza for each resident, where an individual resident stanza looks like:
      - species
      - color

With that specification in hand, the final JSON example above becomes:

```
126poplar16136parrot3red9cockateel4blue4crow5black6spruce210149chickadee5brown4
crow5black4crow5black4crow5black
```

All the same information is present, and the structure has *almost* as much flexibility, but is much smaller. I say almost as much flexibility, since while it's easy to have variable numbers of trees and birds, it isn't such a simple matter to jam a new or extra variable in somewhere, since it relies so heavily on its rigid specifying description in order to decode it. Which exposes one key weakness of the character stanza - if even a single character is lost in the string above, the whole thing (or at least everything after the missing character) becomes unreadably corrupt. One way around this would be to put 'waypoints' in the encoding, that can allow you to pick up reading after a corrupted stretch. For example, instead of explicitly encoding the number of trees, we could start each new tree with a `>`:

```
>6poplar16136parrot3red9cockateel4blue4crow5black>6spruce210149chickadee5brown4
crow5black4crow5black4crow5black
```

Then if something goes awry, we can fast-forward to the next `>` and carry on reading.

If you're in a situation where you're unlikely to be dropping characters here and there, character stanzas can be a nice balance between keeping things small, flexible, and (just barely) human readable; the unpackers for these sorts of data formats tend to be simple compared to something like an HDF5 encoder, which makes this an approachable option for people with medium-sized data. The first place I encountered a character stanza approach was in the [World Ocean Database](https://www.nodc.noaa.gov/OC5/WOD/pr_wod.html), a collection of historical climatological ocean measurements. You can see their character stanza specification in chapter 2 of [their docs](http://data.nodc.noaa.gov/woa/WOD/DOC/wodreadme.pdf), and check out [the decoder](https://github.com/IQuOD/wodpy) the team I work with wrote for it.

#### Bit Packing

Before we dove into our gallery of data format archetypes, I mentioned that natural structure was a key consideration in choosing a format. CSVs make sense for situations with many subjects with the same information recorded about each of them, but fall apart when variable observations or inconsistent treatments are made or applied. JSON is more flexible, but not very memory efficient, which will start to cause pain for large data sets; character stanzas are a bit more compact, but impose a rigid ordering of data. The last exhibit in our gallery for this post is a less-rigid and less-fragile nearest-neighbor of the character stanza, the *bit packing*.

A bit packing scheme assigns meaning to individual bits in short sequences called 'words', and assembles a few words into an 'event'. Usually, words will come packed in regular, convenient-sized chunks, like a 32-bit integer; the exact size of a chunk is up to you, but since this data format really shines for high-data-rate experiments like [GRIFFIN](http://griffincollaboration.github.io/griffin-website/index.html), it's usually useful to pick a size high performance languages like C like to deal in (hence the 32-bit int), and can conveniently create arrays of for representing events. Like the character stanza, an encoding specification will describe what each bit in a word means; a good bit packing, however, ups the ante by making itself as *self describing* as possible, meaning that each word will typically carry some information to tell you what kind of word it is - like a little label. For example, I might have a toy specification like (where the `0x...` notation indicates *hexadecimal notation*, each character of which conveniently represents 4 bits):

 - Header words (typically used for metadata):
   - bits 0-3: 0xF
   - bits 4-7: number of particles in event
   - bits 8-31: time [ms since start of experiment]

 - Electrons:
   - bits 0-3: 0x0
   - bits 4-31: energy

 - Photons:
   - bits 0-3: 0x1
   - bits 4-31: polarization

Now that I've got a specification, I can encode an observation of say, 2 electrons and a photon like:

```
0xF348AD01 0x0ADF6319 0x102F4C71 0x0EEF7819
```

Nevermind most of the gibberish - we can see right away that there's a header word first (because it starts with `0xF`), followed by an electron (since it starts with `0x0`), followed by a photon (starts with `0x1`), and finally another electron (`0x0` again). By using a couple of bits to form a flag to indicate what sort of word each one is, we give ourselves lots of waypoints in the data stream to confirm how we should be unpacking the following bits - like the `>` in the character stanza above. In actuality, character stanzas and bit packings are conceptually the same thing - but bit packings turn the priorities of terseness and self-description up to 11.

One place a good bit packing can really deliver is in encoding data on the fly in an experimental situation where data is being generated by a random process, taking different forms or contexts at each observation. In a nuclear decay experiment, I have no idea what I'm going to see next - will it be an electron? A photon? One of the other things I can detect? I can't build order into my data stream like I would have liked for a character stanza with few waypoints, because my data doesn't *have* an order (at least not yet), I can't keep massive CSV or HDF5 tables in memory to append to, and at 200 TB of data produced by GRIFFIN every *week*, JSON is right out. A bit packing lets us make little real-time squirts of highly compact, flexible data that we can contextualize and decode later.

## Last Word

We've encountered some of the greatest hits of data encoding in this post: delimited, hierarchical and structured, and seen a few different realizations of each. If you're feeling a bit overwhelmed, here's a handy flowchart that I hope will get most people there, most of the time:

![data]({{ site.baseurl }}/img/data-formats.png)


Regardless of which of these formats (or others!) you settle on, listen to Shakespeare:

> This above all: to thine own format be true    
> And it must follow as the , the }    
> Thou canst not be false to any data.    

Strictly, uncompromisingly, *no exceptions ever*, respect the orthodoxy of your data format - this is at least as important as any choice you make when writing data.

*But Bill! You forgot about this totally important other data archetype!* Tell me about how you like to encode your data in [the comments]().
