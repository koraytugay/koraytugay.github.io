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

__John Pesche__
- Chief Information Security Officer

__Laura Beck__
- VP in charge of HR

__Kirsten Fingle__
- Runs the Project Management Office

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

__Stacey__
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

__NOC__
- Found within Building 7
- Network Operations Center
- A large area displaying the status of all the various IT services on large monitors

## Chapter 1
Parts Unlimited is rapidly falling behind the competition in terms of sales and constantly losing market share. The main problem is constantly being late to the market. 

> I hear a commercial from our largest competitor. They are talking about their customer service and the new service they are offering that allows customers to customize their cars online.
>
> How do they keep bringing such incredible new capabilities to market while we remain stuck in the mud?

Upon the current holder is parted away from the company, Bill gets _tricked_ by Steve Masters, into taking the VP of IT Operations role. He learns that he will be reporting to Steve directly from now on.

> Phoenix Project is essential to closing the gap with the competiton. Customer needs to be able to buy from us from wherever they want, whether it's on the Internet or in our retail stores.
>
> We are years late delivering.
> 
> I need Chris and you focused on Phoenix execution. I want IT systems to be reliable and dependable and want disruptions to normal operations minimal so that business can focus on getting Phoenix done.

The very same day he sits in his new chair, he is handed a message from the Dick Landrey informing him that the payroll system is broken, and he only has until the end of the day to solve it, otherwise employees might not get their paychecks on time.

#### Chapter Notes
- The question Bill asks to Steve Masters when he is appointed is notable

> What do you want most from me? And what don’t you want?”

- So is the answer he receives

> I want the IT systems to be reliable and available, and for the business to be able to depend upon them. I want disruptions to normal operations kept to an absolute minimum so that the business can focus on getting Phoenix done.

## Chapter 2
Bill starts investigating the situation and learns the how the financial systems get the data from Ann. Ann tells him that it is a pretty fragile process and adds the information that there is a lot of manual modifications are done to the incoming data. 

> I wince. I don’t like finance personnel manually changing payroll data outside the payroll application. It’s error-prone and dangerous.

Bill is surprised to learn that all the salaried employees data are ok but the hourly ones were all zero. He then goes to NOC to find Wes and Patty to find them talking about the SAN, which surprises him. He also overhears some engineers talking about _an unsuccessful update to SAN_.

Bill, Patty and Wes go to Patty 's office to further discuss where Wes informs Bill that payroll system broke when they were doing a SAN firmware upgrade when the payroll failed. This makes Brent believe the cause to be data, and suggest a rollback, but the rollback attempt bricks the SAN. Now the situation is, SAN does not boot, does not serve data and even the error messages can not be read.

Brent was actually pulled out from the work he was doing for Sarah, where he was already pulled out from his planned work. Bill is hesitant to believe the root cause is SAN being bricked, he claims there would have been more wide-spread outages if it was. Wes suggest them to go talk to Brent.

#### Chapter Notes
- There exists an error-prone, dangerous manual data manipulation done by the Operations
- No one has an idea what else is doing
  - This makes identifying the problems difficult
- People keep dropping on what they are working on for unplanned work
  - People end up dropping unplanned work for incidents

## Chapter 3
They walk past NOC to find Brent working in his cubicle, busy on the SAN issue. Brent informs them they tried an update to SAN the day before and upon reboot there were some tests failing. After aroung 15 minutes they started receiving emails the payroll system was broken, which made Brent link the update failure to payroll system failure. Panicking, they try rolling back the update, which ends up bricking SAN and taking several databases down.

During this discussion, Ann call Bill and informs him that only a single column in the incoming data was corrupt with unidentifiable characters. Bill argues the cause of this problem does not sound like a SAN failure and asks Brent what else was going on yesterday. 

> “Well, now that you mention it... A developer for the timekeeping application called me yesterday with a strange question about the database table structure. I was in the middle of working on that Phoenix test VM, so I gave him a really quick answer so I could get back to work. You don’t suppose he did something to break the app, do you?”

Following this trail, Wes finds it was a developer called Max, who was out for a vacation starting today, who was doing some updates to the time keeping applications database for a project driven by John.

Patty complains John and his team never follows Patty's procedures. John informs them on the phone that they did made an update related to a security law. Patty gets furious on John how he does not follow to procedures and causes problems, which they end up cleaning up. John replies her that the procedures take too much time and no work would get done had they followed.

Finally Bill asks John whether they have tested the change before applying it to which he gets the response

> No, we could not test the change. There’s no test environment.

After hanging up, Patty repeats her point

> We need more process around here and better support from the top, including IT process tooling and training. Everyone thinks that the real way to get work done is to just do it. That makes my job nearly impossible.

Bill reinforces this point

> No one made changes without telling everyone else, and we’d bend over backward to make sure our changes wouldn’t screw someone else up.

Bill also learns from Patty that people are not even using the ticketing system they have to log what changes they make. Bill finds this unacceptable and tells Patty to inform every department manager to attend Change Advisory Board meetings starting tomorrow.

They finally solve the problem at 7 p.m., when it is too late.

#### Chapter Notes
- There is no test environment
- People do not use the procedures finding them too cumbersome
  - They claim no work would get done following all the process
- People do not inform the team on changes
- There is no way to track what has been done recently
- Departments see each other as foes instead of allies, blaming each other

## Chapter 4
It is September 3rd and Bill is in his office at 7:30 a.m. He seens an email from Sarah putting pressure on him where Sarah reminds Bill that Phoenix Project is most important and he is not supposed to be holding releases back. She invites him to an emergency meeting at 10:00 a.m. same day.

Bill and Wes gets into a phone call where Wes comments about the e-mail

> I’m pretty sure it’s about Brent not finishing up that configuration work for the Phoenix developers. Everyone is chasing their tails because the developers can’t actually tell us what the test environment should look like. We’re doing our best, but every time we deliver something, they tell us we did it wrong.

At 10:00 a.m. Bill goes to the meeting to see that pretty much everyone is there in the meeting, including Steve. Kirsten kicks off the meeting..

> First on our agenda is Phoenix. To refresh your memory, last week there were twelve tasks in the critical path of Phoenix Phase 1. Only three of those tasks were completed.

Steve asks for an explanation from Bill for this, to which Bill responds

> The critical resource in question is Brent, who has been one hundred percent utilized helping to recover from the payroll failure, which we all know about. This was a totally unforeseen emergency but obviously one that we had to handle. Everyone knows how important Phoenix is, and we are doing everything we can to make sure Brent can stay focused.

Sarah puts pressure on Bill and Chris during the meeting, expecting to get an answer to the question

> When is the soonest we can go live?

to which Chris responds

> We can go into production one week from Friday.

Bill does not hear liking this answer since Chris is forced to spit out a date and so he does without considering all the things that the Operations team must do before going live by answering from developers perspective only. Wes responds with several arguments

> Your guys still haven’t told us what sort of infrastructure we need, so we can’t even order the necessary server and networking gear.
>
> You’re supposed to support 250 transactions per second, and you’re barely doing even four! We’re going to need so much hardware.
>
> We still don’t have a concrete specification of how the production and test systems should be configured. 
>
> Oh, do you guys not need a test environment anymore? You haven’t even done any real testing of your code yet, because that fell off the schedule, too!

The argument between Sarah, Chris, Bill and Wes keeps going, focused on Sarah pressuring the rollout of Phoenix to be done and not delayed anymore no matter what, where Bill and Wes argue it might be a catastroph if done so. Steve hears what he wants to hear, and appricates the risks but demands the rollout to be done by next week, which Bill finds unreasonable and too risky.

Walking out of the meeting, Wes and Bill are on the same page that there will be big trouble ahead. Bill asks Wes why they do not attend meetings held by Chris 's team, to which he responds

> We did get notice on a couple of the big planning meetings. One of the most critical people who needed to be there, Brent, wasn’t able to make it, due to escalations. 
>
> He’s the guy we need at those meetings to tell those idiotic developers how things work in the real world and what type of things keep breaking in production. The irony, of course, is that he can’t tell the developers, because he’s too busy repairing the things that are already broken.

Bill goes to Patty 's Change Management Meeting in the afternoon, to see an empty room, only attendee being Patty herself. Bill asks Patty what the reason behind this is, to which she replies

>  We brought in some consultants, who helped us replace our ticketing system with an ITIL-compliant change management tool. People were supposed to put change requests into it, where it would get routed for approvals. But, even after two years, all we have is a great process on paper that no one follows and a tool that no one uses. When I pester people to use them, all I get are complaints and excuses.

Bill assures Patty that the change management system is very impoirtant and this has to change. He asks her to schedule another meeting for Friday and inform everyone that attandence is mandatory. 

Following all this Bill sends a strong e-mail, noting

> Effective immediately, managers (or their assigned delegates) are required to attend all scheduled CAB meetings and to perform their assigned duties. We are resurrecting the Parts Unlimited change management process and it will be followed to the letter.

A few minutes later Wes calls Bills, explaining why the change management process is not popular within IT..

>  No one, and I mean absolutely no one, could get a single thing done. It was absolutely ridiculous and a total waste of time.
>
> That software application she made us use is a total piece of crap. It takes twenty minutes to fill out all those fields for a simple five-minute change!

Bill summarises the situation to himself when the call is over..

> Before, I was merely worried that IT Operations was under attack by Development, Information Security, Audit, and the business. Now, I’m starting to realize that my primary managers seem to be at war with each other, as well.

#### Chapter Notes
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
