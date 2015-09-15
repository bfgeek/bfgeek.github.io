---
layout:     post
title:      The Performance of Houdini Paint
date:       2015-09-14 16:00:00
summary:    The hidden performance benefits of the Houdini effort.
categories: houdini
---

I wrote this blog post in response to @sil's [tweet](https://twitter.com/sil/status/640891538483081216) because I can't fit it into 140 characters. :)

I also still write on the web like I'm going to submit an essay to my Year 12 teacher for grading. So excuse the formality.

### Overview (aka. the start bit)

So Houdini is trying to explain the "magic" (pun!) of CSS. What does this mean? It means giving web developers the primitives available to that they can begin to implement the features of the platform themselves.

My favourite example of this is... remember when border-radius wasn't a thing? Imagine if you had the primitive to define your own border drawing code, and not rely on a horrible border-9-patch solution.

Houdini is aiming to give web developers those primitives, and one of them is Houdini Paint. For a rough look at the API see the spec [here](https://drafts.css-houdini.org/css-paint-api/).

### Less Work == Speed

The Houdini Paint API should allow browsers to perform exactly the same set of optimizations they perform when painting a page. Browsers dislike drawing all the possible pixels for the page, they'll typically just draw what is actually visible. For example an element below the visible viewport (e.g. below the fold on a scrollable area), the browser may perform layout but not any of the painting steps required for that element.

Browser rendering engines are also able to do things like prepaint areas when a user is scrolling the page, to enable seamless scrolling.

This is in contrast to attempting to do all those optimizations as a web developer.

For example, if you have a canvas element, how do you know if you should paint it? You can use `elem.getBoundingClientRect` for an element's position, but is it visible? Is it being occluded by a whole element above it? Is it occluded by a deeply nested scroller?

The naive approach is to simply paint every canvas but this obviously does a lot of work on the user's device if these effects are used heavily. It is also a lot of work for the device to perform the checks that the rendering engine performs during its paint phase.

But by hooking directly into the paint phase of browser rendering engines you get all of the optimized laziness for free.

### Memory Optimizations

When creating paint effects with canvas' it is typical for developers to create one canvas per element they are painting into. These canvas' (or output from the canvas) aren't typically removed to keep memory usage low. To do this correctly requires bookkeeping if an element is in the viewport, if it is in the DOM, ..., etc.

Similar to the "speed" optimizations listed above the rendering engine knows where an element is in the page, if it can "throw away" the output (i.e. the drawn pixels) of the element if it's no longer needed.

Again, in contrast to attempting to do all this as a web developer. There is a lot of bookkeeping required, and knowledge of what happens in a particular rendering engine. By building on the foundations of the rendering engine, you potentially get memory performance improvements for free.

### Ergonomics

By building the Houdini Paint into the platform is that you get a lot of ergonomic benefits of CSS.

There is the obvious ergonomics from using css that you can share rules between elements:
{% highlight css %}
.add-my-paint {
  background-image: paint(my-paint);
  --circle-color: blue;
}
{% endhighlight %}

But you can also leverage other things on the web platform such as Web Animations:
{% highlight javascript %}
elem.animate([
  {'--circle-color': 'red'},
  {'--circle-color': 'blue'}
], {
  direction: 'alternate', duration: 5000, iterations: 10
});
{% endhighlight %}

Because the CSS engine will know that `--circle-color` is a `<color>` type, the animations engine can correctly interpolate between 'red' & 'blue'.

Additionally this allows for future performance benefits on behalf of browsers. One un-written feature of the web today is that you animate certain properties these are 'fast'; for example when you animate a 'transform' property.

If browsers decide to move painting over to the compositor thread, animations of Houdini Painted elements could run without jank, even if the main thread is busy handling user input. I'm not saying this will definitely happen, but it's just one optimization that this API will allow.

### Capabilities

Finally there are things that developers will be able to do in Houdini Paint which isn't immediately possible in a canvas at the moment.

1. Paint individual fragments of elements. The web doesn't expose fragments well. For example imagine a `<span>` element broken over two lines, with custom paint, your callback will get invoked once for each fragment.

2. No layout thrashing / guaranteed geometry. In main script javascript it's difficult (impossible?) to know an element's size right before browsers paint occurs. For example an event may come in and change everything. `requestAnimationFrame` is way too early for this purpose.

3. Overflow painting. Some effects are nigh impossible with a canvas effect. For example, try and create a box-shadow... that works everywhere. 

4. Moar geometry. Fragment position data, children position data. So. Much. Data.

### Summary

Explaining the platform is exciting as web authors can start to rely on the underlying optimizations within browsers to start to produce high quality, efficient effects. 

