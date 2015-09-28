---
layout: post
title: "Engineering Collaboration"
subtitle: "How the way we code affects participation"
date: 2015-09-27
permalink: engineeringCollaboration
---

### Introduction 

A few weeks ago, I had a meeting with my old / new boss at TRIUMF, to discuss some of the projects they wanted me to tackle when I returned as a scientific software developer. They explained to me that over the past year, people had struggled to maintain the web apps I had made them, since the lab didn't have time to dig into the details of web development. It was clear that in order for my work to really empower my colleagues and consist of code that could be reused, I had to find a way to write that not only got the job done, but that was easy to maintain for people with other priorities.

I have [written][ssd] and [spoken][scienceXpython] and [spoken some more][introOS] in recent months on the crucial need for a focus on *usability* in open science - the need to ensure that all the data and code we're putting out there isn't simply warehoused on the web, but is legible enough to be brought to life by our colleagues who reuse it. This problem is conceptually simple for data; standardization allows us to make interoperable datasets and tools in a [fairly straightforward way][openData]. But how do we make something as nuanced, as variable and as personal as code universally legible?

### A Digital Lingua Franca

I was really excited to come back to TRIUMF, and get back to web development. In my spare time, I had been tinkering with little projects, keeping skills sharp and reflecting on my previous practice, and I had a lot of ideas I wanted to try out. During that conversation with my old / new boss, I brought up a number of ideas for the projects they proposed; I think there's a lot of mileage to be had in using URLs for stateful applications well, and in an effective use of HTML templating to resolve some of the PHP spaghetti problems they were having (more on these crunchy bits in the next section). I made a few quick little demos of different engineering approaches to sketch out how to deliver these features and tackle the projects at hand - but to my growing concern, nothing was quite right.

The first thing I tried was Python's [Flask framework][flask]. Flask is a brilliant piece of engineering: it can hit the highlights of basic web app infrastructure, like the templating and URL encoding that I wanted, in only a few lines of fairly straightforward Python. What's more, Python is much more common in the scientific community than JavaScript; a Flask app should have been relatively easy to maintain after I moved on.

But, the plot thickened. After proposing this, my boss explained that they wanted this app to be 100% client-side - no server side logic allowed. I was a bit baffled by this at first - if JavaScript is tough for the lab to maintain, why would we push absolutely everything into JavaScript? They explained: servers crash, servers need maintenance, and servers present one more moving part to go wrong. If all we asked of our server is to chuck static JavaScript at clients, that's one less thing to slow down and potentially derail the project in future. I'm a big fan of Flask, but it didn't fit the demands of my employer. Strike one.

Client-side being the new name of the game, I took the opportunity to dive into [Ember.js][ember], a popular JavaScript framework that would allow us to build the completely in-browser experience TRIUMF wanted. And I'm glad I did - I had wanted to check out Ember for some time, and it was glorious: like sitting in the captain's chair of the USS Enterprise, the most advanced, powerful and sophisticated piece of machinery in the galaxy ready to warp me away on adventures in web app engineering.

That was a fun two days. But after installing Ember's whopping massive toolchain, trying to wrap my head around client side unit testing (more on that in a forthcoming post), piecing together their model/view/controller architecture (augmented with super cool model adapters), and marveling at the slug of auto-generated client side code it all boiled down to like a brick of depleted uranium dropped from orbit, I had most definitely found strike two. I look forward to building with Ember in a future project - it's an awesome framework. But for clients who were too busy to maintain a basic node/express backend like I had built them the first time around, the great glittering battleship of Ember was more tooling than needed - and it would only weigh the project down.

I reflected on this for days. How could I deliver the best practices in web development that I wanted, without adding so much complication to the project that it would become a big black hole of maintenance time for new web developers? What does it mean to write not just for reuse, but for reuse by people just starting out in a particular language?

> *Beginner-friendly code ... can be read at face value, with an understanding of syntax and not much else; the less domain knowledge required to understand your code, the more collaborators you will find.*

I have lectured a number of times for Software Carpentry's instructor training course, where one of the topics we spend a whole bunch of time on is the difference between novices, competent practitioners, and masters of a topic. One of the key early elements of this progression, is the development of a working *mental model* of the material; that functional little simulation in your brain that allows you to predict what will happen in a given situation, gives you some context to plug new information into, and provides connections between ideas that allow you to draw inferences and follow logic - in other words, to solve problems in that space. Problem solving without a mental model is pretty much impossible - and it occurred to me that that is a huge barrier and risk for collaborative scientific coding.

Programming is rife with domain knowledge - not only basic syntactic knowledge, but knowledge of design patterns, best practices and paradigms. Without the functioning mental model of that domain knowledge that comes with really digging into programming as a profession, code that uses those paradigms will be completely inscrutable. If the acronym 'M-V-C' doesn't mean anything to you, good luck maintaining that Ember app - that beautifully functioning machine becomes so much dead weight. In order to write code that encourages collaboration across a wide range of knowledge and skill levels, we need to organize our code in a way that doesn't require extensive domain knowledge to understand it, and that exposes that sophistication through as simple a set of controls as possible - like how the complicated inner workings of a car are abstracted away behind a wheel and a couple of levers. Beginner-friendly code puts as much on the screen as possible, so that the code can be read at face value, with an understanding of syntax and not much else; the less domain knowledge required to understand your code, the more collaborators you will find.

With that in mind, the way forward on my first project was clear: obviously what the world needs, is another JavaScript framework!

### Simple on its Face (introducing Ultralight)

Before people riot at [yet another JavaScript framework][nomorefwplz], I promise - this one is so simple it barely rises above the level of syntactic sugar. [Ultralight][ultralight] does exactly two things, one of which it subcontracts to [mustache.js][mustache]: HTML templating, and query string parsing. Recall from my TRIUMF project, these were the two things I wanted to add to the project, and I had to do them client side. But the goal of Ultralight is to do these things in a way that requires the user to only write very simple HTML and JavaScript, where 'very simple' includes a knowledge of basic syntax, but excludes any knowledge of sophisticated tooling or design patterns. Let's take a tour.

Here's a completely functional Ultralight page:

```
<!DOCTYPE html>
<html lang="en">
    <head>
        <script src='https://cdn.rawgit.com/janl/mustache.js/master/mustache.js' type="text/javascript"></script>
        <script src='ultralight.js' type="text/javascript"></script>

        <script id="body" type="text/template">
            <p> I have 17 cats. </p>
        </script>

        <script type='text/javascript'>
            ul = new ultralight([])
            ul.fetchTemplates();
        </script>

    </head>
</html>
``` 

Serve it up, and you will indeed have 17 cats. The core of an Ultralight page is that `<script id="body">` tag - that's where all the HTML for your page goes, the first key being, *it's plain HTML* - Ultralight will whip that into an actual page for you thanks entirely to mustache.js, no special knowledge required.

OK, it works - but pretty boring. Change that `<p>` tag to the following:

```
{% raw %}
<p> I have {{cats}} cats. </p>
{% endraw %}
``` 

Now, visit your page at `whateverDomain.org?cats=17`, and you will once again have 17 cats. Ultralight automagically shepherds the query string parameters into mustache's templates, allowing the user to build pages that encode their state right in the URL. URL encoding is something I'd like to see a lot more of in scientific web apps, since they are usually such highly stateful beasts, depending on a zillion different parameters and inputs to a complex calculation. By encoding this stuff in the query string, we can bookmark, link and use the back button - in other words, we can build science that's good for the open web. And we did it all without asking our users to follow anything more complicated than a variable, thanks largely to mustache and a bit of orchestration.

Ultralight also tries to make organizational best practices easy, by wrapping mustache.js' partial template importing. In a new file `partials/header.mustache`, put the following:

```
<h1>Collaboration is Awesome!</h1>
<h3>especially with cats</h3>
```

Then in your main HTML file, that HTML fragment will be available for mustache to use per its usual syntax:

```
{% raw %}
...
        <script id="body" type="text/template">
            {{> header}}

            <p> I have {{cats}} cats. </p>
        </script>
...
{% endraw %}
```

You'll also need to let Ultralight know you want that template:

```
...
        <script type='text/javascript'>
            ul = new ultralight(['header'])
            ul.fetchTemplates();
        </script>
...
```

*et voila:* collaboration with cats is indeed awesome. Now, rather than writing giant piles of cut-and-pasted spaghetti HTML, the user can write modular, reusable HTML in nicely organized files *by writing nothing but HTML* - the somewhat hairy process of async loading a bunch of external files into unexecuted `<script>` tags is dealt with by Ultralight. Huge thanks to the mustache team; that project does most of the heavy lifting here, and Ultralight itself is mostly just a bit of wrapping and sugar on top.

### Conclusion

There are a couple of more features surrounding callbacks in Ultralight, but I'll leave those for the docs. In total, Ultralight allowed me to successfully build the web apps I wanted (check out this [charge state booster page][CSB] to see Ultralight in production, or [another page about cats][pixel] for a more complete MWE of more of Ultralight's features), without dumping a ton of domain knowledge on my users. To use every feature of Ultralight, you need to know:

 - HTML
 - mustache templates
 - what a query string is
 - how to write a JavaScript function
 - what objects with key / value pairs look like in JavaScript.

which, as promised, is all syntax, any element of which can be learned without requiring a lot of experience or domain knowledge. *That* is what I meant when I described code that can be read at face value, and remains legible to as broad an audience as possible.

[ssd]: http://billmills.github.io/blog/ssd/
[scienceXpython]: http://billmills.github.io/scienceXpython/
[introOS]: https://www.youtube.com/watch?v=HDlRl86ERgM
[openData]: http://billmills.github.io/blog/wod-alpha/
[flask]: http://flask.pocoo.org/
[ember]: http://emberjs.com/
[nomorefwplz]: http://bitworking.org/news/2014/05/zero_framework_manifesto
[mustache]: https://github.com/janl/mustache.js/
[CSB]: http://griffincollaboration.github.io/beamCompanionExplorer/CSB.html?species=Ca&A=50
[pixel]: http://billmills.github.io/ultralight/index.html?cats=1
[ultralight]: https://github.com/BillMills/ultralight