+++
title = "Testing and Deployment"
date = 2020-11-16T15:49:08+01:00
draft = false
pre = "<b>5. </b>"
weight = 5
+++

## DevOps

The term DevOps is loosely used and it can have multiple semantics depending on the context. The way we will use it
throughout the course and what it refers to in the development lifecycle is a mindset of having a __robust pipeline for
features development__.

The pipeline relies on tight feedback loops all throughout the different product teams. Imagine if one of the product
features is retired, the benefit of the teams being able to function autonomously is that the delivery is not affected
one bit.

#### In practice

The team feedback loop is the automated test suite. This allows you to get feedback on changes as you
write your code. Because we are working on the frontend we need feedback both on functionality and visuals. We need to
focus on end to end tests that run the same way as a user would interact with the app.

The tight feedback loop at the feature team layer helps with rapid turnaround and faster onboarding for new devs. Standing up a
production environment should be as easy as pushing a button(in dev speak running a script). Having these tighter
feedback loops also allows you to have a clear understanding of the definition of done, both in terms of compliance with
the __build should be green__ mantra and the visual feedback from our design system set up.

#### Testing pyramid

{{< lazy-image image="test-pyramid.png" lightbox=false />}}

