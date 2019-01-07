---
title: "Scaling software around growing teams and growing ML data"
date: 2019-01-03T10:11:00+00:00
draft: true
---

I've spent the past two years working with a fun little tech [company](https://www.babylonhealth.com) in the _healthtech_ world. 

In January 2018 my team was 2 engineers with a vague idea about solving a growing data problem. Today it is a 20-strong team dealing with a problem that we realise hasn't been solved properly yet, in our company or in any other. In addition to this, our company is growing at a startling rate - 10x staff growth in less than 12 months, a user registration growth of similar figures, and scaling issues unlike anything I've seen before.

We're trying to handle growth this with [blitzscaling](https://www.blitzscaling.com/) - priortising speed over efficiency in the face of uncertainty.

For us specifically this means prioritising software customisiation over software speed because we don't know the best way to solve the scaling problem we have.

The scaling problems commonly discussed in technology circles is the scaling of performance. I.e. - _how can I make my software efficiently support 10x and 100x more concurrent users_. In the last 10-15 years various cloud services and orchestration tools have been developed, like AWS, and kubernetes, that puts this _performance_ problem mostly in the "solved if you install this thing" category. That's an over simplification, but the point I'm trying to make is: 

Most engineering teams do not have to invent anything new to deal with software scaling problems anymore.

## The New Scaling Problem(s)

So what scaling problems am I talking about?

The problems we've seen fall into roughly two categories:

1) Scaling software to support 10x and 100x more concurrent _developers_.
2) Scaling software to support more and more complex ML data.

The first problem is a problem that has been around since software was first written. One or two engineers develop a piece of software, but they're not so experienced and so they tightly couple various aspects of it - perhaps the presentation and data layer are inherently coupled. A few months later you spin out a "presentation" team and a "data" team but you discover they can't work independently because the software is tightly coupled. They're stepping on each others toes a lot, which in software means many merge conflicts. They're finding that they can't parallelise work because only one developer can work on the software at a time.

This problem is mostly tackled with good software design. But I wouldn't be writing about it now if I didn't have anything new to add. In the process of growing our own software team and dealing with the issues described above, we have found new, pragmatic ways of designing software that optimises the number of developers who can work on it.

The second problem is the newer problem, for my team but also for the industry as a whole. It's been a common fact in Machine Learning circles that it is [easy to build and release ML products but incredibly hard to maintain them once they are in production](https://papers.nips.cc/paper/5656-hidden-technical-debt-in-machine-learning-systems). Normal software design doesn't necessarily work well with this _scaling data_ problem and I'll go into the reasons why.

When you combine these two problems it makes for a difficult scenario, a _perfect storm of scaling issues_ unlike the scaling issues I've heard of in the industry.

My hope is that by sharing what I've learned it will give me a chance to reflect on the decisions I've made, and also offer them up for criticial feedback.