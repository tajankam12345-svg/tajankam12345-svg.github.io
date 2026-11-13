---
layout: post
title: "Not a stock picker: building an AI that helps you invest"
date: 2026-11-12 09:00:00 +0900
categories: [stock]
tags: [ai, finance, plan]
series: "An AI that helps you invest"
---

This project doesn't exist yet. This post is about why I want to build it and what I want it to be.

### I don't want an AI that picks the next moonshot

A stock-prediction AI is about the most overdone side-project idea there is. I'm building one anyway, because I think most people are asking AI the wrong question.

Scroll through any investing forum and you'll see it: people asking a chatbot which ticker will pop tomorrow. They want to catch the next meme-stock run, and they want the answer now. But that isn't what investing is. Investing means deciding how to split what you own, rebalancing as things change, and putting money into companies you believe will be worth more in years, not days.

### Short-term prediction is where AI is weakest

I learned something while building a circuit-board simulator (that's a separate series). An AI is only trustworthy inside the range of data it learned from. Where the data is thin, it's confidently wrong. My first board model, trained on 263 real boards, missed hotspot temperatures by a median of 66%. Trained on tens of thousands of boards, the error dropped to 1–3%.

I think markets work the same way. The AI models we use, LLMs included, were presumably trained on long stretches of history. But what moves a stock tomorrow is tonight's headline, one fund's big order, the mood of the market. That data is sparse and noisy, and predictions made where data is missing come with huge error bars.

So I changed the question. Not "what goes up tomorrow?" but **"how should I weight the stocks I already own?"** And the AI doesn't answer that for you. It lays out the evidence and gives an opinion you can weigh. It's an assistant, not an oracle.

### A tool for healthier investing habits

I'm not trying to build something that makes people trade more. I want something that helps people step back and look at the bigger picture.

### Why it's free

Imagine someone selling a stock-prediction AI for $10 a month. My first reaction would be suspicion. Can I actually trust it? Does it have a real track record? Does it even work?

If it's free, the dynamic changes. People can try it without that suspicion ("eh, why not?") and casually check its calls for a few days. If lots of people watch it get things right, traffic goes up, and that traffic becomes one more signal for grading the AI.

Charging money has a bigger problem, too. Selling predictions reads like a promise: our AI gets it right. So what happens when someone follows it and loses money? Honestly, I don't even want to imagine that.

### What the AI looks at

On a regular schedule, the AI collects:

- The news and trends of that moment
- Price trends and charts
- The company's financial statements
- Material events: earnings, leadership changes, mergers and acquisitions
- What happened in similar situations in the past

### What it gives you

- **A view on each stock**: add, hold, or trim for the long run, with the reasoning behind it.
- **Portfolio weights**: how to keep your overall mix balanced, and whether you're too concentrated in one name. When the situation changes, it updates its advice in real time.

### The AI grades itself

This is the part I care about most. Every opinion the AI gives gets logged:

1. The inputs at that moment: the news, events and trends, plus all the data up to that point
2. What the AI recommended
3. Whether that call turned out to be right

Once the log builds up, every type of call gets a hit rate, something like "buy calls made in setups like this have been right X% of the time." It's the same idea as publishing an error figure for every check in my board simulator. Users should be able to decide, with numbers, how much to trust the AI.

There's one trap here. When grading past calls, you can only use **information that was available at that moment**. If even a little later news or price data leaks in, it's like taking a test after seeing the answer key, and the hit rate looks far better than it really is. Quants call this look-ahead bias. So the log will record not only the inputs but also the cutoff of the data the model had been trained on at the time.

### Try it as a paper-trading mini-game

The pitch will be "come try this AI." Instead of real money, you get a paper-trading system that mimics real trading as closely as possible, built like a mini-game.

- Take the AI's call and paper-trade on it.
- Or ignore it and trade your own way.

Both outcomes become answer keys for the AI. As results pile up for people who followed it and people who didn't, I'll be able to say with numbers whether its advice actually helped. The final decision always stays with the user.

### Next: an assistant that fits your style

Everyone invests differently. Some people buy and hold, some rebalance often, some avoid risk at all costs. By learning the styles that show up in the paper-trading game, the AI can give advice that fits each person. That personalized assistant could become a paid tier. It wouldn't sell predictions. It would sell advice that understands you.

*This is a project plan, not financial advice.*
