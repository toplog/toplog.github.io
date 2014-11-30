---
layout: post
title:  "TopLog User Story"
date:   2014-11-30 20:49:27
categories: story user
tags: user story
author: patrick
description: "How to get ahead of your product breaking via logs"
share: true

---
## User Story: "Life as a CTO of a new startup"


####Problem: Customers Complaining That Your Web App Is Slow

If you're the CTO, DevOps Manager or the developer on call whose responsibility is to make sure your company's web app is up and running, you probably have gotten messages like this in your inbox (or as a tweet or on hackernews):

“Your app is really slow - Customer X”

This probably results in anything from a feeling of mild irritation to —if your company is strictly net-facing— a feeling of gut-churning panic. When your product has an issue and customers start complaining, it's hard to measure the loss in revenue but you know it can get bad… real bad. Attracting new customers is difficult at the best of times, and losing them happens all too easily.

Meanwhile, business reputation, customer loyalty and the effects on employee productivity are much harder quantities to calculate.

So while you may be the one who is panicking, this is the entire company's problem. Regardless of your sense of urgency, however, your options remain the same, and they're not particularly efficient.

You'll probably start by checking out the app for yourself —yes, slow! Okay, so time to look through the logs… but what are you looking for?

Rooting around, eventually you find this fun line -just once- hidden among 10,000 other log lines from the past hour:


`[03/Nov/2014:23:59:44 +0000] prod-worker1 1115 PROCESSLOG INFO analysis output: Warning: log() expects parameter 1 to be double, string given in foo.php on line 156 Warning: Division by zero in foo.php on line 156 Warning: log() expects parameter 1 to be double………`

Okay, so there's some sort of back-end component getting a `divide by zero`? How come? And why is this resulting in some of the app being slow?

Generally to answer these questions you have to find the needle in the haystack.  Finding the log entries are only easy to find when they are tagged as `ALERT` or `ERROR`. However, problems happen when the log lines creating the issue are not obviously tagged like that.

Now you have to track down the developer who wrote the log line, find out what their decision process was,  and hope to get some insight into what caused the problem in the first place. 

Was this error really the root cause? Are there other, related issues that caused the problem? And what lead to this? 

All of this investigation takes time, and diverts you and your dev team away from your main job: developing the product.

You don't want to waste time spinning your wheels. But that comes with the territory, right? You spend a lot of time putting out fires and trying to minimize the damage.


####Solution: Pattern Detection and Alerting

So what if —instead of those panicked error messages or customer complaint's— your inbox had an email showing this:

    Warning, new pattern appeared in your logs:
        * expects parameter * to be double, * given in * on line 156 Warning: Division by zero in *
    
    It was matched 6 times in the last 5 minutes.

    Click to see your alert report.

This is what topLog provides me (if you haven't guessed this is a thinly disguised actual example from our logs). The line is detected as a pattern never seen before in the logs, so topLog sends me a notification that I can act on immediately.

Not only does it point me to the entries in the log that I need to see, I can view the corresponding abnormal behavior, helping me find the root cause. topLog offers real anomaly detection— telling me what to look for and where.

By monitoring application logs, detecting patterns and notifying me of abnormalities as they happen I can approach my job pro-actively.


