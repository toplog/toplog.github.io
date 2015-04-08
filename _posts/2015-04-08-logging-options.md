---
layout: post
title:  "Logging Options"
date:   2015-04-08 9:00:00
categories: DevOps, alerts
tags: DevOps, testing, alerts
author: patrick
description: "How and where do I log"
share: true

---
###So, where do I log?

Logging is something that has a few options, most people think of logs as either text files or syslog, which, in reality, are essentially the same thing. Our customers, those who already have logging going on inside their stack, usually have one of the two scenarios in place:

1. They log each component of their stack to a different file within the server
or
2. They have all components of their stack logging to syslog / a central file / service on the server 

In either case, they have logging, which is awesome.  Both have pros and cons.  We typically recommend staying away from dumping each component to one file, mostly because, as a text file, it will grow fast, contain things you don't control (other system level logs from outside your core stack).  The downside of having each component go to its own file, however, is needing to look through all the different files to see what is related can get tricky.  This can be dealt with by centralized logging systems (cough cough, topLog, cough), but before we get to that, some nice tips:

- Put everything in `/var/log/stack_components_name/stack_components_name.log` for an example, go look at `/var/log/apache2`
- SET UP logrotate.  I can not stress this enough.  Logs that grow and grow, well, they grow!  Make sure you have logrotate rules to take care of each file you write.  We like to limit log files to 100MB before being gzipped and rotated.  If you have a log management solution in place then you don't need to keep many of the compressed around, I would recommend at least 2 days, if HD space is available.
- Document what is in your log directory and what is being logged.  Nothing is more embarrassing then running out of HD space because of your own logs.  We actually have a README in our log dir that lists all the components logging into that folder.

### Okay, so what about syslog (vs files) ###

So, I shouldn't brush over syslog, as its awesome.  And yes, using syslog (or the equivalent) is a great way towards centralizing your logs and shipping them somewhere else (a centralized syslog server).

So what is syslog (besides, often the case, a file called `syslog`)?  It is widely supported protocol that allows a set of devices all to record events in a similar manner, and letting them push these event records to a syslog server.  Sounds great!  Why don't we just configure all our components, mysql, apache, our app itself, all to push to syslog instead of two files?  Well, honestly, that is a great way to go.  However: Even though it is a protocol, their is no real standard message format, so having all these components, even the ones your team created, push to one place, may make it hard to read / correlate manually. That said, you can probably deal with that issue.  The biggest issue we have with Syslog is it uses UDP, and hence, could care less if an event record (log line) gets lost in transit.

Yes, you read that right, Syslog is a transport protocol that doesn't care if your log lines get to the other end correctly.  I can explain why: syslog doesn't want to bog your network down or your log source (the server / component pushing the log) with dealing with handshakes, ack packets and resending data, i.e it uses UDP.  This is awesome, lightweight and designed never to take your stack down because of logging transport back ups.  What that does mean, however, is you can loose logs, and probably when you need them most, when things go wrong.

Lastly, syslog has no authentication or encryption.  So if you are talking about pushing logs around a network, internal or external, you can't take for granted that the events being received by your syslog server aren't:
- being read by someone else
- actually created by your own sources

If you are going to use logs for security, fault detection, anomaly detection, or really for anything, you need to trust the channel and the message.  Unfortunately syslog, on its own, does not provide this.

### So, if not syslog, then what? ###

Let me be clear, go ahead and use syslog, however, maybe don't use it to centralize all your logs, use something else for that, especially if the channel your pushing your logs across could have heavy network traffic or may be in the open (always assume they are in the open, even internally).

What do we recommend to people: 

File based logging, as previously described, locally (i.e within one server/instance/container/vm) and use a lossless / encrypted / low footprint log shipper.  In that domain their are a few solutions available, some for free, some not.  We don't hide our love for logstash and logstash-forwarder, in fact its what we use to get logs into us from our clients.  I won't get into the details of logstash vs fluentd vs other solutions, as they are all pretty awesome and do a great job of moving logs around.  I will however talk about the general model we love to see:

`log file -> shipper -> network -> central log server`

and then, from that server:

`log server -> queue -> indexer -> log store`

You can probably guess some of the components we love to use for all of that, but the focus for us in this model:

- the shipper needs to be lightweight and handle remembering "the last thing I sent" so it can deal with making sure no log lines are missed
- the shipper needs to have throttling as part of its protocol so it doesn't swamp either end or the channel when trying to catch up
- encrypt and authenticate the channel for shipping
- do NO processing of logs on the front line of the receiving end, just throw to a queue that can scale as needed (key / value stores are awesome for this)
- off the queue, have any processing needed be done by an indexer, scale this as needed (length of Q based is nice)
- have the indexer throw your logs into a store of some sort, key / value or document based works, by sql style can work just as well depending on how structured your logs are.


### Wrap up ###


You may be thinking: syslog is so much easier then what he just said.  You are right.  It is, and it has been around a long time and will be for a long time.  It is awesome.  However, scaling it and, more importantly, doing something with those logs is not something it is great at.  If you want to be able to scale your services without worrying about whether your syslog infra will keep up, or you care about getting some intelligence out of those logs, I urge you to look at setting up something like what I just described.

Thats all for now,
Happy Logging!



