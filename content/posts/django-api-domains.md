---
title: "Django API Domain styleguide"
date: 2019-03-01T17:03:00+00:00
draft: false
---

I've spent the past two years working with a fun little [company](https://www.babylonhealth.com) in the _healthtech_ world.

In January 2018 my team was 2 engineers with a vague idea about solving a growing data problem. Today it is a 28-strong team building a blitz-scaling ML model management system that powers AI symptom checkers across the globe. To manage the ML model powering this AI, we have built a sophisticated _Model Management Service_ that doctors use to moderate medical concepts, their relationships to each other (things like "a cough can be caused by the flu") and teach the ML Model the probability of that relationship in different parts of the world.

Our system is a [Django](https://djangoproject.com) powered service using [graphQL](https://github.com/graphql-python), because most of our data is in a graph, and we have a [React](https://reactjs.org/) frontend that uses this API. I was the original developer on this project and I already had a lot of experience with Django. We picked up Django for the batteries-included plugins, like auth-n and auth-z, ORM, routing, and the strong library of plugins. I didn't have a styleguide or pattern in mind when I started building the project, I just took what I knew about Django and some patterns I had followed before and emergent design _"happened"_.

As we grew the team and took on more feature requests, we started to be contrained by the organisation of our codebase. I encouraged the team to read the django documentation to learn more about how it should be formatted, but it didn't really help. The documentation, from the tutorials to the full docs, talk about a model-view-controller world in which Django renders HTML and delivers it to a web browser.

## Django's styleguide is old

Something about this struck me as odd - I've worked with Django since 2012, and I only remember once using it to render HTML. Nearly all my time with Django, and all the time I have seen Django being talked about at conferences, is to provide an API (usually with [Django REST Framework](https://www.django-rest-framework.org/)) to a frontend project. **I would argue that this is actually the defacto standard for Django today**. The documentation is probably out of date now. This is generally a trend I am seeing with Django - the project is trying to modernise [how it is run](https://github.com/django/deps/pull/47) and even [how to handle async properly](https://github.com/django/deps/blob/master/draft/0006-channels.rst).

**Maybe it is time Django re-considered the design patterns it suggests for developers?**

Back to my immediate problem: in order to help the team organise their software better, I set out to find a good styleguide from the community. I read about Domain driven design, the benefits of bounded contexts, and I found a nice [styleguide by hacksoft](https://github.com/HackSoftware/Django-Styleguide). This was great! The documentation here is very sound, and perfect for smaller projects or small companies.

But we experiemented with this and found it wasn't fit for purpose for a few reasons. Namely, the fact that is encouraged business logic to live in the models. Django also recommends this and it is basically the [active record](https://www.martinfowler.com/eaaCatalog/activeRecord.html) pattern. In our experience with very large teams, keeping business logic tied to the models encouraged developers to fill up `models.py` with tonnes of code and make it very hard to work on it at the same time. Not to mention the fact that once a single file owns more than one problem in a domain (presentation, data, controller, etc) it tends to suck all other problems into the file too.

Another downside to the hacksoft pattern that we had to deal with, which I think is somewhat unique to ML or data-heavy projects, is leaking data dependencies. It turns out, that just like good _Domain Driven Design_, ML-based or data-heavy projects need good _Data Design_ too. Otherwise you put in a huge amount of effort to separate business problems only to discover data ties it all together and silenty erodes the boundaries you tried so hard to create. There has even been a [paper published](https://papers.nips.cc/paper/5656-hidden-technical-debt-in-machine-learning-systems.pdf) which analyses this. Suffice to say - we definitely had these issues in our Model Management Service.

What we wanted in our styleguide was a pattern that encourage strong [bounded contexts](https://www.martinfowler.com/bliki/BoundedContext.html) between the "domains", encouraged "skinny models" so that developers didn't put a boat load of code into one place, and was generally flexible enough to suit most situations.

## Long like Django API Domains styleguide!

We started to take the parts of Hacksoft's styleguide and adapt it to suit our own needs. We encouraged skinny models and a strongly-bounded-context between our "domains" (basically Django's apps). We then discovered that we had to start scaling the software fast, very fast. I'm not talking _"make the software 10x faster"_ fast, I mean _"deliver 10x more features on this now, here is 12 developers, go."_ fast. It was a crazy hectic time. Probably one of the hardest times of my career so far. This styleguide emerged from it, and so far it has proven useful for us.

I am publishing an OS copy of the styleguide today to gather feedback and share what we have learned.

You can now read the [Django API Domain Styleguide here](https://github.com/phalt/django-api-domains).

![dads](https://github.com/phalt/django-api-domains/blob/master/diagrams/dads_main.png)

## The Goals

There are a few key goals of this styleguide that I want to cover in-depth (the guide itself just bullet-points them):

### Treat Django's app more like domains

Django does a good job of encouraging sensible separation of concerns in it's own styleguide. A django app is a packagable, reusable piece of code that solves a business problem. Our guide aims to move this pattern closer to a true Domain idea - full encapsulation, with clearly defined boundaries in and out of the project.

### Build strong bounded contexts between domains

The implementation of the APIs / Interfaces in our domains creates a bounded context that isolates all data and functionality going into and out of the domain. The idea behind this being that, when a domain moves or a domain wants to change it's service, it won't need huge refactoring to work with the rest of the system. It also encourages developers to think in a _graceful failing_ mindset.

### Enable separation of domains to increase developer velocity

In a blitz-scaling team you're often blocked by the number of people who can collaborate on a project at once. This can happen if the software isn't designed to keep developers spread out in the project. Our styleguide encourages domain separation to increase developer velocity **and** when it makes sense for business problems. For example - having more than six developers working in one domain? Maybe it's time to split that so the teams can work separately.

### Reduce the effort to refactor and move code

If you follow the styleguide you will create software that is made up of lots of isolated components. Each component should be easy to pull out and put somewhere else without much disruption. This builds upon Django's original goals of `apps` but extends it more.

### Support API-based applications first

As mentioned at the start of this post, most Django apps provide APIs, including the one we use. It makes sense for the styleguide to benefit this and drop any support for server-side-rendered-views as we don't need them. We go all in on this in our styleguide.

## Feedback plz

This isn't the end of the journey for this styleguide. I want to gather feedback from the community and see if this helps other people. Additionally, I want to start encouraging the Django community to consider if we should be re-designing the suggested patterns in Django's documentation. If we're looking to give the project a fresh coat of pain in some areas, maybe we should be doing it in the styleguide section too.

Thoughts, questions? Contact me on [twitter](https://twitter.com/phalt_)
