---
layout: post
title: "Part 4. The call connected. Then the AI started making things up."
date: 2026-11-07 12:00:00 +0900
categories: [badajum]
tags: [ai, voice, telecom]
series: "Badajum: an AI that answers the phone"
---

*Badajum: an AI that answers the phone — part 4 of 4.*

I called the 070 number from my phone. The call went through the carrier, landed on my server, and the AI picked up: "Thank you for calling Papa John's." It took the order. The connection itself worked.

### The problem was what the AI said

After a few calls, the AI kept saying strange things. It answered questions nobody asked and drifted off the script. The biggest problem was that **it invented menu items that don't exist**. The menu was right there in its instructions, and it still made up plausible-sounding pizza names and offered them to customers.

In Part 3 I had the server validate items and sizes, but that check only runs when the order is saved. It can't stop what the AI says mid-call. By then the customer has already heard about a pizza that doesn't exist, and in a store, that's a promise you can't keep. Explaining "sorry, we don't actually have that" at the counter is worse than just answering the phone yourself.

So I made a call: **today's models aren't reliable enough for real customer-facing work yet.** The whole pipeline is built and working. When the models get better, I'll pull it back out.

### Other problems still on the list

- **Cost**: the 070 number, the forwarding plan and its cap, AI usage, voice usage, and hosting. Each is small, but a per-store monthly price has to cover all of them. If the Korean is weak and you move up to a bigger model, the per-call cost goes up too.
- **Hosting**: right now it's the PC in my room. A real store needs an always-on server with a fixed address.
- **Law and privacy**: how long to keep recordings and customer phone numbers, and where, needs a proper look at the law.
- **Brand and ordering systems**: I tested with Papa John's because it's the conversation I know best. Using it in an actual store would need permission from the store and headquarters, plus integration with their point-of-sale system.

### What stuck with me

All the automated tests with simulated calls passed. The browser calls were fine too. But a handful of real calls exposed the most important problem. What you can build and what you can actually put in front of customers are different things, and you only see the gap when you test in the real world.

That thought kept bugging me. Is there a way to know how something will behave in the real world before you test it there? A few days later, the same question came back in a completely different place: my circuits class.
