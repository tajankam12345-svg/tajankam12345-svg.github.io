---
layout: post
title: "Part 3. Field-testing it on the store where I work"
date: 2026-11-06 11:00:00 +0900
categories: [badajum]
tags: [ai, voice, telecom]
series: "Badajum: an AI that answers the phone"
---

*Badajum: an AI that answers the phone — part 3 of 4.*

I took the setup I'd built for repair techs and dropped it onto Papa John's pickup orders. It's the conversation I know best. I hear every day how customers phrase things and where orders get tangled.

### Four text boxes instead of a settings page

At first I had a separate setting for every detail: service fee, industry, and so on. Once I tried to fit a pizza place in, the fields didn't match anymore. Instead of adding fields for every industry, I replaced them with four free-text boxes the owner fills in:

1. **Greeting**: "Thank you for calling Papa John's. An AI will take your order."
2. **Conversation rules**: tone and question order. Location → pickup or delivery → menu → crust → sides and drinks → confirm → total and pickup time.
3. **Menu**: 24 pizzas plus sides and drinks, with prices.
4. **Everything else**: promotions, hours, FAQs.

Rules like saving the order, hanging up, and the 911-style emergency script (119 in Korea) get appended automatically, so the owner never has to write them. What changes from store to store stays separate from what every store needs.

### Code guards the things the AI can't get wrong

The Papa John's menu in Korea is pickier than you'd think. It comes in four sizes (Regular, Large, Family, Party), but only 9 pizzas come in Regular, the thin-crust pizzas only come in Family, and the plant-based ones only come in Large. If a customer asks for a Regular John's Favorite, that size doesn't exist.

Just telling the AI "if a size doesn't exist, ask again" didn't feel safe enough. So in the menu box you can write lines like `Super Papa's | R, L, F, P`. The moment the AI tries to save an order, the server checks the item and size against that list. If they don't match, the save is rejected and the AI has to ask the customer again.

Prices work the same way. The AI doesn't do mental math. It passes along the line items, the server calculates the total, and the AI reads that number back as-is. It's only allowed to quote prices and deals that are written in the four boxes.

In the end I split the work: the AI handles the conversation, and code handles the numbers and the menu checks. On real calls, though, that line turned out to be a lot leakier than I expected. More on that in Part 4.

### Voice and timing

On the phone, timing feels worse than content. If a customer says "umm..." while thinking and the AI jumps in, it's instantly uncomfortable. So by default the AI decides a customer is done talking from what they said, not from a stretch of silence.

Voice was its own decision. Besides the default voice, I added ElevenLabs voices as an option. The AI replies in text, the server cuts it into sentences and synthesizes each one ahead of time, and if the customer interrupts, the remaining sentences get thrown out. There were more natural-sounding models, but they were slower. On a phone call, a fast answer matters more than a pretty voice.

### Trying to break it on purpose

I wrote down more weird scenarios than normal orders and tested them:

- Ask for a size that isn't on the menu → it should offer the sizes that exist
- Say you smell gas → the emergency-number script comes before anything about pizza
- Mumble on purpose → after two misses it should wrap up with "the store will call you right back to confirm"
- Pretend to be a telemarketer → the call should end with no order logged
- Hang up halfway → if there's a number, a "missed call" entry should be saved

I also added guardrails for cost. Calls are capped at 5 minutes, with a wrap-up prompt at 4:30. If the order is saved and the AI doesn't hang up, the server ends the call after 15 seconds. The real API key never goes to the browser; it only gets a temporary key that expires in 2 minutes.
