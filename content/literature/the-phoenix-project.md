---
layout: default
title:  "The Phoenix Project"
---

# The Phoenix Project
{:.no_toc}

* TOC
{:toc}

## Summary
### Falling Behind
Parts Unlimited is rapidly falling behind the competition in terms of sales. The main problem is constantly being late to the market.

> I hear a commercial from our largest competitor. They are talking about the new service they are offering. How do they keep bringing such incredible new capabilities to market while we remain stuck in the mud?

### The Phoenix Project
Bill gets promoted to the VP of IT Operations role learning he must make the Phoenix Project go live:

> Phoenix Project is essential to closing the gap with the competiton. Customer needs to be able to buy from us from wherever they want, whether it's on the Internet or in our retail stores.
>
> We are __years late delivering__.

### Untracable Work
The very same day payroll system is broken. While investigating, Bill overhears some engineers talking about _an unsuccessful update to SAN_.

Bill is hesitant to believe the root cause is SAN, he claims 
there would have been more wide-spread outages if it was. He decides to talk to Brent, the guy who was doing the upgrade.

Brent informs them they tried an update to SAN the day before and after 15 minutes payroll system was broken, which made them link the incidents. Panicking, they rolled back the update, which ended up bricking SAN taking several databases down.

Bill pushes more asking what else was going on yesterday.

> A developer for the timekeeping application called me yesterday with a question about the database structure. I was in the middle of working on something, I gave him a quick answer.

It turns out, it was a developer who was doing updates to the time keeping applications database, who broke the payroll system.

### Absence of Test Environment
Bill contatcs the responsible person asking whether they tested their change.

> No, we could not test the change. There's no test environment.

The payrol system is finally fixed, but too late.

### Unplanned Work
The next morning Bill is invited to an emergency meeting.

> First on our agenda is Phoenix. Last week there were twelve tasks in the critical path of Phoenix Phase 1. Only three of those tasks were completed.

To which Bill responds:

> The critical resource in question is Brent, who has been one hundred percent utilized helping to recover from the payroll failure. This was an emergency that we had to handle.

### Harmful Pressure
Bill and Chris are pressured to provide an answer to the question _When is the soonest we can go live?_.  Chris is forced to spit out a date and so he does without considering all the things that the Operations team must do before going live.

### Processes Not Being Followed
Bill goes to Change Management Meeting in the afternoon, to see an empty room. Patty explains:

> All we have is a process on paper that no one follows and a tool that no one uses. All I get are complaints and excuses.

Bill asks her to schedule another meeting and broadcasts an e-mail:

> Effective immediately, managers are required to attend all scheduled CAB meetings and to perform their assigned duties.

### More Unplanned Work
Bill starts yet another day with an invitation to an emergency meeting with the internal audit.

> We found 952 IT general control deficiencies, of which 16 are significant.  You need to investigate each of these findings, confirm them, and then create a remediation plan. We require your response by one week from Monday.

Bill is now wondering where he is going to find resources for it.

### Constraints
Wes and Bill starts a conversation after the meeting, which leads to Wes commenting they would need Brent for a bunch of the items. 

> Brent. Brent, Brent, Brent! Can't we do anything without him? If you're telling me that our organization can't do anything without him, we've got a big problem.

### Unkown Workload
As the discussion goes, Wes complains people will just keep asking IT for more and more _stuff_, which has always been the case. Bill asks where he can find the current list of _commitments_, which does not exist as far as Wes knows. 

He calls Patty telling her he will need her help to figure what all the current work commitments are. Patty informs him there is no such list and it is somewhat scattered across departments, and some work is actually not even documented at all.

> Get me the work estimate to fix the audit findings. For each of those resources, tell me what their other commitments are that we're going to be pulling them off of. I'm guessing we're overloaded.

### Simple Tools
The next meeting is the CAB meeting with all technical leads attending. Bill understands that expecting the current Change Management Tool to be used is impossible, and comes up with a very basic request:

> I want each group to write down every change they're planning, one change per index card. I've drawn a calendar on the whiteboard where we will eventually post approved changes according to their scheduled implementation.

### Definition of Change
This reveals another problem, the problem that they do not even agree on what a change is. What counts as a _change_ which is later agreed upon: __A change is any activity that is physical, logical, or virtual to applications, databases, operating systems, networks, or hardware that could impact services being delivered.__

### Work in Progress
Bill and Erik meet, they go to one of the manufacturing plants and climb to the catwalk to get a birds-eye view of the plant floor. Erik speaks:

> You can see loading docks on each side of the building. Raw materials are brought in on this side, and the finished goods leave out the other. Orders come off that printer down there. If you stand here long enough, you can actually see __Work In Progress__ make its way toward the other side of the plant floor, where it's shipped to customers as finished goods. __WIP is the silent killer__.

### Risk Management
Bills attends yet another CAB meeting. Almost every area of the wall is now covered in whiteboards. Index cards cover nearly every inch of the whiteboards on two of the walls.

> And now we're supposed to go through and approve all of them? This meeting was only scheduled for an hour, we would need days to go through all of these!

The plan is as follows:

- __High-risk changes__ must have change requests submitted, but must have authorization before being scheduled and implemented.
- For __messy middle changes__ the change submitter has the responsibility for consulting and getting approval from people affected. Once they do that, they submit their change card for us to review and approve for scheduling.
- __Low-risk changes__, changes that have been done many times before successfully are preapproved. They still need to be submitted, but they can be scheduled without the need of approval.

### Mitigating Risk
It is three days before the Phoenix deployment an Bill finds himself in a CAB meeting where people are contributing much more with the new procedure and changes are being approved and planning is made.

They realise, too many changes are planned for Friday, which is also the deployment day for the Phoenix. People start exchanging ideas and after a while, the distribution of the cards on the change board is much more even.

### Taking Load Off of the Bottleneck
Brents name keeps coming up and Bill goes to Brents cubicle to observe him. He sees Brent is falling behind because everyone is just calling him on their will to get things done, which constantly interrupts him.

> Okay, from here on out, you're working only on Phoenix. I'm expecting you to reject any task that anyone tries to assign you.

### Building Technical Knowledge Across the Organization
Bill meets Wes and Petty to share his ideas:

> We need to change the way we're managing escalations to Brent. Right now.
> Every time that we let Brent fix something that none of us can replicate, Brent gets a little smarter, and the entire system gets dumber.

They come up with the following plan:

- A resource pool of level 3 engineers will be created who will be responsible for resolving all incidents and will be the only people who can get access to Brent.
- Brent is not allowed to fix the escalations himself, he must guide the level 3s.
- All the fixes must be documented by the level 3s upon resolving an incident.

### Quality Assurance
Two hours after the Phoenix deployment was scheduled to start, and things are not looking good.

Wes speaks first:

> We are getting incomplete releases from the developers. We still don't know how to configure the test environment so that Phoenix actually comes up cleanly.

and William follows Wes:

> The code is changing so fast that we're having problems keeping up. Phoenix is going to blow up in production. I've talked with Chris a couple of times about stopping the release, but he ran right over me. 
>
> When we find problems in our testing, we send it back to Development to have them fix it. Then they'll send back a new release. The problem is that it takes about a half hour to get everything set up and running.

Convinced with the fact that this deployment will not bring any good, Bill contacts Steve to inform he wants to stop the deployment at this time. His request is not approved:

> It sounds bad, but at this point, we don't have a choice. We have to keep going.

After a very long night, things go as expected, the deployment becomes a big failure.

### Definition of Complete
Ideas are being exchanged in a meeting on how IT in general can be improved. Chris claims his team delivers projects on time. _Yeah, just like you hit the Phoenix date, right?_ Wes asks.. 

> That's not what I meant. It was a total disaster. But, technically, we did hit the date.

This is a recurring pattern. Development never factors in all the work that Operations needs to do.

### Project Freeze
They also talk about how they should be planning incoming demand. Bill asks Steve:

> Are we even allowed to say no?

to which Steve responds:

> I need you to say no. We cannot afford to have this team be order takers. We pay you to think, not just do.

Finally, they decide to take a very different approach: __IT Operations will freeze all non-Phoenix work__, naming it Project Freeze.

### Work Centers and Workers
Erik and Bill visit the manufacturing plant one more time, where they discuss how Brent is similar to a worker in the plant and how the IT work is similar to work centers. If work centers depend on a specific worker, they can not operate. 

### Standardizing and Documenting Work
Erik summarises what Bill has done by creating the Level 3 pool and appricates the effort.

- Standarized Brent's work so that other people can execute it. 
- Reduced the number of work centers where a specific worker is required.
- Generate documentation that will enable you to automate some of them.

### Releasing And Feedbacks
Erik advises the key is to have frequent releases and faster feedback.

> You'll never hit the target you're aiming at if you can fire the cannon only once every nine months. Stop thinking about Civil War era cannons. Think antiaircraft guns.

### DevOps
Development and Operations working together, along with QA and the business, are a supertribe that can achieve amazing things. One must bulild a __deployment pipeline__, a stream from code check-in to production.

### Automate Everything
They finally start working on automating everything, where they will be able to create environments for Development, QA and IT Operations daily, synchronised, ultimate goal being: being able to do up to 10 deployments per day to production.

## Key Learnings
- People tend not to use the processes they find cumbersome.
- The main objective of planning work is to get the left and right hands to know what the other is doing which should give some situational awareness during outages.
  - Work Planned and Work in Progress must be visible across the teams.
- Being able to take needless work out of the system is more important than being able to put more work into the system.
- Work In Progress is the silent killer.
  - When there is no clear, unified decision made by the business as a whole as to what work is a priority, company resources are innevitably wasted switching between all the Work In Progress depending on who is screaming the loudest. [Source](
  https://patrobinson.github.io/2015/09/01/wip-is-the-silent-killer/)
  - Code is in production, no value is actually being generated, because it's merely WIP stuck in the system.
- There must be enough time for Quality Assurance planned, and approval from QA is a must for production deployment. 
- QA must be able to stop the release if found necessary.
- Being able to start a test environment must be a fast, automated process.
- Definition of Complete must be done clearly, and all plan must be done accordingly.
- Analysis of capacity and demand must be done before accepting work, and new work must be refused if needed.
- Practicing half an hour daily is better than practicing once a week for three hours.

### Four Types Of Work
- Business Projects
- Internal IT Projects
- Changes
- Unplanned Work
  - Also known as: Firefighting
  - Unplanned Work is the most expensive form of work: in the presence of unplanned work, all planned work gets delayed.
  - It is important to know where your unplanned work is coming from.

### Three Ways
#### The First Way
The First Way helps us understand how to create fast flow of work as it moves from Development into IT Operations, because that's what between the business and the customer is.

Visualising work is very important for creating a fast flow of work. Index cards on a kanban board is a very good mechanism to do this. It lets everyone see the Work In Progress.

#### The Second Way
The Second Way shows us how to shorten and amplify feedback loops, so we can fix quality at the source and avoid rework. Sources of unplanned work must be removed.

#### The Third Way 
The Third Way shows us how to create a culture that simultaneously fosters experimentation, learning from failure, and understanding that repetition and practice are the prerequisites to mastery.

## Further References
- [Gene Kim Defines the 3 Ways of The Phoenix Project](https://www.youtube.com/watch?v=nUOXDEvplRc)

## Appendix
### Characters
<dl>
<dt>Bill</dt><dd>Former Director of Midrange Technology Operations and VP of IT Operations.</dd>
<dt>Brent</dt><dd>A highly skilled engineer.</dd>
<dt>Chris</dt><dd>VP of Application Development.</dd>
<dt>Erik</dt><dd>Technology hotshot and a potential board member.</dd>
<dt>Patty</dt><dd>Director of IT Service Support.</dd>
<dt>Steve</dt><dd>CEO and acting CIO.</dd>
<dt>Wes</dt><dd>Director of Distributed Technology Operations who is responsible for maintenence of servers and databases.</dd>
<dl>

### Acronyms
<dl>
<dt>CAB</dt><dd>Change Advisory Board, responsible for Change Management Process.</dd>
<dt>SAN</dt><dd>Storage Area Networks, provides centralized storage to many critical systems where failures are typically global.</dd>
</dl>

### Places
<dl>
<dt>Parts Unlimited</dt><dd>Manufacturing and retail company.</dd>
<dt>NOC</dt><dd>Network Operations Center, a large area displaying the status of all the various IT services on large monitors.</dd>
</dl>