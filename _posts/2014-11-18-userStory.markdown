---
layout: post
title:  "TopLog User Story"
date:   2014-11-18 20:49:27
categories: story user
tags: 
author: patrick
description: "How to get ahead of your product breaking via logs"
share: true

---
## User Story: "Can't log in"

As CTO / Dev Manager / Chief "put out the fire" person, I used to get a lot of these:

"Websites slow" or "Can't login" or "my run failed" (we deal with data analytics).  And you can guess the first thing I do is go look, then dig, and grep, into the logs... and I find this....

`[2014-11-15 16:09:12] production.ERROR: exception 'UnexpectedValueException' with message 'The stream or file "tmp/filetmp.tmp" could not be opened: chmod(): Operation not permitted' in /var/bin/vendor/bootstrap/compiled.php:9016`

What the heck, a file doesn't exist so none of our user can login?  What happened to it?  Why is this even required?

And thus more digging, then asking and then (hopefully) the solution.  First, the easy one, bring back the file, the second, find the dev that wrote the log line `compiled.php line 9016` and start chatting about what the decision process was here, why, why , why, etc.   And then, we fix, and hope things stay working for a while.

You'll notice I said "used to", now I get this in my inbox:

`Notice: New pattern has shown up, it reads: ElasticSearch query failed to IP * on run *`

And instead of waiting to get an email from a new customer saying "I can't see my resuts" (if they bother to send it), I can start the process ahead, before the user experiences the break.  I can see the log line immediatly, what log lines happened before it and what is related.  Solving the problem pro-actively.

I can put the silly filetmp.tmp file back before they can't log in, and then, I can ask the dev in question, "why is this required".

Who else has fealt the pain of something breaking, digging through logs, and hoping you can find the problem fast enough that very few people realize something has gone wrong?  I doubt I'm the only one ;-) 
