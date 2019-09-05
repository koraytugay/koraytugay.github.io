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

## Chapter 1
Parts Unlimited is rapidly falling behind the competition in terms of sales. The main problem is constantly being late to the market.

> I hear a commercial from our largest competitor. They are talking about the new service they are offering. How do they keep bringing such incredible new capabilities to market while we remain stuck in the mud?

Bill gets promoted to the VP of IT Operations role, and he will be reporting to Steve directly from now on. During his conversation with Steve, he learns:

> Phoenix Project is essential to closing the gap with the competiton. Customer needs to be able to buy from us from wherever they want, whether it's on the Internet or in our retail stores.
>
> We are __years late delivering__.
> 
> I need Chris and you focused on Phoenix execution.

The very same day he sits in his new chair, he is handed a message from Dick informing him that the payroll system is broken, and he only has until the end of the day to fix it.

__Notes__
- The question Bill asks to Steve Masters when he is appointed is notable

> What do you want most from me? And what don't you want?”

- So is the answer he receives

> I want the IT systems to be reliable and available, and for the business to be able to depend upon them. I want disruptions to normal operations kept to an absolute minimum so that the business can focus on getting Phoenix done.

## Chapter 2
Bill starts investigating and learns how the financial systems get the data. It is a pretty fragile process and there is a lot of manual modifications are done to the incoming data. 

> I don't like finance personnel manually changing payroll data outside the payroll application. It's error-prone and dangerous.

He then goes to NOC to find Wes and Patty and overhears some engineers talking about _an unsuccessful update to SAN_ which he does not like.

Wes, Patty and Bill starts discussing and Wes informs Bill that payroll system broke when they were doing a SAN firmware upgrade. As they attempted rollback on the upgrade, SAN got bricked. Now the situation is, SAN does not boot, does not serve data and even the error messages can not be read.

Bill is hesitant to believe the root cause is SAN being bricked, he claims 
there would have been more wide-spread outages if it was. Wes suggest them to go talk to Brent, the guy who was doing the upgrade.

__Notes__
- There exists an error-prone, dangerous manual data manipulation done by the Operations
- No one has an idea what else is doing
- This makes identifying the problems difficult

## Chapter 3
They find Brent working in his cubicle, busy on the SAN issue. Brent informs them they tried an update to SAN the day before and upon reboot there were some tests failing. After around 15 minutes payroll system was broken, which made them link the update failure to payroll system failure. Panicking, they try rolling back the update, which ends up bricking SAN and taking several databases down.

Bill argues the cause of this problem does not sound like a SAN failure and asks Brent what else was going on yesterday. 

> Well, now that you mention it... A developer for the timekeeping application called me yesterday with a strange question about the database table structure. I was in the middle of working on something, so I gave him a quick answer.

Following the trail, Wes finds it was a developer called Max, who was doing updates to the time keeping applications database for a project driven by John.

Patty complains John and his team never follows the Change Management Process. John informs them they did made an update related to law and tells that the procedures take too much time and no work would get done had they followed.

Bill asks John whether they have tested the change before applying it to which he gets the response

> No, we could not test the change. There's no test environment.

Bill learns from Patty that people are not using the ticketing system where they are supposed to log what changes they made. Bill finds this unacceptable and tells Patty to inform every department manager to attend Change Advisory Board meetings starting tomorrow.

They finally solve the problem at 7 p.m., when it is too late.

__Notes__
- There is no test environment
- People do not use the procedures finding them too cumbersome
  - They claim no work would get done following all the process
- People do not inform the team on changes
- There is no way to track what has been done recently
- Departments see each other as foes instead of allies, blaming each other

## Chapter 4
It is a new day and Bill is in office to see an email from Sarah putting pressure on him, inviting him to an emergency meeting. Bill and Wes gets into a phone call where Wes comments about the e-mail

> I'm pretty sure it's about Brent not finishing up that configuration work for the Phoenix developers. Everyone is chasing their tails because the developers can't actually tell us what the test environment should look like. We're doing our best, but every time we deliver something, they tell us we did it wrong.

Sarah takes the lead in the meeting

> First on our agenda is Phoenix. To refresh your memory, last week there were twelve tasks in the critical path of Phoenix Phase 1. Only three of those tasks were completed.

Steve asks for an explanation from Bill for this, to which Bill responds

> The critical resource in question is Brent, who has been one hundred percent utilized helping to recover from the payroll failure. This was an emergency that we had to handle. We are doing everything we can to make sure Brent can stay focused.

Sarah puts pressure on Bill and Chris during the meeting, expecting to get an answer to the question

> When is the soonest we can go live?

to which Chris responds

> We can go into production one week from Friday.

Bill does not hear liking this answer since Chris is forced to spit out a date and so he does without considering all the things that the Operations team must do before going live by answering from developers perspective only. Wes responds with several arguments

> Your guys still haven't told us what sort of infrastructure we need, so we can't even order the necessary server and networking gear.
>
> You're supposed to support 250 transactions per second, and you're barely doing even four! We're going to need so much hardware.
>
> We still don't have a concrete specification of how the production and test systems should be configured. 
>
> Oh, do you guys not need a test environment anymore? You haven't even done any real testing of your code yet, because that fell off the schedule, too!

The argument keeps going, focused on Sarah pressuring the rollout of Phoenix to be done and not delayed anymore no matter what, Steve hears what he wants to hear, appricates the risks but demands the rollout to be done by next week, which Bill finds too risky.

Walking out of the meeting, Wes and Bill are on the same page that there will be big trouble ahead.

Bill goes to Patty 's Change Management Meeting in the afternoon, to see an empty room, only attendee being Patty herself. Patty explains

>  We brought in some consultants, who helped us replace our ticketing system with a change management tool. People were supposed to put change requests into it, where it would get routed for approvals. All we have is a process on paper that no one follows and a tool that no one uses. When I pester people to use them, all I get are complaints and excuses.

Bill assures Patty that the change management system is very important and this has to change. He asks her to schedule another meeting informing everyone that attandence is mandatory. 

Following all this Bill broadcasts via e-mail:

> Effective immediately, managers are required to attend all scheduled CAB meetings and to perform their assigned duties.

A few minutes later Wes calls Bills, explaining why the change management process is not popular within IT..

>  No one could get a single thing done. It was ridiculous and a waste of time.
>
> That software application she made us use is a total piece of crap. It takes twenty minutes to fill out all those fields for a simple five-minute change!

__Notes__
- There is a clear problem between the Development team and the Operations team in handoffs
  - Operations claim Developers do not think about deployment or testing until the very last moment
- The whole IT seems to be dependent on a single engineer
- Promises are made to people without considering all the parties involved in IT
- There is barely any communication between teams and instead of working together people seem to be pointing fingers
- Development is taking up all the schedule leaving testing, deployment and configuration little to no time
- People do not use the tools and follow procedures
  - They believe it is too cumbersome and too slow to do so
  - They claim no work can get done with such heavy procedures

## Chapter 5
Bill starts yet another day with an email informing him on another urgent matter that must be fixed as soon as possible.

> We just concluded our Q3 internal audit for the upcoming SOX-404 external audit. We discovered concerning deficiencies. Due to the urgency of the findings, we need to meet with IT this morning. 
>
> Nancy

Bill finds the meeting room and sees that Nancy, John and Wes are present, together with Tim, the IT auditor. The news Bill hear is not pleasent.

> We found 952 IT general control deficiencies, of which 16 are significant.  You need to investigate each of these findings, confirm them, and then create a remediation plan. We only have three weeks until the external auditors arrive. We require your response by one week from Monday.

Bill is now wondering where he is going to find resources for it.

Wes and Bill starts a conversation after the meeting, which leads to Wes commenting they would need Brent for a bunch of the items. Bill is not happy with hearing this.

> Brent. Brent, Brent, Brent! Can't we do anything without him? Look at us! I don't care how talented he is. If you're telling me that our organization can't do anything without him, we've got a big problem.

Wes, admits in an embarrassed way that Brent may know more about how the company works compared to him, which Bill does not like either.

> You're a senior manager. This should be as unacceptable to you as it is to me!

As the discussion goes, Wes complains people will just keep asking IT for more and more _stuff_, which has always been the case. Bill asks where he can find the current list of _commitments_, which does not exist as far as Wes knows. 

He calls Patty telling her he will need her help to figure what all the current work commitments are, so that he can talk about resourcing to Steve later. Patty informs him that there is no such list and it is somewhat scattered across departments and people, and some work is actually not even documented and done by people going to their favorite IT guy and for getting done as quickly as possible.

Bill then decides the best he can get at the moment can be finding out all the commitments the people work on audit findings and Phoenix are. 

> At the very least, get me the work estimate to fix the audit findings. Then, for each of those resources, tell me what their other commitments are that we're going to be pulling them off of. For that matter, do the same thing for every person assigned to Phoenix. I'm guessing we're overloaded, so I want to know by how much. I want to proactively tell people whose projects have been bumped, so they're not surprised when we don't deliver what we promised.

Patty approves Bill..

> We should have done this a long time ago. We bump up the priorities of things all the time, but we never really know what just got bumped down.

..and informs him that she will start right away.

__Notes__
- Urgent matters (mostly caused by tech debt) never let people be productive
- People are not prioritising their tasks correctly
- There is no team work and people are spending time fighting with each other
- A lot of tasks depend on one particular person
- Demand, priorities, status of work in progress, and available resources are not known, not visible by any means
  - No one knows what gets bumped down, when something gets bumped up 
  - People reach IT staff directly to get things done, by-passing any documentation or project tracking

## Chapter 6
Bill, Patty and Wes meet in the afternoon and Patty shares her findings: There are too many projects with priotiries unknown, people are gettig interrupted with frequent outages and procedures are being overridden. There also exists the problem of IT people not being able to get the work done that is in their hands.

Wes jumps in..

> We'll probably need to hire seven people. It'll take time to find these people and then another six to twelve months before they're fully productive.

The next meeting is the CAB meeting. There are fourteen people in the meeting this time, most of the technical leads are attending.

Bill summarises the purpose of the meeting: _We need to tighten up our change controls, and as managers and technical leads, we must figure out how_. 

Technical leads repeat the problems with the Change Management Tool, it is too cumbersome to use.

> There's a million mandatory fields..
>
> To follow Patty's rules, I have to manually type in hundreds of server names in one of the text boxes..
>
> When my guys put in change requests, they have to wait a lifetime to get approvals, let alone get on the schedule.

Bill understands that expecting the current Change Management Tool to be used is impossible, and comes up with a very basic request:

> I want each group to write down every change they're planning, one change per index card. I want three pieces of information: who is planning the change, the system being changed, and a one-sentence summary.
>
> I've drawn a calendar on the whiteboard where we will eventually post approved changes according to their scheduled implementation.

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

__Notes__
- It is expected from a new hire to be fully productive between six to twelve months.
- The Change Management Tool is not user-friendly.
  - A light-weight procedure is embraced by people.
- Terms (such as _change_) do not have agreed on meanings.

## Chapter 7
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
>
> Decades ago, there used to be a guy named Mark. He was the supervisor for that first work center, right down there by that desk. One day I see Mark picking up a folder to start some job. I ask him, ‘On what basis did you choose that job, versus any of the others? e says, ‘It's a job that requires this work center first. And we're open.' I could hardly believe it. I tell him, ‘Your station is just the first of twenty operations. You don't factor the availability of any of the other nineteen stations in your decision?' And
he replies, ‘Well, no. This is the way I've done it for twenty years.'
>
> But of course, now everyone knows that you don't release work based on the availability of the first station. Instead, it should be based on the tempo of how quickly the bottleneck resource can consume the work.
>
> Theory of Constraints showed us how any improvements made anywhere besides the bottleneck are an illusion. Any improvement made after the bottleneck is useless, because it will always remain starved, waiting for work from the bottleneck.
>
> __Your job as VP of IT Operations__ is to ensure the fast, predictable, and uninterrupted flow of planned work that delivers value to the business while minimizing the impact and disruption of unplanned work, so you can provide stable, predictable, and secure IT service.

He then summarises the __Three Ways__ and tells Bill that he must understand them well:
- __The First Way__ helps us understand how to create fast flow of work as it moves from Development into IT Operations, because that's what's between the business and the customer.
- __The Second Way__ shows us how to shorten and amplify feedback loops, so we can fix quality at the source and avoid rework.
- __The Third Way__ shows us how to create a culture that simultaneously fosters experimentation, learning from failure, and understanding that repetition and practice are the prerequisites to mastery.

He tells Bill that there are four types of work, and he must find out what the other three are, next to the one he already identified: Business project work.

__Notes__
- One must be able to see the work to be able to organize, sequence and have assurance that available resources can complete it.
- Any improvements made anywhere besides the bottleneck are an illusion.
- There are four types of work, and only one is revealed in this chapter:
  - Business Project

#### WIP is the silent killer
I am not sure if I understand what is meant by this. Pat Robinson has a summary of this sentence [here](https://patrobinson.github.io/2015/09/01/wip-is-the-silent-killer/).

> When there is no clear, unified decision made by the business as a whole as to what work is a priority, company resources are innevitably wasted switching between all the Work In Progress (WIP) depending on who is screaming the loudest. 

Another explanation I found is as follows:

> The quantity of work-in-progress will directly affect cycle-time, the more work you are currently undertaking the greater the required cycle-time to complete each task.

The explanation I would make would be someting like this: As long as you are not able to finish tasks in your hand, but still accepting new work, you are not making any overall progress. Work In Progress is both the source of product, but in the same time, is the cause of not being able to deliver anything, if not managed properly.

## Chapter 8
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

## Chapter 9
Another SEV 1 Outage occurs and Brent fixes the outage without informing anyone, which Bill does not like.

> We need to get everyone used to solving problems in a methodical way.

Phoenix deployment is now only three days away and things are not looking good. Bill finds himself in a CAB meeting where people are contributing much more with the new procedure and changes are being approved and planning is made.

They realise, too many changes are planned for Friday, which is also the deployment day for the Phoenix. People start exchanging ideas and after a while, the distribution of the cards on the change board is much more even.

Looking at the board, Bill realises the other two categories of work Erik had mentioned.

> Wes and Patty did a manual inventory of all our projects, coming up with nearly a hundred projects. Those projects certainly represent two categories of work: business projects and internal IT projects. I realize that changes are the third category of work.
>
> Thinking for a moment, I decide there's value in knowing that changes represent yet another category of work but don't know why.

__Notes__
- Individual engineers are making changes without informing others and following procedures to get things done.
- The simpler Change Management Process is embraced by everyone and is much more effective.
- Too many changes on a given day is not welcome, but it was only apparent by following the system.
- The three types of work are:
  - Business Projects
  - Internal IT Projects
  - Changes


## References
- [Gene Kim Defines the 3 Ways of The Phoenix Project](https://www.youtube.com/watch?v=nUOXDEvplRc)
