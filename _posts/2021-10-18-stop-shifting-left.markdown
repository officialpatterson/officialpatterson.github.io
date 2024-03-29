---
layout: post
title:  "Stop shifting left"
date:   2021-06-28 00:00:00 +0100
categories: lean
---

In the wake of lean software development, many teams have implemented 'shift-left' practices in the hopes that it will improve their value stream model by capturing problems sooner in the software development process than later. In some contexts, such as testing, this makes perfect sense as it is well known that capturing bugs earlier in the process means we can address them earlier and sink much less resources into a project before surfacing these problems.

The problem though, is that some organisations have began to apply this method blindingly, under the belief that shifting-left an activity from a constrained resource to an earlier resource will solve their problem of constrained resources. What quickly happens is that the earlier resource becomes burdened with the activity.

In effect the value stream hasn't been optimised. Instead, you've now got Tom and Dick doing their work from earlier in the stream **and** also doing the work of Harry who was doing the burdensome activity from later in the value stream. You've not solved any problem, you've just moved it about.

I've even seen this problem materialise in multi-disciplinary teams where shift-left has resulted in operations engineers burdening data scientists with how to deploy and scale their models on the cloud. I'm not saying it's not good for them to know how their code is deployed in production(knowledge sharing is good after all) but every team player should play to their strengths.

Instead, orgs that find human resources that are constrained by certain activities should either increase their head count (bad) to reduce the resource contention or by reducing the burden of the task by automating the activity as much as possible (good). Over the short term this requires an increase in temporarily increasing the cost of the activity while automation is implemented but the cost is amortised over the long term.

In short, shifting-left isn't necessarily a bad thing when applied for the right reasons, resource contention not being one of them.
