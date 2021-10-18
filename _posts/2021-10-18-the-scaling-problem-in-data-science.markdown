---
layout: post
title:  "The scaling problem in data science"
date:   2021-06-28 00:00:00 +0100
categories: data science, scaling
---

Data science in my opinion has a scaling problem. Not in the sense that we're being over-loaded with data and don't know how to handle it, but in the sense that the growth and velocity of data science teams is hampered by the organisation and practices of the team itself.


## Most roads are dead ends
In software engineering, most teams are always planning and iterating on their code, continually improving it with the tacit knowledge that this will be used for possibly years to come. Even if not, engineers already have a good idea that theyre code will be used many times.

In contrast, most data science work is end of the road analysis: the work is agreed to be carried out with some objectives, the analysis carried out, the result of your analysis is shown off to stakeholders, and then that's it. You're work has delivered the value required.

In fairness, sometimes that is where the work must end as it no longer generates any value for the organisation. This is probably true for a consultancy more than any other sort of business. 

However this kind of 'throw-away' analysis is a limiting factor in how much business a DS team can generate. Take the example above, to scale that approach would mean taking on a linearly proportionate number of staff to tackle the work - an obvious red flag when it comes to scaling.

## Repos, PRs, and continous integration.
Many data science teams have already noticed the challenges in building quality and reproducible workflows and have leveraged some SWE behaviours and tooling to assist in this. Yet, unsurprisingly, the common approaches aren't optimised for data science workflows.

Take repos. Most DS teams will create a new repo for each project and, like the example above, once the project completes the repo is left to be forgotten. If your data science team is a bit more 'professional' than others they will have continous integration setup for each repo, with the undeserved confidence of swagger that a nightly passing build gives. The team might even take on other SWE principles like code re-use, and moving common code out into it's own repository to then be used as a shared library.

It's not a bad approach either as it does the job, but it does introduce even more resources that the team must manage - every repo added, is another repo that needs human resources to be maintained. For every CI, you must also maintain that CI. 

## Painful delivery
My final point I want to make is that most teams still haven't got the hang of delivering value. Sure, they can write up a report, maybe make a nice shiny dashboard in R to show off to stakeholders, but they struggle to deliver these artefacts while taking an agile approach that has the minimum of human interaction.

Even teams that do, have painfully long and high-toil delivery pipelines.

