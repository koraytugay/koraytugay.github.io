---
layout: default
title:  "The Phoenix Project"
---

# The Phoenix Project
{:.no_toc}

* TOC
{:toc}

## Characters
__Bill Palmer__
- Former Director of Midrange Technology Operations
- VP of IT Operations

__Brent__
- Highly skilled engineer
- Has know-how on many applications and systems
- One of the few people who understand how all the applications talk together

__Chris Allers__
- VP of Application Development
- Responsible for developing applications

__Dick Landrey__
- CFO

__Erik__
- Technology hotshot
- Potential board member

__John Pesche__
- Chief Information Security Officer
- Looks to be in mid-thirties with thick, curly black hair

__Patty__
- Director of IT Service Support
- Owns all the level 1 and 2 help desk technicians
- Owns the key processes and tools that the entire IT Operations organization relies upon
  - Trouble ticketing system
  - Monitoring
  - Running change management meetings

__Steve Masters__
- CEO
- Acting CIO

__Wes__
- Director of Distributed Technology Operations
- Responsible for 
  - over a thousand Windows servers
  - for the database and networking teams

## Acronyms
__CAB__
- Change Advisory Board
- Lead by Patty
- Responsible for Change Management Process

__CMM__
- Change Management Meeting
- Where a team coordinates and organizes all their work to make sure they do not shoot ourselves in the foot

__SAN__
- Storage Area Networks
- Provides centralized storage to many critical systems
- Failures are typically global

__SEV 1 Outage__
- Severity 1 Outage
- Serious business-impacting incidents
- Everything else is dropped until a SEV 1 is solved

## Parts Unlimited
- Manufacturing and retail company based in Elkhart Grove

__MRP-8__
- One of the manufacturing plants
- An enormous building in good condition and recent renovations

__NOC__
- Found within Building 7
- Network Operations Center
- A large area displaying the status of all the various IT services on large monitors

## Book Summary
### The Phoenix Project
Parts Unlimited is rapidly falling behind the competition in terms of sales. The main problem is constantly being late to the market.

> I hear a commercial from our largest competitor. They are talking about the new service they are offering. How do they keep bringing such incredible new capabilities to market while we remain stuck in the mud?

Bill gets promoted to the VP of IT Operations role learning he must make the Phoenix Project go live:

> Phoenix Project is essential to closing the gap with the competiton. Customer needs to be able to buy from us from wherever they want, whether it's on the Internet or in our retail stores.
>
> We are __years late delivering__.

### Untracable Work
The very same day payroll system is broken, and he only has until the end of the day to fix it. Bill starts investigating and learns how the financial systems get the data. He goes to Wes and Patty and overhears some engineers talking about _an unsuccessful update to SAN_.

Bill is hesitant to believe the root cause is SAN being bricked, he claims 
there would have been more wide-spread outages if it was. They decide to go talk to Brent, the guy who was doing the upgrade.

Brent informs them they tried an update to SAN the day before and upon reboot there were tests failing. After 15 minutes payroll system was broken, which made them link the update failure to payroll system failure. Panicking, they try rolling back the update, which ends up bricking SAN and taking several databases down.

Bill pushes more asking what else was going on yesterday.

> A developer for the timekeeping application called me yesterday with a question about the database structure. I was in the middle of working on something, I gave him a quick answer.

Following the trail, Wes finds it was a developer, who was doing updates to the time keeping applications database for a project driven by John.

### Absence of Test Environment
They contact John and ask whether they have tested the change before applying it to which he gets the response:

> No, we could not test the change. There's no test environment.

They finally solve the problem at 7 p.m., when it is too late.

### Lack of Planning
The next morning Bill is invited to an emergency meeting. Sarah takes the lead 

> First on our agenda is Phoenix. Last week there were twelve tasks in the critical path of Phoenix Phase 1. Only three of those tasks were completed.

To which Bill responds:

> The critical resource in question is Brent, who has been one hundred percent utilized helping to recover from the payroll failure. This was an emergency that we had to handle.

Sarah puts pressure on Bill and Chris during the meeting, expecting to get an answer to the question _When is the soonest we can go live?_.  Chris is forced to spit out a date and so he does without considering all the things that the Operations team must do before going live. Wes responds with several arguments:

> Your guys still haven't told us what sort of infrastructure we need, we can't even order the necessary servers.
>
> You're supposed to support 250 transactions per second, and you're barely doing four.
>
> We still don't have a concrete specification of how the production and test systems should be configured. 
>
> Oh, do you guys not need a test environment anymore? You haven't even done any real testing of your code yet, because that fell off the schedule, too.

Steve appricates the risks and demands the rollout to be done by next week. Walking out of the meeting, Wes and Bill are on the same page that there will be big trouble ahead.

### Processes Not Being Followed
Bill goes to Change Management Meeting in the afternoon, to see an empty room. Patty explains:

> All we have is a process on paper that no one follows and a tool that no one uses. When I pester people to use them, all I get are complaints and excuses.

Bill assures Patty that the change management system is very important and this has to change. He asks her to schedule another meeting informing everyone that attandence is mandatory. 

Following all this Bill broadcasts via e-mail:

> Effective immediately, managers are required to attend all scheduled CAB meetings and to perform their assigned duties.

A few minutes later Wes calls Bills, explaining why the change management process is not popular within IT:

> No one could get a single thing done. It was ridiculous and a waste of time. That software application she made us use is a total piece of crap. It takes twenty minutes to fill out all those fields for a simple five-minute change!

### Unplanned Work
Bill starts yet another day with an invitation to an emergency meeting with the internal audit.

> We found 952 IT general control deficiencies, of which 16 are significant.  You need to investigate each of these findings, confirm them, and then create a remediation plan. We only have three weeks until the external auditors arrive. We require your response by one week from Monday.

Bill is now wondering where he is going to find resources for it.

### Constraints
Wes and Bill starts a conversation after the meeting, which leads to Wes commenting they would need Brent for a bunch of the items. Bill is not happy hearing this.

> Brent. Brent, Brent, Brent! Can't we do anything without him? I don't care how talented he is. If you're telling me that our organization can't do anything without him, we've got a big problem.

Wes, admits Brent may know more about how the company works compared to him.

> You're a senior manager. This should be as unacceptable to you as it is to me!

### Unkown Workload
As the discussion goes, Wes complains people will just keep asking IT for more and more _stuff_, which has always been the case. Bill asks where he can find the current list of _commitments_, which does not exist as far as Wes knows. 

He calls Patty telling her he will need her help to figure what all the current work commitments are. Patty informs him there is no such list and it is somewhat scattered across departments, and some work is actually not even documented at all.

> At the very least, get me the work estimate to fix the audit findings. Then, for each of those resources, tell me what their other commitments are that we're going to be pulling them off of. For that matter, do the same thing for every person assigned to Phoenix. I'm guessing we're overloaded.

Patty approves Bill..

> We should have done this a long time ago. We bump up the priorities of things all the time, but we never really know what just got bumped down.

..and informs him that she will start right away.

### Simplifying the Change Management System
Bill, Patty and Wes meet in the afternoon. There are too many projects with priotiries unknown, people are gettig interrupted with frequent outages and procedures are being overridden. 

Wes jumps in..

> We'll need to hire seven people. It'll take time to find these people and then another six to twelve months before they're fully productive.

The next meeting is the CAB meeting. There are fourteen people in the meeting this time with technical leads attending.

Purpose of the meeting is _to tighten up our change controls, and as managers and technical leads, we must figure out how_. 

Technical leads repeat the problems with the Change Management Tool, it is too cumbersome to use. Bill understands that expecting the current Change Management Tool to be used is impossible, and comes up with a very basic request:

> I want each group to write down every change they're planning, one change per index card. I want three pieces of information: who is planning the change, the system being changed, and a one-sentence summary.
>
> I've drawn a calendar on the whiteboard where we will eventually post approved changes according to their scheduled implementation.

### Definition of a Change
One of the leads holds up a card he has filled in and starts reading it:

> Execute the vendor-recommended database maintenance script on
Octave server XZ577 to fix retail store POS performance issues. This affects the order entry database and applications. We'd like to do this next Friday evening at 8:30 p.m.

to which Wes objects:

> That's not a change! That's just running a database script. If you were changing the script, then we'd have something to talk about.

where the reply is:

> No, it's definitely a change. It temporarily changes some database settings, and we don't know what production impact it could have. To me, it's just as risky as a database configuration change.

This reveals another problem, the problem that they do not even agree on what a change is and leads to some long discussion on what counts as a _change_ which is later agreed upon as follows:

__A change is any activity that is physical, logical, or virtual to applications, databases, operating systems, networks, or hardware that could impact services being delivered.__

Given the time for the meeting is up, Bill tells people to fill their index cards as soon as possible and hand them to Patty.

### Work in Progress
There is a potential new board member: Erik and he wants to meet all IT leaders.

As Bill and Erik meet, they go to one of the manufacturing plants: MRP-8 and climb to the catwalk to get a birds-eye view of the plant floor. Erik speaks:

> You can see loading docks on each side of the building. Raw materials are brought in on this side, and the finished goods leave out the other. Orders come off that printer down there. If you stand here long enough, you can actually see all the __WIP__, that's __Work In Progress__ (or 'inventory' for plant newbies), make its way toward the other side of the plant floor, where it's shipped to customers as finished goods.
>
> In the 1980s, this plant was the beneficiary of three incredible scientifically-grounded management movements. You've probably heard of them: the __Theory of Constraints__, __Lean production__ or the Toyota Production System, and __Total Quality Management__. They all agree on one thing: __WIP is the silent killer__. Therefore, one of the most critical mechanisms in the management of any plant is job and materials release. Without it, you can't control WIP.

### Prioritising Work
Bill is in a meeting with Steve:

> We are understaffed. There's no way that we can deliver everything promised. Either we need to cut down the project list, or we've got to staff up. The other big problem is that we have too many different projects competing for our attention. You've been consistent and clear that Phoenix is the most important, but we can't seem to keep resources dedicated to it.

Steve simply tells him he has to work with what he has, and must complete make progress with the Phoenix Project and all the other projects.

Bills walks into the Change Coordination Room for another CAB meeting. Almost every area of the wall is now covered in whiteboards. Index cards cover nearly every inch of the whiteboards on two of the walls.

> And now we're supposed to go through and approve all of them? This meeting was only scheduled for an hour, we would need days to go through all of these!

Since the amount of change requests are too many, and can not be each worked on, they know they have to somehow filter and manage them. They come up with the following plan:

- __High-risk changes__ must have change requests submitted, but must have authorization before being scheduled and implemented.
- For __messy middle changes__ the change submitter has the responsibility for consulting and getting approval from people affected. Once they do that, they submit their change card for us to review and approve for scheduling.
- __Low-risk changes__, changes that have been done many times before successfully are preapproved. They still need to be submitted, but they can be scheduled without the need of approval.

### Mitigating Risk by Distributing High-Risk Changes
It is three days before the Phoenix deployment an Bill finds himself in a CAB meeting where people are contributing much more with the new procedure and changes are being approved and planning is made.

They realise, too many changes are planned for Friday, which is also the deployment day for the Phoenix. People start exchanging ideas and after a while, the distribution of the cards on the change board is much more even.

### Taking Load Off of the Bottleneck
Brents name keeps coming up and Bill goes to Brents cubicle to observe him. He sees Brent is falling behind because everyone is just calling him on their will to get things done, which constantly interrupts him.

> Okay, from here on out, you’re working only on Phoenix. I'm expecting you to reject any task that anyone tries to assign you.

Brent believes that would not work..

> Our guys around here just don't seem up-to-speed with how all our systems work. In the end, they keep coming to me.

but Bill insists this will be the case. He realises this is a serious problem that must be fixed:

> We need to change the way we're managing escalations to Brent. Right now.

Bill meets Wes and Petty to share his ideas:

> Every time that we let Brent fix something that none of us can replicate, Brent gets a little smarter, and the entire system gets dumber.

### Building Technical Knowledge Across the Organization
They come up with the following plan:

- A resource pool of level 3 engineers will be created.
- The level 3s will be responsible for resolving all incidents.
- The level 3s will be the only people who can get access to Brent.
- Brent is not allowed to fix the escalations himself, he must guide the 3s.
- All the fixes must be documented by the level 3s upon resolving an incident.

### Refusing Work
Patty realizes a lot of changes are not getting done and the change request cards are piling up. Changes go in but never come out.

> But changes need to get done, right? That’s why they’re changes. But when was the question ever asked whether we should accept the
work? And on what basis did we ever make that decision?

### Quality Assurance
Two hours after the Phoenix deployment was scheduled to start, and things are not looking good.

Wes speaks first:

> We are getting incomplete releases from the developers. We still don’t know how to configure the test environment so that Phoenix actually comes up cleanly.

and William follows Wes:

> The code is changing so fast that we’re having problems keeping up. Phoenix is going to blow up in production. I’ve talked with Chris a couple of times about stopping the release, but he ran right over me. 
>
> When we find problems in our testing, we send it back to Development to have them fix it. Then they’ll send back a new release. The problem is that it takes about a half hour to get everything set up and running.

Convinced with the fact that this deployment will not bring any good, Bill contacts Steve to inform he wants to stop the deployment at this time. His request is not approved:

> It sounds bad, but at this point, we don’t have a choice. We have to keep going.

After a very long night, things go as expected, the deployment becomes a big failure..

- POS Systems in stores are down and all sales are being processed by manual credit card imprint machines.
- Customers using the Phoenix website are complaining about how it is either down or so slow as to be unusable. 
- The Phoenix website is leaking customer credit card numbers and losing transactions.

### Too Much Pressure
A few days after yet another SEV 1 Outage occurs and Bill insists they follow the procedure they have where other engineers must handle the situation. Steve does not like this and gets in an argument with Bill and applies pressure to just involve Brent to get things done as soon as possible.

Bill tells Steve to accept his resignation. 

## Key Learnings
- People tend not to use the processes they find cumbersome.
- The main objective of planning work is to get the left and right hands to know what the other is doing which should give some situational awareness during outages.
  - Work Planned and Work in Progress must be visible across the teams.
- Being able to take needless work out of the system is more important than being able to put more work into the system.
- When there is no clear, unified decision made by the business as a whole as to what work is a priority, company resources are innevitably wasted switching between all the Work In Progress depending on who is screaming the loudest. [Source](https://patrobinson.github.io/2015/09/01/wip-is-the-silent-killer/)
- There must be enough time for Quality Assurance planned, and approval from QA is a must for production deployment. 
- QA must be able to stop the release if found necessary.
- Being able to start a test environment must be a fast, automated process.

### Four Types Of Work
- Business Projects
- Internal IT Projects
- Changes
- Unplanned Work
  - Also known as: Firefighting
  - Unplanned Work is the worst form of work: in the presence of unplanned work, all planned work gets delayed.
  - It is important to know where your unplanned work is coming from.

### Three Ways
#### The First Way
Helps us understand how to create fast flow of work as it moves from Development into IT Operations, because that's what's between the business and the customer.

Visualising work is very important for creating a fast flow of work. Index cards on a kanban board is a very good mechanism to do this. It lets everyone see the Work In Progress.

#### The Second Way
The Second Way shows us how to shorten and amplify feedback loops, so we can fix quality at the source and avoid rework. Sources of unplanned work must be removed.

#### The Third Way 
The Third Way shows us how to create a culture that simultaneously fosters experimentation, learning from failure, and understanding that repetition and practice are the prerequisites to mastery.

## References
- [Gene Kim Defines the 3 Ways of The Phoenix Project](https://www.youtube.com/watch?v=nUOXDEvplRc)