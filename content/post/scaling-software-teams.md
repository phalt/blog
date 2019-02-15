---
title: "Scaling Software Teams Is Really Difficult"
date: 2019-02-01T17:03:00+00:00
draft: false
---

_This blog post is a readable version of a talk I was planning to give at Pycon US 2019._

For those of you who do not already know me; I am Paul. I write  software. I’ve been doing that now for about eight years or so, usually with Python. Currently I work as a Tech Lead at a [healthtech company](https://babylonhealth.com) in rainy London, UK.

The company's goal is to improve the efficiency of providing healthcare. One of the ways we do this is with a symptom checker. It works like this: you talk to a chatbot, and it runs some ML algorithms to  predict what disease you might have. We have Doctors who input the data needed to make those predictions. We found that Doctors  don't understand JSON blobs, and asking them to fill in the blanks was difficult.  So instead we started to build a tool that speaks their language, and gathers the data in a format that the ML algorithms want. We call this our Model Management System.

From around June 2018 we started closing deals to provide our symptom check around the world. Part of those deals were some new feature requests and complications. For example - how we describe our "head pain" in British English, could be very different from Malay. This is an over simplified example, but it gets difficult when you factor in cultural, social, and religious sensitivities, not to mention specific product requirements from partners. We ended up having a huge number of dimensions that we need to slice our data on.

So, we had a growing list of feature requests and not enough developers to help build it all. We decided to hire and grow the team to help ship these requirements on time. "Just add more developers" was our instruction from the senior staff in the company.

In short; we managed to grow the team from two engineers to a team of twenty eight. Of those twenty eight, around twenty of them are developers. We increased our velocity and our productivity, and we are currently on track to deliver some key growth enablers.

I'm here to explain to you today that **scaling software teams is very difficult**. It's not a simple case of "add more devs". There are problems that you won't expect.

I want to share with you some of the key things we learned, so when you try and explosively grow your team, you can plan ahead and hopefully avoid the pitfalls we found.

# The key things we learned

In summary, the things we learned to help us grow were:

1. Challenge Conventional Wisdom.
2. Refactor your software, and your teams.
3. Be pragmatic about your design choices.
4. Make it easy to learn about parts of the project.

# Challenge Conventional Wisdom

I'm going to use an analogy to help me describe this one. I recently started playing the boardgame [Go](https://en.wikipedia.org/wiki/Go_(game)). It's a hugely satisifying game when you learn to play it right. When I started picking up the basics, I discovered a lot of parallels with the things we did to scale our software team.

There is this famous game called the _"Lee Sedol ladder game"_. He's the person who famously lost to AlphaGo (but remember - he is also the only person to beat AlphaGo!). In Go, there is a particular pattern of stones called a _"ladder"_ that is considered a really bad play. The conventional wisdom is that it is    futile to play out a ladder because the person trapped inside will lose and the person capturing will waste turns capturing. Normally both players recognise a ladder and then they just play something else. During this game, Lee Sedol and his opponent reached a point where a ladder would be formed. Everyone said they wouldn't play it, because it's stupid and wasteful. Be Lee Sedol forced the play and his opponent rose to the challenge. What his opponent didn't realise was that Lee had planned it all along and tricked him. He ended up winning that game.

![lee_sedol_ladder_game](https://senseis.xmp.net/diagrams/10/fd01d92aad5e803194846f8f88e68a92.png)

How is this relevant to scaling software teams? Conventional wisdom is a form of emergent design. We build things slowly over time based on what works right for that moment. You learn to never play a ladder in Go because you play it once or twice, realise it is bad, and then never play it again. Emergent design happens when patterns start to form chaotically without any direct control over it. Sometimes emergent design makes sense, it's like evolution. But it only makes sense for the time it emerges, not for the future.

How many times have you started working on a project and asked _"Why is this built the way it is?"_ and you get an answer like: _"It's that way just because it is."_. There is no real reason, it emerged from chaos, became the norm, and settled in place. It's kind of like a form of **sunk cost fallacy** - _"We do this this way because it cost us a lot to get here and it will cost too much to change it now."_

When you start scaling teams you need to **challenge conventional wisdom** in your project. Play that ladder out, even though everyone says it is wasteful to try. Re-draw your business domain model with today's understanding and ask yourself if your software fits in that world.

What needs to change in order for your software to fit? What is redundant? What doesn't make sense? If you don't do this, you will be held back by the shackles of past decisions and they will make it hard for you to move forward.

# Refactor your software, and your teams

In January I had a one-to-one with my manager. We were talking about  the recent re-organisation of the company. We shifted around teams, our  management, and our reporting path. The funny thing is - we did  the same thing a few months before.

I asked my manager _“We just finished doing a re-org, why are we doing another one?”_ and they replied _“You enable progress when you re-organise things”_.

We know that if we don’t refactor often then our software will slow us  down. A project built by one developer cannot be worked on by six  developers. Because one developer hasn’t built it for six  developers, they built the features they needed at the time. Each  new feature you add will change how your software works. Business  struggles to find value in refactoring. The truth is that **refactoring creates productivity**.

![](ref_1.png)
![](ref_2.png)
![](ref_3.png)

I did a twitter poll about refactoring. The results told me that  developers want to refactor as often as they build new features. This is  good because it matches the assumptions I’ve seen in my team. But from  the poll it seems that developers do not get to refactor as often as  they like. I have also struggled with this. Showing value to the  business with refactoring is very difficult. If you approach it from  the angle of productivity and provide clear diagrams of the cost  (especially if you can figure our $$$ cost) it can help. Remember: every  decision to not refactor immediately limits the productivity of a team.  Software that isn’t refactored becomes technical debt.

The same applies for teams. We found that the way two developers  worked together didn’t work for a team with eight members, or even  twenty. We had to refactor the team and re-do how we organised  ourselves. We split into smaller teams of 4 devs, a tech lead, and a BA.  That felt right for the time. The boost to productivity was immediately  obvious.

# Be pragmatic about your design choices

Let's shift back to the game of Go. With other boardgames like Chess or Checkers the problem space is much smaller. You can be pretty good at Chess or Checkers quite quickly, and you don't have to think too many turns ahead to win. With Go, the entire game _is about thinking ahead_. You will always lose if you do not think ahead about your actions. There is this concept in Go called *Kame no Itte* - it means the "move of God". Players spend years in pursuit of it. A single *Kame no Itte* could be the accumulation of a lifetime of practice. This "God move" is a move that is completely original and inspired. It is the type of move that will put you in a position to deal with any situation.

When you are scaling your software team, you need to find the *Kame no Itte* of software design. It means you should make pragmatic design choices. For us, we found that our *divine design* was one that exploited the ability to easily pivot. We created a styleguide with lots of decoupled domains that each how their own internal flexible design. We've even published [the styleguide](https://github.com/phalt/django-api-domains) and I've written [a blog post about it](https://phalt.github.io/post/django-api-domains/).

This design choice has made it simple to add new features, refactor existing components, and isolate legacy areas of our code. My suggestion to you is to read styleguides from other teams, understand _why_ they were made. Take the bits you need to help with your own problems and share it with the team.

# Make it easy to learn new parts of the codebase

How many on-boarding experiences have you had that were bad? Have you struggled to set up a project, or find it difficult to understand the design choices in software? [According to a poll I ran](https://twitter.com/phalt_/status/1095280357128241152) these are all equally limiting things when new developers are trying to learn a project. When you are hiring two developers a month (which we were), this means our existing developers have to stop their work and on-board the new team members. Therefore no work gets done in about three months. This shuts down the "just hire more devs" argument completely.

So what can we do to make it easier for teams to learn new parts of the codebase?

The first thing we did was break it down into two problems:

1) How do I understand existing parts of the codebase?
2) How do I make sure new parts of the codebase that I develop are well understood?

