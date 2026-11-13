---
layout: post
title: "Part 1. You can't stretch dough and answer the phone"
date: 2026-10-28 09:00:00 +0900
categories: [badajum]
tags: [ai, voice, telecom]
series: "Badajum: an AI that answers the phone"
---

*Badajum: an AI that answers the phone — part 1 of 4.*

At the Papa John's in Korea where I work part-time, the phone is the most awkward customer in the store. Whether I'm stretching dough or pulling a pie out of the oven, when it rings I have to stop and pick up. And the conversation is almost always the same. Which location? Pickup or delivery? Which pizza, what size, what crust? Any sides or drinks? Then I read back the total and tell them when to come by.

One day it hit me. If a conversation is this scripted, why does a person need to have it? My job is making pizza, not asking the same six questions dozens of times a day.

### Who needs this more than I do?

When I thought about it more, I realized some people have it much worse. A store employee can at least pick up. A plumber halfway under a sink, or a boiler technician on a job, can't. In Korea a lot of these trades are one-person shops where the owner's cell phone is the business line. Every missed call is a lost job.

So my first mockups weren't for a pizza place. They were for solo repair techs. I named it Badajum, which roughly means "I'll pick up for you" in Korean. If the owner doesn't answer within a set time (say, 15 seconds), the AI takes the call and logs the request, and the owner calls the customer back from the app.

- **Home**: AI on/off, today's requests, pending callbacks
- **Request detail**: the problem ("no hot water, boiler display shows error 03"), neighborhood, preferred time, plus a call summary and the full transcript
- **Onboarding**: what kind of number customers call, carrier call-forwarding setup, and an industry template
- **Forwarding usage**: Korean carriers cap call-forwarding minutes each month, so this got its own screen

I also sketched a console for designated-driver companies. That's a big service in Korea: after drinking, you call someone who drives you home in your own car. The AI would answer many calls at once on the company's main line, capture pickup and drop-off locations, and pass them to the dispatch system.

### What was already in the first screens

What I spent the most time on was how customers would feel about talking to an AI. If it pretended to be human and got caught, I figured the business would lose the customer's trust along with it. So the greeting says up front that it's an AI and that the call is recorded.

If the AI fails to understand the same thing twice, it stops pushing and says, "The owner will call you right back." Handing off to a person beats making the customer wrestle with a bot. The AI doesn't commit to appointments or price quotes either. It takes the request, and the owner makes the decisions on the callback.

Looking back, the hardest part of this stage wasn't technical. It was figuring out whose time this would actually save. I assumed the tech would be the easy part. It turned out to be much harder than I thought.
