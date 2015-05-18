---
layout: post
title:  "Effective Code Review for Journals"
date:   2015-04-20
permalink: code-review-for-journals
---

*This post originally appeared on the Mozilla Science Lab's blog.*

<em>Nature Biotechnology</em> <a href="http://www.nature.com/news/rule-rewrite-aims-to-clean-up-scientific-software-1.17323">recently announced</a> that it would be requiring authors to 'check the accessibility of code used in computational studies', in an effort to mitigate retractions and errors resulting from bugs &amp; under-validated code. The article quoted the Science Lab's director, <a href="https://twitter.com/kaythaney">Kaitlin Thaney</a>, in observing the Science Lab's position that openness in research is not only a matter of releasing information, but making sure it is effectively reusable, too, in order to reproduce and confirm results and carry that work forward.

But, technical challenges remain. As was discovered in the series of code review pilot studies from the Science Lab and<span class="author-p-21003"> Marian Petre from Open University</span> in 2013 and 2014, third parties reviewing code they weren't involved in writing leads to superficial reviews without much value; see reflections on these studies from <a href="http://www.mozillascience.org/code-review-for-science-what-we-learned/">Thaney</a> as well as <a href="http://www.mozillascience.org/stating-the-obvious/">Greg Wilson</a>, in addition to recent comments to the same effect from Wilson <a href="http://software-carpentry.org/blog/2015/04/quality-is-free-getting-there-isnt.html">here</a>.

However, journals like <em>Nature Biotech</em> can still compel some very valuable change by marshaling a system of code review for their submissions. As we discuss in <a href="http://mozillascience.github.io/codeReview/intro.html">our teaching kit on code review</a> (and as was originally investigated in <a href="http://smartbear.com/smartbear/media/pdfs/wp-cc-11-best-practices-of-peer-code-review.pdf">this study</a>), much value can be derived from setting expectations for code clarity and integrity. By demanding authors submit a high-coverage test suite for any original code used,  journals can encourage researchers to use this fundamental technique for ensuring code quality; also, as discussed in depth in the study linked above, the act of requiring authors to describe and justify the changes made at each pull request results in measurably less bugs committed - <em>before code review has even begun</em>. Specifically, journals could require:

<ul>
    <li>a passing test suite with a minimum standard of coverage (&gt;90%)</li>
    <li>a commit log consisting of small pull requests (&lt;500 lines each), each with an accompanying description &amp; justification of the changes made and strategies taken.</li>
</ul>

Neither of these require reviewers to read code in-depth, but both push authors to <em>seriously</em> <em>reflect on their code</em>, and thus improve its quality.

For more strategies on how to implement a system of code review for scientific software, check out <a href="http://mozillascience.github.io/codeReview/intro.html">our curriculum on code review</a>. The ideas and strategies presented there are crafted with busy scientists in mind, and explore how to get the most out of short, low-time-commitment reviews; feedback and contributions always welcome over at the <a href="https://github.com/mozillascience/codeReview/tree/gh-pages">project repo</a>.