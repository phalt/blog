---
title: "Productive Team Ceremonies"
date: 2018-10-10T14:00:00+01:00
draft: true
---

Babylon has hired ThoughtWorks to help us deliver some of our WoWs during the second half of 2018.
Hello, I'm Paul and I am the Tech Lead for AIM. Our goal is to build tools that support the AI Doctors, Epidemiologists, and Researchers at Babylon. You may have heard talk of an AI CMS - that's our project! We are also one of the teams that has embedded ThoughtWorks support.

There is one clear business benefit to us working with an agency: hitting our WoWs for H2 2018. However, during our time with ThoughtWorks we have discovered a second benefit: new ways of working. These new ways of working have had a noticeable effect on our software development velocity. Because of this we are able to deliver on some key requirements in record time!
In my opinion, adopting these ceremonies and processes are as valuable as the delivery goals we have to meet. As our company grows; we need to be continuously optimising how we work and how we maximise our effectiveness as engineering teams. It is my hope that if I share some of these practices with you then your team can try them out, and see if they help you too!
Dev Huddles
Consider the following scenarios:
1) Your team's PM has gathered some requirements from a stakeholder, and now the team needs to work out a technical solution that meets the requirements.
2) One of your team has discovered a better way to architect part of your software, or fix some tech debt, and now they want to discuss adopting it with the rest of the team.
In both of these cases the agenda is the same: Your team has a problem, and your team needs to work out the effort involved in changing your system to solve the problem.
Generally discussions around the problems are left until sprint planning. This is far too late for the team to share context around the problem. The team is also unable to confidently express the effort involved in solving the problem. No tickets should go into a sprint that hasn't had one at least one Dev Huddle session to cover it. You can cover multiple problems in one Dev Huddle session.
Dev Huddles are 30-60 minute, tech-only informal meetings. They allow the tech team to share context about the problem. The team then discusses the problem and start solutionising it - coming up with a high-level way of solving the problem.
The output of a Dev Huddle should be either:
1) Follow-up actions assigned to one of the tech team, who will present the outcomes of the actions at the next Dev Huddle, which they are responsible for organising. For example - they might research libraries that help them solve the problem, or research design patterns.
2) A rough series of tasks needed to solve the problem, with T-shirt sizes against each task. These tasks should be ready to be turned into stories in your JIRA board.
The benefits of Dev Huddles are:
1) Shared context of the problem. Any engineer in the team should be able to pick up the task and work on it with some understanding of what the problem is and what the solution is.
2) A clear understanding of the effort involved. This allows stakeholders and PMs to understand the expectations around how long it will take to deliver something.

Dev Boxing
You have spent the last few days coding to solve a problem. Your create a Pull Request, and your changes are merged. The next day, a QA picks up the task and starts QAing. They immediately discover bugs and failing acceptance criteria. They log these bugs in the ticket. Because you picked up another task - you wait until the following day to jump back on the ticket. You solve the problems, and QA is happy. Finally you demo the work to the PM, and they explain that the requirement has been misunderstood by you and QA. It's back to square one.
Do you see the problem here? It takes too long between hand-over to get feedback about the work that everyone is doing. Dev Boxing is a way to reduce the feedback loop to minutes instead of days.
Dev Boxing is when an Engineer, a QA, and a PM (and sometimes a stakeholder) stand around the Engineer's machine and look at the new feature the Engineer has just built. The QA and the PM test out the new feature and give feedback. This should take roughly 10-15 minutes and it does not need to be as thorough as a real QA session. This happens before a PR is submitted but just before the Engineer is ready to submit their code for code review. (by the way - it is named Dev Boxing because you stand around the developer's machine, or box).
The goal of a Dev Boxing session is:
1) Quick feedback loop for bugs and missing requirements. The Engineer knows, within minutes, what bugs there are and what requirements they have forgotten to cover.
2) Reduces toil and increases velocity of software development. A bug that could be a two-minute fix will usually days to get back into the hands of an Engineer. With Dev Boxing this can be fixed immediately. Time is saved, toil is reduced, and features are shipped faster.

Effort Spent
Another ceremony the team has adopted is tracking all effort spent during a sprint - effort spent dealing with production issues, effort spent when someone is blocked, even the effort spent finalising requirements for a task.
Sometimes a stakeholder will request a new feature or a bug fix and then ask how long the it will take to finish the task. A tech team will give them a rough estimate which is based on the time spent developing the solution to the task and then getting it into production. More often than not, there are complications or situations that change that estimate. When it comes to delivering a task late, or when the estimated work for a sprint is not the actual work done, it is very hard to show where the effort was actually spent. The stakeholder will ask "Why didn't it get shipped when you said you could do it in this amount of time?".
Tracking Effort Spent on all tasks - not just tech tasks, is a way of helping stakeholders understand what delayed the release of a feature or why it took so long to fix a bug. This can actually help vindicate your team's desire to work on tech debt - when you show it is blocking the release of features, non-technical stakeholders understand why you want to work on it.
Some of the things you should start tracking the effort of:
1) Production issues - how many engineering hours were put into dealing with production bugs or server issues?
2) Re-evaluating requirements - how many engineering hours were put on hold because the requirements were not clear and you had to change them half way through a sprint?
3) Being blocked by other Engineers or other teams - how many engineering hours were put on hold because you had an external dependency or your work was blocked by other Engineers in the team?
4) Problems QAing or testing changes - how many engineering hours were interrupted because your testing environment isn't built for quick and iterative feedback?
There are many ways you can track Effort Spent. Our team creates JIRA tickets for all tasks, even if they just track the investigation time spent around a bug bug. Do what works best for your team, just make sure you present it in a non-technical way for your stakeholders to understand.

Continuous Improvement
These are just a few ceremonies from a number that we have adopted. In the future I hope to cover how we are doing Requirements Discovery, Retrospectives, and Velocity Planning. The goal of each ceremony is to continuously recognise areas for improvement and act on them. This is something that all teams need the time and space to do.
If you have any questions about the ceremonies I've shared here, or you want me to help your team learning them, then contact me on slack (Paul Hallett) or email at paul.hallett@babylonhealth.com
