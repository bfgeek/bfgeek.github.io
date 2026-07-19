---
title: What is Product Quality?
description: 
hero: hero.jpg
tags:
  - quality
---

In my experience product quality is an extremely poorly understood concept in organisations.

<figure>
  <img src="hero.jpg" width="1408" height="768">
  <figcaption>A Gemini generated image of the scene from <a href="https://www.youtube.com/watch?v=N9wsjroVlu8">Office Space</a> smashing the printer.</figcaption>
</figure>


Typically there isn’t a shared understanding of what it means. Different people
have different mental models. As a result two people may both believe they are
improving product quality, but believe the other people are working on
something futile.

Let’s see what [Wikipedia has to say](https://en.wikipedia.org/wiki/Quality_\(business\)):

> In business, engineering, and manufacturing, quality has a pragmatic
interpretation as the conformity of something (goods or services); it is
defined as being suitable for the intended purpose (fitness for purpose) while
satisfying customer expectations.

Great \- thanks for that word soup.

As an exercise for you the reader \- ask yourself how you define quality in
your product. Ask your colleagues. Better yet, if you really want a fun
exercise, ask your leadership \- see if they come up with a similar word soup.

Your answers will likely vary wildly. Instead many organisations will agree on
some vanity metrics. These might include:

* Number of bugs open
* Latency at the 99%-ile
* User churn rate
* CSAT (Customer Satisfaction Score)

These metrics aren't bad in themselves, but they are proxies for quality.

You might be thinking to yourself \- “Okay smarty-pants, what is quality then?”
I'd argue it is very simple, but difficult to measure.

> **Product quality is how the customer feels about your product \- what emotions
does your product produce when used?**

That's it. That's the whole ball game. If you squint other definitions match
this, but not before they've confused the hell out of everyone.

Now we get to the hard bit. What emotions do you want your customer to feel
about your product?

Broadly speaking emotions can be grouped into “positive” (*delight*, *pride*,
*interest*) and “negative” (*annoyance*, *irritation*, *fear*, *worry*).

The first thing you probably want to optimize for is to ensure your product
doesn’t trigger ***any*** major negative emotions. A single major negative
emotion can completely crowd out a dozen positive ones.

For example \- if you produce a phone which [spontaneously
combusts](https://en.wikipedia.org/wiki/Samsung_Galaxy_Note_7#Battery_explosions_and_recalls)
\- even if the customer really loved the design/color \- they’d be hard pressed
to ever trust your brand again.

A great recent example is [Engineering Explained’s review of the Lucid
Air](https://www.youtube.com/watch?v=1WiQAOmESH0). I implore you to watch this
video, and see the range of negative emotions. He bought this car and genuinely
believed this was the “best engineered vehicle the world has ever seen”. Jason
likely was excited/elated/etc by his new purchase. Only for a wave of negative
emotions to push those out.

What does this mean for you?

You should deeply care when customers are *frustrated*/*irritated*/*annoyed* by your
product. If you are trying to improve product quality, this is the first thing
you should focus on. Have a product that doesn’t reliably turn on the lights
when asked? You should probably fix that before anything else.  Have a product
that takes 10 seconds to respond to a query? You should probably fix that.

From this you can start building proxy metrics. E.g. latency at the 99%-ile
might matter\! (Especially if it's 10 seconds\!) But it might not matter
anymore once it's below 200ms. (What matters performance wise is a whole
separate blogpost which I should write someday).

The ***rate*** of incoming bug reports is also typically a good metric. “How
fast are people filing bugs compared to the last version?” (Assuming the way
people file bugs between versions is exactly the same).

An important thing to note here is that if a customer feels ***no emotion***
that is a ***good thing***. If a customer successfully pays some bill and feels
absolutely nothing, that’s great\! It means that the experience was
frictionless and you made it as easy as possible.

Minor annoyances are also okay. If an animation occasionally stutters or a
setting is buried a couple of levels deep, that’s usually fine—as long as the
majority of your customers aren’t experiencing it. You shouldn’t go on a
crusade to ensure there are absolutely no negative emotions, you need to spend
your time wisely, focus on the major/severe issues (even if limited to a few
customers) or widespread ones.

Once a product has a solid foundation (your customer doesn’t want to throw your
product out a window), you can start to care about the minor annoyances if
needed, and the positive emotions.

A design refresh can really matter\! It can take something boring to use and
make it delightful (think about how fun tapping a like button was the first
time popping up little hearts).
But this shouldn’t be at the cost of making the product harder to use and
frustrating customers.

Marketing really matters too\! It can predispose the customer into feeling
excitement or anticipation when first using your product. This is also why the
OOBE (Out of Box Experience) is so important. Your first interaction with the
product predisposes the customer.

However a common trap for organisations is to believe that the positive
emotions e.g. triggering delight is the only thing that matters, and believe
that if customers “love” a product enough they will overlook the desire to
throw it out the window.

This isn’t the case.

So what should you do? Firstly make sure that everyone has a shared
understanding of what you are optimizing for. As I’ve argued for here I think a
customer's emotions define product quality. But you should get everyone singing
from the same hymn sheet.

Next ensure that your proxy metrics (latency, churn, bug rate) actually match
this shared understanding. One metric I hate for example is open bugs, the
metric you should actually care about is incoming bug rate (even if those
issues are merged into others).

Finally, when it comes to prioritization, use this quality framework to
advocate for what your customer actually needs \- rather than a vanity project
that will be deprecated in six months.

Questions/thoughts/comments? Reach out to me on
[BlueSky](https://bsky.app/profile/bfgeek.bsky.social).

