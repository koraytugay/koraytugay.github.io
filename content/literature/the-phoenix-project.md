---
layout: default
title:  "The Phoenix Project"
---

# The Phoenix Project
{:.no_toc}

* TOC
{:toc}

## Characters
__Ann__
- Operations Manager

__Bill Palmer__
- Former Director of Midrange Technology Operations
- VP of IT Operations

__Brent__
- Highly skilled engineer
- Has know-how on many applications and systems
- One of the few people who understand how all the applications talk together

__Bob Strauss__
- Steve 's old boss
- Chairman of the company

__Chris Allers__
- VP of Application Development
- Responsible for developing applications
- Life currently dominated by Phoenix
- Reports to Steve Masters
- Manages nearly two hundred developers
- Constantly being asked to deliver more features with less money

__Dick Landrey__
- CFO

__Erik__
- Technology hotshot
- Potential board member

__John Pesche__
- Chief Information Security Officer
- Looks to be in mid-thirties with thick, curly black hair

__Laura Beck__
- VP in charge of HR

__Kirsten Fingle__
- Runs the Project Management Office

__Nancy Mailer__
- Chief Audit Executive
- Smart and formidable

__Patty__
- Director of IT Service Support
- Owns all the level 1 and 2 help desk technicians
- Owns the key processes and tools that the entire IT Operations organization relies upon
  - Trouble ticketing system
  - Monitoring
  - Running change management meetings
- Thoughtful, analytical
- Stickler for processes and procedures
- Does all the scheduling and hands the people to do the work

__Sarah Moulton__
- SVP of Retail Operations
- Blames other people for screwups

__Stacy__
- Steve 's assistant
- 40 years old
- Very organized

__Steve Masters__
- CEO
- Acting CIO

__Wes__
- Director of Distributed Technology Operations
- Responsible for 
  - over a thousand Windows servers
  - for the database and networking teams
- Loud, outspoken

__William Mason__
- Director of Quality Assurance
- Works for Chris

## Acronyms
__CAB__
- Change Advisory Board
- Lead by Patty
- Responsible for Change Management Process

__CMM__
- Change Management Meeting
- Where a team coordinates and organizes all their work to make sure they do not shoot ourselves in the foot

__ITIL__
- IT Infrastructure Library
- Documents many IT best practices and processes

__Phoenix Project__
- A Project long overdue and over budget that must go live very soon
- Cruicial for the future of the company

__SOX-404__
- Sarbanes-Oxley Act of 2002
- Financial auditing

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

__Building 2__
- Tallest building on the campus
- Houses Steve 's office

__Building 5__
- Where Laura and her staff reside
- Nicely furnished

__Building 7__
- Ghetto of the Parts Unlimited

__Building 9__
- Where the Marketing department is at

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

Bill gets promoted to the VP of IT Operations role, and he will be reporting to Steve directly from now on. During his conversation with Steve, he learns:

> Phoenix Project is essential to closing the gap with the competiton. Customer needs to be able to buy from us from wherever they want, whether it's on the Internet or in our retail stores.
>
> We are __years late delivering__.

The very same day payroll system is broken, and he only has until the end of the day to fix it.

### Untracable Work
Bill starts investigating and learns how the financial systems get the data. He goes to Wes and Patty and overhears some engineers talking about _an unsuccessful update to SAN_.

Wes tells that payroll system broke when they were doing a SAN firmware upgrade. As they attempted rollback on the upgrade, SAN got bricked. Now the situation is, SAN does not even boot.

Bill is hesitant to believe the root cause is SAN being bricked, he claims 
there would have been more wide-spread outages if it was. They decide to go talk to Brent, the guy who was doing the upgrade.

They find Brent working busy on the SAN issue. Brent informs them they tried an update to SAN the day before and upon reboot there were some tests failing. After around 15 minutes payroll system was broken, which made them link the update failure to payroll system failure. Panicking, they try rolling back the update, which ends up bricking SAN and taking several databases down.

Bill argues the cause of this problem does not sound like a SAN failure and asks Brent what else was going on yesterday. 

> A developer for the timekeeping application called me yesterday with a strange question about the database table structure. I was in the middle of working on something, so I gave him a quick answer.

Following the trail, Wes finds it was a developer, who was doing updates to the time keeping applications database for a project driven by John.

### The Absence of Test Environment
They contact John and ask whether they have tested the change before applying it to which he gets the response:

> No, we could not test the change. There's no test environment.

They finally solve the problem at 7 p.m., when it is too late.

### The Lack of Communication
It is a new day and Bill is in office to see an email from Sarah putting pressure on him, inviting him to an emergency meeting. Bill and Wes gets into a phone call where Wes comments about the e-mail:

> I'm pretty sure it's about Brent not finishing up that configuration work for the Phoenix developers. Everyone is chasing their tails because the developers can't actually tell us what the test environment should look like. 

Sarah takes the lead in the meeting:

> First on our agenda is Phoenix. Last week there were twelve tasks in the critical path of Phoenix Phase 1. Only three of those tasks were completed.

Steve asks for an explanation from Bill for this, to which Bill responds:

> The critical resource in question is Brent, who has been one hundred percent utilized helping to recover from the payroll failure. This was an emergency that we had to handle.

Sarah puts pressure on Bill and Chris during the meeting, expecting to get an answer to the question:

> When is the soonest we can go live?

to which Chris responds:

> We can go into production one week from Friday.

Chris is forced to spit out a date and so he does without considering all the things that the Operations team must do before going live by answering from developers perspective only. Wes responds with several arguments:

> Your guys still haven't told us what sort of infrastructure we need, so we can't even order the necessary server and networking gear.
>
> You're supposed to support 250 transactions per second, and you're barely doing even four! We're going to need so much hardware.
>
> We still don't have a concrete specification of how the production and test systems should be configured. 
>
> Oh, do you guys not need a test environment anymore? You haven't even done any real testing of your code yet, because that fell off the schedule, too!

Steve appricates the risks and demands the rollout to be done by next week.

Walking out of the meeting, Wes and Bill are on the same page that there will be big trouble ahead.

### Processes Not Being Followed
Bill goes to Patty 's Change Management Meeting in the afternoon, to see an empty room. Patty explains:

> All we have is a process on paper that no one follows and a tool that no one uses. When I pester people to use them, all I get are complaints and excuses.

Bill assures Patty that the change management system is very important and this has to change. He asks her to schedule another meeting informing everyone that attandence is mandatory. 

Following all this Bill broadcasts via e-mail:

> Effective immediately, managers are required to attend all scheduled CAB meetings and to perform their assigned duties.

A few minutes later Wes calls Bills, explaining why the change management process is not popular within IT:

>  No one could get a single thing done. It was ridiculous and a waste of time.
>
> That software application she made us use is a total piece of crap. It takes twenty minutes to fill out all those fields for a simple five-minute change!

### Unplanned Work
Bill starts yet another day with an email informing him on another urgent matter that must be fixed as soon as possible.

> We just concluded our Q3 internal audit for the upcoming SOX-404 external audit. We discovered concerning deficiencies. Due to the urgency of the findings, we need to meet with IT this morning. 
>
> Nancy

Bill finds the meeting room and sees that Nancy, John and Wes are present, together with Tim, the IT auditor.

> We found 952 IT general control deficiencies, of which 16 are significant.  You need to investigate each of these findings, confirm them, and then create a remediation plan. We only have three weeks until the external auditors arrive. We require your response by one week from Monday.

Bill is now wondering where he is going to find resources for it.

### Constraints
Wes and Bill starts a conversation after the meeting, which leads to Wes commenting they would need Brent for a bunch of the items. Bill is not happy with hearing this.

> Brent. Brent, Brent, Brent! Can't we do anything without him? Look at us! I don't care how talented he is. If you're telling me that our organization can't do anything without him, we've got a big problem.

Wes, admits in an embarrassed way that Brent may know more about how the company works compared to him, which Bill does not like either.

> You're a senior manager. This should be as unacceptable to you as it is to me!

### Planning Work
As the discussion goes, Wes complains people will just keep asking IT for more and more _stuff_, which has always been the case. Bill asks where he can find the current list of _commitments_, which does not exist as far as Wes knows. 

He calls Patty telling her he will need her help to figure what all the current work commitments are, so that he can talk about resourcing to Steve later. Patty informs him that there is no such list and it is somewhat scattered across departments and people, and some work is actually not even documented and done by people going to their favorite IT guy and for getting done as quickly as possible.

Bill then decides the best he can get at the moment can be finding out all the commitments the people work on audit findings and Phoenix are. 

> At the very least, get me the work estimate to fix the audit findings. Then, for each of those resources, tell me what their other commitments are that we're going to be pulling them off of. For that matter, do the same thing for every person assigned to Phoenix. I'm guessing we're overloaded, so I want to know by how much. I want to proactively tell people whose projects have been bumped, so they're not surprised when we don't deliver what we promised.

Patty approves Bill..

> We should have done this a long time ago. We bump up the priorities of things all the time, but we never really know what just got bumped down.

..and informs him that she will start right away.

### Simplifying the Change Management System
Bill, Patty and Wes meet in the afternoon and Patty shares her findings: There are too many projects with priotiries unknown, people are gettig interrupted with frequent outages and procedures are being overridden. There also exists the problem of IT people not being able to get the work done that is in their hands.

Wes jumps in..

> We'll need to hire seven people. It'll take time to find these people and then another six to twelve months before they're fully productive.

The next meeting is the CAB meeting. There are fourteen people in the meeting this time, most of the technical leads are attending.

Bill summarises the purpose of the meeting: _We need to tighten up our change controls, and as managers and technical leads, we must figure out how_. 

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

As Bill and Erik meet, they start talking about the current situation the company is in. He shares his first impressions with Bill:

> Your problem right now is that you obviously don't actually know what 'work' is.

to which Bill responds:

> We need to get Phoenix out the door. That qualifies as work in my mind.

But Erik does not find the answer complete:

> Yes, that's certainly one type of work. But you're still missing the three other types of work that IT Operations is responsible for. We're going for a ride.

They go to one of the manufacturing plants: MRP-8 and climb to the catwalk to get a birds-eye view of the plant floor. Erik speaks:

> You can see loading docks on each side of the building. Raw materials are brought in on this side, and the finished goods leave out the other. Orders come off that printer down there. If you stand here long enough, you can actually see all the __WIP__, that's __Work In Progress__ (or 'inventory' for plant newbies), make its way toward the other side of the plant floor, where it's shipped to customers as finished goods.
>
> In the 1980s, this plant was the beneficiary of three incredible scientifically-grounded management movements. You've probably heard of them: the __Theory of Constraints__, __Lean production__ or the Toyota Production System, and __Total Quality Management__. They all agree on one thing: __WIP is the silent killer__. Therefore, one of the most critical mechanisms in the management of any plant is job and materials release. Without it, you can't control WIP.


### Prioritising Work
Bill is in a meeting with Steve:

> We are definitely understaffed. There's no way that we can deliver everything we've promised. Either we need to cut down the project list, or we've got to staff up. The other big problem is that we have too many different projects competing for our attention. You've been consistent and clear that Phoenix is the most important, but we can't seem to keep resources dedicated to it.

The meeting does not go as Bill expected and Steve simply tells him he has to work with what he has, and must complete make progress with the Phoenix Project and all the other projects.

Bills walks into the Change Coordination Room for another CAB meeting. Almost every area of the wall is now covered in whiteboards. Index cards cover nearly every inch of the whiteboards on two of the walls.

> And now we're supposed to go through and approve all of them? This meeting was only scheduled for an hour, we would need days to go through all of these!

Since the amount of change requests are too many, and can not be each worked on, they know they have to somehow filter and manage them. They come up with the following plan:

- __High-risk changes__ must have change requests submitted, but must have authorization before being scheduled and implemented.
- For __messy middle changes__ the change submitter has the responsibility for consulting and getting approval from people affected. Once they do that, they submit their change card for us to review and approve for scheduling.
- __Low-risk changes__, changes that have been done many times before successfully are preapproved. They still need to be submitted, but they can be scheduled without the need of approval.

__Notes__
- The main objective with all the planning work is to get the left and right hands to know what the other is doing which should give some situational awareness during outages.

### Mitigating Risk by Distributing High-Risk Changes
Another SEV 1 Outage occurs and Brent fixes the outage without informing anyone, which Bill does not like.

> We need to get everyone used to solving problems in a methodical way.

Phoenix deployment is now only three days away and things are not looking good. Bill finds himself in a CAB meeting where people are contributing much more with the new procedure and changes are being approved and planning is made.

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
7:30 p.m. on Friday, two hours after the Phoenix deployment was scheduled to start, and things are not looking good.

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
A few days after yet another SEV 1 Outage occurs, and instead of appointing Brent to the situation, Bill insists they follow the procedure they have where other engineers must handle the situation. Steve does not like this and gets in an argument with Bill and applies pressure on him to not follow these processes and just does whatever is needed to get things fixed as soon as possible.

Bill finally decides there is nothing to do here, and tells Steve to accept his resignation. 

## Key Learnings
- People tend not to use the processes they find cumbersome.
- Work Planned and Work in Progress must be visible across the teams.
- Being able to take needless work out of the system is more important than being able to put more work into the system.
- When there is no clear, unified decision made by the business as a whole as to what work is a priority, company resources are innevitably wasted switching between all the Work In Progress depending on who is screaming the loudest. [Source](https://patrobinson.github.io/2015/09/01/wip-is-the-silent-killer/)
- There must be enough time for Quality Assurance planned, and approval from QA is a must for production deployment. QA must be able to stop the release if found necessary.
- Being able to start a test environment and do a smoke test must be automated and a quick process.

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