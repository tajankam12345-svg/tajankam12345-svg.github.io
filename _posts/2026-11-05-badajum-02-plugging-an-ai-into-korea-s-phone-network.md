---
layout: post
title: "Part 2. Plugging an AI into Korea's phone network"
date: 2026-11-05 10:00:00 +0900
categories: [badajum]
tags: [ai, voice, telecom]
series: "Badajum: an AI that answers the phone"
---

*Badajum: an AI that answers the phone — part 2 of 4.*

Getting an AI to understand speech and talk back is a solved problem at this point. OpenAI's realtime voice models take audio in and give audio back. The real challenge was connecting that AI to **an actual Korean phone number**.

### Start with a browser call

If you hook up the phone first, you can't tell what broke. So I started with a test page where I talk to the AI through my laptop mic. The browser and the real phone line share the same agent logic, so both follow the same settings and the same conversation rules. When a call ends, the page shows the exact text message the owner would get, and the request is saved to a SQLite database. I packaged it so it installs and runs with two double-clicks on my PC.

### How a customer's call reaches the AI

You can't change the owner's cell number, because customers already have it. So the setup looks like this:

```
Customer → owner's cell (no answer)
         → carrier call forwarding
         → our 070 number (a Korean VoIP number)
         → my server ⇄ OpenAI realtime voice
         → request saved → owner notified → owner calls back
```

Every line of that diagram turned out to be a Korea-specific problem.

- **Call forwarding has a cap.** The carrier plan I looked at stopped forwarding after 270 minutes a month. Lifting the cap cost about 2 won per second, roughly 8 cents a minute. That's why the app has a forwarding-usage screen, and why every call is cut off at 5 minutes.
- **You may not get the caller's real number.** If a forwarded call carries both the original caller (From) and the number that forwarded it (ForwardedFrom), one 070 number can serve many businesses. If it doesn't, every owner needs their own 070 number. The docs didn't say, so I had the server log every header it received.
- **iPhones might screen the call first.** With "Silence Unknown Callers" turned on, a first-time customer's call may never get forwarded. Onboarding includes instructions for turning it off.
- **Porting is cleaner.** If the business has a landline, porting that number straight to the service avoids both the forwarding cap and the forwarding fees.

### Voice streaming instead of a SIP trunk

The original plan was to have the 070 provider pass calls straight to OpenAI's SIP endpoint. With the provider I was using, that required a SIP trunk add-on at 99,000 won a month, about $70. That's a lot for a student who just wants to run tests.

So I found another route, the provider's voice-streaming mode:

1. When a call comes in on the 070 number, the provider asks my server what to do with it.
2. My server replies, "Connect to this WebSocket."
3. The call audio flows over that WebSocket. My server forwards it to OpenAI and streams the replies back.

Luckily, both sides use the standard phone codec (G.711 μ-law at 8 kHz), so audio passes through with no conversion. The WebSocket connection itself isn't signed, so to block fake connections my server puts a token only it can generate into the URL and checks it when the connection opens.

### The server is my desktop

I didn't have money to rent a server. I ran it on my own PC and used a tunnel to give it a public address. Put that address in the provider's console, and calls to the 070 number ring through to the computer in my room.

Writing this up reminded me of something. The hard part of shipping an AI product isn't the AI. It's fitting the AI into real-world plumbing: carriers, rate plans, smartphone settings.
