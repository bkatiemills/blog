---
layout: post
title: "Full Stack Science"
subtitle: "a guide to open science infrastructure"
date: 2016-01-05
permalink: full-stack
---

### Introduction

After my recent [introduction to Docker + Rocker][rocker] class at UBC's [Mozilla Study Group][ubcsg], one of the students asked a question I think a lot of people, from newcomers to open science veterans, are struggling with:

*How do we put all this infrastructure together in our real research?*

In time, I think we'll look back on the last few years as an era of huge infrastructural strides for reproducible research on the web - but now that we've had a few years to come up with all these tools and services, it's time to piece it all together into a clear, simple toolkit, with guidelines on standards and usage. In this post I present my standard stack - the tools I try to use to ensure my work as a scientific software developer is as reproducible, reusable and participatory as possible. To do so, I'll walk you through a toy example employing all the tools in an imaginary study of the ice cream flavor preferences of common housecats; think of this as a minimum working example of my reproducible scientific stack.

When reading this tutorial, keep in mind: these are standards to *shoot for*. I rarely (if ever) actually pull off the full stack depicted here, since life and grant cycles invariably get in the way. Think of this as a standard to work towards, and remember - if you include even a subset of these ideas in your work, you're doing great.


### The Lineup

My A-team consists of:

 - **GitHub**, for sharing code; see the [cat-icecream repo][cat-icecream-git].
 - **Docker Hub**, for sharing dependencies; see the [cat-icecream image][cat-icecream-docker].
 - **FigShare**, for sharing data; see the [cat-icecream data][cat-icecream-data].
 - **Travis CI**, for testing my code
 - **Zenodo**, for minting DOIs for my code

The first three address distribution, and GitHub goes a step further by providing first-rate collaboration tools; more on that below and elsewhere. The last two address two of the key meta-tasks of collaborative and open science: how do we quantitatively argue for the quality of our code (testing, continuous integration), and how do we make our work discoverable (DOIs, citations). In what follows, I'll step through them in the order I build them up in a real project.

### Part 1: GitHub

**Background lesson:** If you've never used git or GitHub before, check out [Software Carpentry's intro][swc-git]. If you can make commits and push and pull from a repository on GitHub, you're good to go.

If I was trapped on a desert island, with a gigabit link to only one service from the outside world, GitHub is my pick. GitHub is the backbone of my practice not just because I write lots and lots (and lots) of code, but because it is my favorite *social network*; its collaboration tools and techniques have matured to the point where it is a powerful platform not just for sharing code, but for working on it together.

Let's start by checking out the [cat-icecream repo][cat-icecream-git]. Project organization is its own huge topic, and one I may address in a future set of lessons, but I'm going to leave it off for now; `cat-icecream` is an example of how I organize my python projects, but I'll leave you to peruse on your own. What I want to focus on today, are the key elements that support our work's *reproducibility and reusability*.

#### README

A million years from now and on another world, when someone wants to recreate `cat-icecream`, the first thing they'll see is the `README.md` in the top level of its GitHub repo. Think of this as the home page of your research; it needs to communicate what this work is, and provide the definitive guide for reproducing it. `cat-icecream`'s readme provides a template for the key elements of a good readme. I'll walk through its sections below, and provide some reading time estimates for each section - stick to these constraints! No one is going to read your readme if it's hundreds of pages long.

##### i. Badge Status

**Estimated reading time: negligible**

The very top of your readme is the place to include all those slick badges that Travis and Zenodo provide; more explanation on what they are will be provided in their respective sections.

##### ii. Introduction

**Estimated reading time: 2 min**

This is essentially an abstract for your project. Like an abstract, it *absolutely does not* contain all the details about everything; it is just enough to let people know what this project is about.

##### iii. Other Resources

**Estimated reading time: negligible**

A list of links to other key resources, at a minimum the data and docker repository of the project dependencies. Include DOIs where available (make DOIs available wherever possible).

##### iv. Running Instructions

**Estimated reading time: as short as possible**

The Running Instructions are perhaps the most important part of your readme - this is where you walk people through the whole process of installing your software and dependencies, getting your data, and reproducing your results. As in `cat-icecream`, I like to indicate what software people will need installed to follow the instructions in this section (often just `git` and `docker`, if all your analysis is encapsulated in your Docker image). These dependencies are followed by explicit, step by step instructions that lead the user from downloading all the things they'll need, to setting up their environment, to actually running the analysis. `cat-icecream` is pretty simple, so a few bullet points is all it took; for more sophisticated analyses, you'll want to produce a [shell script][swc-shell] or even a [Makefile][makefiles] for helping manage this process.

As advertised at the start of this section, the real strength of GitHub is in its ability to support collaboration on code. Much has been written about techniques for creating great collaborations on GitHub; I recommend the [Working Open Guide][wog] from my former colleagues at Mozilla as a gem of these distilled strategies. But, before worrying too much about how to manufacture collaboration, *focus on creating users first* - if people can actually run your software, they will start having their own ideas about it, and feel inspired to get involved and collaborate. After all, [most long term contributors to open source projects are users first][zm] - making sure people actually get to the 'user' stage is the point of the 'Running Instructions' section in your readme.

> *Focus on creating users first - they will... feel inspired to get involved and collaborate.*

##### v. Programmatic Logic

**Estimated reading time: 5-10 min**

A while back, I wrote a [blog post][tenminplan] here on how to communicate the broadstrokes of your engineering to a new collaborator. I'll refer you to that post for the details, and reiterate here that the point is *to give a new contributor the lay of the land* - this section in no way communicates every detail about the project, but rather lets the reader know the rough shape of things, so that they start to form a mental model of the project. The 10 minute limit here is key - that's all anyone can absorb in one sitting, and while it may be very tempting to go into great detail about all your awesome work, *don't*. Contributors will have to learn the details with time and experience; this is just a rough sketch to get people started. Flow charts like the one in `cat-icecream` are highly recommended; I made that one with the free service provided by [draw.io][draw].

![flow](https://raw.githubusercontent.com/BillMills/cat-icecream/master/img/cat-icecream-logic.png)

##### vi. Contribution Guidelines

**Estimated reading time: 5 min**

The last element I like to include in my readme is the contribution guidelines. Many projects will push this out to its own `CONTRIBUTING.md` file - this is acceptable, but I prefer to put it right in the readme so that GitHub puts it front-and-center on the repo's landing page - that way, people who aren't familiar with the convention of having a `CONTRIBUTING.md` get it put in front of them without explicitly seeking it out.

Contribution guidelines have two functions:

 - make people feel like their contributions are welcome
 - provide a set of steps that guide contributors to making good contributions

The first point is served by the second; if people understand what the 'right way' to contribute is, they can feel a bit more confident that they're doing the right thing; couple this with lots of willingness to help people with their problems, and you lay the groundwork for a healthy and respectful collaboration.

`cat-icecream`'s contribution instructions are verbatim what I use in my real projects; they boil down to:

 1. Open an issue to tell me what you want to do
 2. Make a *small* pull request
 3. Test everything.

Opening an issue first helps make sure that if someone wants to put effort into this project, they put that effort into a change that actually has a hope of being accepted; the limit on pull request size is a product of strategies for effective code review, [researched here][smartbear] and discussed in great detail in my [teaching kit on scientific code review][codereview].


#### LICENSE

One oft-overlooked and sometimes fraught part of publishing code open source is getting a license on it. I'm not a lawyer, don't listen to me; go on down to your institution's IP office or legal team, ask them what they think of using code or data that doesn't have a clear license associated with it, and watch the look of horror in their eyes. An appropriate open source license is *mandatory* if you want people to actually be able to use your work without legal pouncing all over them.

Luckily, this doesn't have to be hard. The Internet overflows with arguments about which license to use where - feel free to enter the fray, but I choose to make my life easy: [MIT][MIT] for code, [CC-0][cc0] or [CC-BY][ccby] for everything else, *and that's it*. Cut and paste the text of the license you choose into the `LICENSE` file in your repo, change the copyright information appropriately, and you're done. 

#### GitHub Last Word

As repeated a whole bunch of times above, GitHub is a platform for collaboration above all else. The single most common mistake I see scientists making with GitHub, is when they say *'I'm totally going to put my code on GitHub... eventually, after I clean it up'*. Do not fall into this trap; push your code now. There is no such thing as 'clean' code - all code, *all code*, is the authors' best stab at pulling off what they were trying for. It *all* has bugs, it *all* has quirks, and *none* of that makes it a lesser work. It's normal to feel nervous putting everything out there when you aren't used to it yet - but as a scientific software developer, I have *never* looked at a project on GitHub and thought less of its authors; the entire world is a work in progress, and anyone who has ever written a single line of code knows that. By pushing code early and often, we give those collaborators a chance to get in on a project on the ground floor, before its so giant and mature that participating seems overwhelming.

> *The single most common mistake I see scientists making with GitHub, is when they say 'I'm totally going to put my code on GitHub... eventually'... Do not fall into this trap; push your code now.*

### Part 2: Docker

**Background lesson:** Docker provides some great [hello world][dockerhw] examples of working with Docker as part of their getting started material; also check out my [Intro to Rocker][rocker], which introduces some of the use patterns I'll mention below (and focuses on using Docker with R, which I know many of my readers really dig).

Anyone who has ever been in the same room as me for more than ten seconds knows I talk about the importance of open source software to science *a lot* - but there's just one problem: *code does not run in a vacuum*. It isn't enough for us to take our code and slap it up on GitHub; if we want our work to remain reproducible into the future, when the machines we did our analyses on are long gone, we need to be able to reproduce the running environments we did that work in, which generally means the operating systems and software dependencies we had at the time. Anyone who has fought with restoring a complicated dependency stack has a personal, profound understanding of the term *dependency hell* - these tasks are black holes of productivity, they're boring, they're unreliable, and we need a better solution. Docker is that solution.

Put really simply, Docker is to dependencies what Git is to code. Docker lets us run our code inside a container that encapsulates an operating system and all our software dependencies, and then share that container on the web via their service, [Docker Hub][dockerhub]. We can even have a whole bunch of those containers, so we can swap between different sets of dependencies for different projects, as easily as you would switch between one directory and another.

I'll leave an introduction to actual Docker usage to the background lessons linked at the top of this section. The [cat-icecream example on Dockerhub][cat-icecream-docker] doesn't have much to it, but there are some non-obvious best practices from its creation to note:

 - **Creating a Dockerfile** is discussed in my intro to Rocker and on a million other Docker tutorials on the web. Throw this into the top level of your GitHub repository for safe keeping, so that the container can be regenerated and updated in future. Check out [cat-icecream's Dockerfile][cat-icecream-dockerfile] here.
 - **Use Docker Hub**. This is probably the easiest part of using Docker; once you've made your container, [make an account on Docker Hub][dockerhub], and then do a `docker push myname/containername`, and you're good to go. Add some comments to the resulting repository page (particularly linking back to the GitHub repo), but feel free to leave the detailed instructions and discussions for the `README` discussed above, on GitHub.
 - **Separate code, data and dependencies**. Docker is for encapsulating your operating system and dependencies, *not* your code and data. All kinds of funny business can happen with Docker caching if you try to add static files to your image; of course it can be done, but I recommend mounting an external directory containing you code and data instead. There's an example of this in `cat-icecream`'s running instructions in that repo's `README`; from within the root directory of your git repo, do:

```
docker run -v $PWD:/cat-icecream -i -t billmills/cat-icecream /bin/bash  
```

The magic is in the `-v $PWD:/cat-icecream` part. This connects the `$PWD` directory on your real computer (so, the root of the git repo you were in when you ran this command), to the directory `/cat-icecream` on your virtualized machine within Docker. That way, you can start by grabbing the code and data at the version you want, and rely on Docker *only* for the dependencies you want to run that code under. This is convenient since code tends to change much more often than dependency stacks, and because it gives the added bonus that any files saved in `/cat-icecream` within the Docker container will appear in the mounted directory on your real machine, avoiding any headaches of the 'how do I get my work out of Docker's bubble universe' variety.

Docker commands can look a bit ghastly for people who aren't shell enthusiasts - be sure to include them explicitly in the running instructions in your GitHub repo's readme, and you'll give third parties a fighting chance of running your code without sending them straight to dependency hell.


### Part 3: FigShare

After GitHub and Docker, connecting data to your work with [FigShare][fs] is a breeze. Simply upload your dataset, apply some reasonable tags and categories, and link from your GitHub's `README`. Notice that FigShare automatically applies a DOI to your dataset; make certain to quote that DOI in your `README`, so there can be no confusion which object you mean. Again, check out the [cat-icecream example][cat-icecream-data].

Of course, much less of a breeze is organizing your data in a useful structure and a salient format; I hope to cover both these topics in a future post. For now, getting it up there in any state is better than leaving it entombed in the sarcophagi of our laptops.


### Part 4: Testing & Travis CI

**Background lesson:** Travis provides a lesson for newcomers [here][travishw]. For those new to unit testing, check out these introductions in [python][pyunit], [R][runit], and [JavaScript][qunit].

In the section above on Docker, I mentioned that there was just one problem with open source code, which was true - sort of. There's also a meta-problem: *do you trust me?*

If we are to use open source to build up an ecology of software tools we can use off the shelf and to support reproducible analyses, we need to be able to make some sort of verifiable and falsifiable argument that our code actually does what it says it does, so we don't have to take whatever we find on the Internet on faith. Unit tests are practical falsifiability for code.

The background links above have detailed introductions to the mechanics of unit testing; once those tests are written, the easiest way to include them into our open science stack is via a continuous integration service like Travis. Continuous integration is the practice of running those unit tests automatically every time the code is changed, and raising warnings when something stops working.

Luckily, setting up continuous integration with Travis is pretty easy, now that we already created a Docker container to run our code in; Travis can grab that container from Docker Hub, our code from GitHub, and run our tests automatically every time we push to our GitHub repo. To get started, follow the three steps Travis prescribes, starting from your Travis homepage, `https://travis-ci.org/profile/your-user-name`:

![travis](img/travis.png)

For creating your `.travis.yml` in that middle step, look at the [cat-icecream .travis.yml][travisyml] example (notice the `$PWD` mounting trick again for Docker in the last line - this is crucial here, since Travis automatically clones new code from GitHub when you push it, and fetches the Docker container separately; by mounting one inside the other, your new code is available in your Docker container). You can copy this almost exactly for any similarly arranged project. You'll have to change `billmills/cat-icecream` to the name of your Docker container, and the name of the `/cat-icecream` directory in the last line to something more appropriate for your project, and *that's it* - Travis will take care of the rest, and it will provide you with this nifty badge that indicates if your code is working or not:

[![Build Status](https://travis-ci.org/BillMills/cat-icecream.svg?branch=master)](https://travis-ci.org/BillMills/cat-icecream)

Put that in your GitHub `README` at the very top, so people know you've tested your code, and it works as expected. Another nice bonus you get for free is that Travis and GitHub will conspire to indicate whether a new pull request breaks your tests or not - that way, you never merge in broken code, and collaborators get a gentle automatic reminder to run (and pass) all tests before expecting their contribution to be accepted.

### Part 5: Zenodo

Finally, when your analysis is complete and your paper is ready for submission, it's time to ship your code in a way that you can cite in your paper and that allows future readers to get ahold of exactly that version of your software, despite how it may evolve after the fact. GitHub on its own can satisfy these needs in practical terms; see their [guide to making a release][github-release] of your code, and optionally, have a look at this [guide to semantic versioning][sv] for tips on how to pick meaningful version numbers.

The software developer in me would like to call that a wrap - but unfortunately, we need a little bit more to make code citations fit nicely into the towering bibliographic infrastructure the sciences have built up around themselves. Enter the DOI - a digital object identifier that acts like a unique serial number, pointing to a permanently archived copy of the release you just made and ensuring that it will always be available and indexable in the many systems that know how to deal with standardized DOIs.

A few years ago, GitHub partnered with Zenodo to offer exactly this service; see [GitHub's instructions on how to stamp a release with a DOI][code-citation]. Once you've stamped your release with a DOI, be sure to add the badge (or at least the DOI number) to a table at the top of your `README` in yout GitHub repo along with the corresponding release version number, like this:

![doi-table](img/doi-table.png)

Since your code may evolve after your paper is published or your project is complete, readers of the paper will need to know how to get back to the release that paper corresponds to; citing the correct DOI in that paper is sufficient, but it never hurts to reiterate the correspondence between DOIs and releases all in one place.


### Conclusion

The first step in open science is simple availability. If we can make our code, data and dependencies available to our colleagues, we'll have taken a big leap forward. But, if we really want to achieve the full potential of this movement, there are some other \*abilities beyond just availability we need to approach, and I think the stack I've walked you through here lays the groundwork for them. By writing a thorough `README` on GitHub and by distributing our dependencies on Docker, we support the *usability* of our work; by writing unit tests and enforcing them with continuous integration services like Travis, we reinforce the *reliability* of that work; and by stamping data and code with DOIs like FigShare and Zenodo provide, we negotiate the *discoverability* of that work. In these ways, I think this stack has come together as an engine for the open, reproducible and reusable science we aspire to.

*But Bill! You missed my favorite tool! I also need xyz to make my work reproducible! Let me know your gold-star open science stack in [the comments][comments].*












[rocker]: https://github.com/BillMills/Rocker-tutorial
[ubcsg]: http://minisciencegirl.github.io/studyGroup/
[swc-git]: http://swcarpentry.github.io/git-novice/
[cat-icecream-git]: https://github.com/BillMills/cat-icecream
[cat-icecream-docker]: https://hub.docker.com/r/billmills/cat-icecream/
[cat-icecream-data]: https://figshare.com/articles/cat_icecream_dataset/2059248/1
[swc-shell]: http://swcarpentry.github.io/shell-novice/
[makefiles]: https://stat545-ubc.github.io/automation00_index.html
[wog]: http://mozillascience.github.io/leadership-training/index.html
[zm]: http://www.mockus.org/papers/willingness.pdf
[tenminplan]: http://billmills.github.io/blog/ten-minute-plans/
[draw]: https://www.draw.io/
[smartbear]: https://smartbear.com/SmartBear/media/pdfs/11_Best_Practices_for_Peer_Code_Review.pdf
[codereview]: http://mozillascience.github.io/codeReview/intro.html
[MIT]: https://opensource.org/licenses/MIT
[cc0]: https://wiki.creativecommons.org/wiki/CC0
[ccby]: http://creativecommons.org/licenses/by/4.0/legalcode
[dockerhw]: https://docs.docker.com/mac/
[dockerhub]: https://hub.docker.com/
[cat-icecream-dockerfile]: https://github.com/BillMills/cat-icecream/blob/master/Dockerfile
[fs]: https://figshare.com/
[travishw]: https://docs.travis-ci.com/user/for-beginners
[pyunit]: http://ivory.idyll.org/articles/nose-intro.html
[runit]: https://github.com/kazi11/TestPackage#tests-directory
[qunit]: https://qunitjs.com/
[travisyml]: https://github.com/BillMills/cat-icecream/blob/master/.travis.yml
[github-release]: https://help.github.com/articles/creating-releases/
[sv]: http://semver.org/
[code-citation]: https://guides.github.com/activities/citable-code/
[comments]: https://github.com/BillMills/blog/issues/9