---
title: Things I'm proud of in 2025
description: A review of various projects on Blink which I was proud of in 2025.
hero: hero.jpg
tags:
  - review
---

This started off as a general "2025 in-review" but ended up too boring and too
long, so here are just a handful of things I'm proud of last year. These aren't
necessarily the most important work I did, or the most "impactful", but things
that gave me joy, and hopefully will pay dividends in the future.

<img src="hero.jpg" width="2509" height="1671">

### flex-wrap: balance

I spent a month or so implementing `flex-wrap: balance`. A lot of this time was
just spending time trying to understand different academic papers which
implement different solutions to the
[Knuth-Plass line-breaking problem](https://en.wikipedia.org/wiki/Knuth%E2%80%93Plass_line-breaking_algorithm).

In the end, some of the solutions were too difficult to understand (research
papers often gloss over important details, have poorly named variables, [and
also have bugs](https://github.com/golang/go/blob/f8ee0f84753b22254d217bf28ce8ecca7db7025c/src/go/doc/comment/text.go#L307-L309)),
so I ended up [implementing something](https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/renderer/core/layout/flex/flex_line_breaker.cc;l=21-55;drc=ed470f480a83a5a9e1b10eb186b225ab7bc376c1)
different/new instead. I primarily did this so that it will be maintainable, at
some point someone might find an issue with the algorithm I used and will need
to fix it.

I settled on something that was `O(N)` in the common case, and I think converges
to `O(Nlog(N))` in the worst case (the naive solution is `O(N^2)` [which is truly
the worst complexity](https://bsky.app/profile/randomascii.bsky.social/post/3lk4c6st7nc2e)).

Many people have wanted this feature since ~forever - hopefully we can give
this to developers soon.

I use `flex-wrap: balance` in my other [blog
post](https://bfgeek.com/flexbox-image-gallery/) this year to build an image
gallery with flex.

<img src="balance.jpg" width="1706" height="1350">

### Flexbox Precision Issues

Rounding errors typically don't matter that much for most applications. They do
in layout engines, however, as it often results in a "gap" between the content of
an element, and the element's border. This (rightfully) infuriates people.

Web layout engines use [fixed-point
arithmetic](https://en.wikipedia.org/wiki/Fixed-point_arithmetic), (instead of
floating-point) so when you divide a layout number by an integer you end up
with a remainder.

Fixed-point is used as floating-point gets more inaccurate the further away from
zero you get leading to gaps and other layout issues (someday I'll write a
"floating-point is the
[worst](https://www.simonandschuster.com/series/The-Worst!-Series)" blog post).


Previously we discarded this remainder, and as such resulted in gaps when you
used `justify-content: space-between` on a flexbox as one example.

I implemented a ["diffuser"](https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/renderer/core/layout/geometry/layout_unit_diffuser.h;drc=f7eff366e22a141571facc94d9352064689b5143)
which can diffuse/distribute the remainder over N buckets in a "nice" way.
Under the hood this uses the core component of [Bresenham's line drawing
algorithm](https://en.wikipedia.org/wiki/Bresenham%27s_line_algorithm) which is
a fun 2D graphics cross-over.

The other precision issue I worked on was rounding issues within the
[floating-point calculation](https://source.chromium.org/chromium/chromium/src/+/main:third_party/blink/renderer/core/layout/flex/line_flexer.cc;l=115-137;drc=137e3e7bc23789602d3a2e3db2ffa3766ccec71f)
of how flex-grow/flex-shrink are used. The solution I ended up with is actually
used within Gecko's [flex implementation](https://searchfox.org/firefox-main/rev/21d2b7dcadb4f3d8789b84364a70ce103896047c/layout/generic/nsFlexContainerFrame.cpp#3247-3249);
in hindsight, I should have read that first.

It works by calculating the fraction of the remaining available space (instead
of the total available space), which converges without underflow or overflow.

Fixing both these precision issues resulted in a non-trival number of bugs fixed.

### The SVG Viewport Dependence Optimization

This was my first substantial optimization change for the year, as well as a
nice win code complexity wise. In the end it was only worth about 0.1% on
Speedometer3 but [removed a bunch of complex
logic](https://chromium-review.googlesource.com/c/chromium/src/+/6173723/8/third_party/blink/renderer/core/svg/svg_element.cc)
maintaining HashMap on a hot codepath.

[This optimization](https://chromium-review.googlesource.com/c/chromium/src/+/6168835)
essentially removed a HashMap containing elements which had geometry which
depended on the SVG viewport (`<rect width="50%">`) as one example. It replaced
it with a couple of bits computed during layout instead.

Many performance/code-complexity improvements I've made within Blink have been
just removing HashMaps. It’s an interesting phenomenon, lots of developers will
reach for HashMaps as they are "fast" O(1) insertion/removal is hard to beat,
but often the slowness is caused by a piece of code looping over every element
within the map (making it just a fancy list at that point), or a HashMap placed
on a hotpath where the insert and contain operations eat CPU cycles.

The logic for maintaining these HashMaps are often very complex and fragile,
and in my experience leads to bugs in browser engines.

Maybe one day I'll write a "HashMaps are the
[worst](https://www.simonandschuster.com/series/The-Worst!-Series)" blog post
detailing this further.

### Fixing `<video>` with `aspect-ratio: 4/3 auto`

There has been a [relatively bad
bug](https://github.com/w3c/csswg-drafts/issues/7524) in Blink with the
`<video>` element and the aspect-ratio property, namely we ignored the
aspect-ratio coming from a video once it had loaded.

The issue with fixing this bug was that every engine did something different,
and since time immemorial the `<video>` element has had a default
`aspect-ratio` of `2:1`.

There were many web-platform-tests [asserting this
behaviour](https://github.com/web-platform-tests/wpt/pull/54365/files), as
everyone actually did the same thing for the basic case.

The final fix was [very
straight-forward](https://chromium-review.googlesource.com/c/chromium/src/+/6641808/5/third_party/blink/renderer/core/layout/layout_video.cc),
but first required [getting everyone to
agree](https://github.com/w3c/csswg-drafts/issues/12053) that everyone was
wrong, and us proving that the proposed change was web-compatible (fortunately
[Hyrum's Law](https://www.hyrumslaw.com/) didn't apply today).

### Other engines implementing "block-in-inline"

A few years ago we removed a significant code-complexity burden from Blink
which is known as "inline-splitting". We [inherited
this](https://webkit.org/blog/115/webcore-rendering-ii-blocks-and-inlines/#:~:text=Blocks%20inside%20Inline%20Flows)
from WebKit and caused major issues for us (and a security issue or two). The
CSS2 spec also (unhelpfully) [suggests that
engines](https://www.w3.org/TR/CSS2/visuren.html#anonymous-block-level:~:text=are%20broken%20around%20the%20block%2Dlevel%20box)
should implement this problem this way.

Why it's bad is a whole "Ian rants at you for an hour" type scenario - so let's
not get into that today.

We've since ripped this code out, and now both
[WebKit](https://bugs.webkit.org/show_bug.cgi?id=303236) and
[Servo](https://github.com/servo/servo/pull/41492) appear to be implementing
this approach as well last year (hurray!) and this should fix some well known
bugs.

I don’t think I can claim the original idea, (it potentially was a suggestion from
an EdgeHTML engineer), but I believe it is evidence that working in the open,
and writing down how/why you did something can help out others in the same
space later on.

### Merging Anonymous Table Parts

My final piece of work in 2025 was trying to fix a cluster of issues caused by
not merging anonymous table-parts correctly.

When you have an element with `display: table-cell` it implicitly creates a
`table`, `table-section`, `table-row` surrounding it. If you have two `display:
table-cell`s next to each other they belong to the same anonymous table, etc.

We had a long standing issue that when you removed or changed the `display` of
an element, we didn't "merge" these contiguous anonymous table objects correctly.

There have been attempts to fix this within Blink before, but ended up causing
crashes and other issues. I spent a bunch of pre-work fixing things that might
have caused crashes previously, and [landed a
patch](https://chromium-review.googlesource.com/c/chromium/src/+/7244131) to
merge these objects. This should be out in the wild in February (so fingers
crossed), and should fix a non-trivial amount of bugs for web-developers.

<hr>

There are lots of other things I did work-wise that didn't make the cut
(helping ship width:stretch, CSS anchor positioning fixes, various performance
 optimizations, removing `-webkit-box` quirks).

Let's see where 2026 takes us!

