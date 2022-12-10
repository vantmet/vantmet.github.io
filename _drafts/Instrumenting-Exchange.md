---
layout: post
title: Instrumenting Exchange - Are you mad?!?!
categories: [Exchange, Go]
---

I recently saw an issue which lead to an Exchange server passing mails over an IP that hadn't been anticipated. That meant that a whole heap of mails got dropped. Whilst troubleshooting this issue I discovered that there are not many ways of finding how many of the mails passing through your exchange system are being dropped/rejected etc. So I decided to make myself one.

In the modern web application world, and particularly from an SRE point of view, we talk a lot about instrumenting web apps. A core component of this is monitoring the web server's request rate. Service Level Indicators (SLIs) such as rate of responses that aren't 200; or percentile of responses that return in x milliseconds are common. I thought it would be easy for one of our many monitoring tools to give me a similar set of data but for the SMTP process. It does after all have a similar, well defined, error code system, recorded in log files. This is where my problems started.

The events I'm interested in are stored in the MessageTrackingLogs files. These are plain text files, easily searched. Of course there are also a set of specific powershell CMDlets to search them. So there are a few options to get the information I want:

1. Write a batch file to call LogParser (link) to search the files.
2. Write a PowerShell script to find the metrics I want and return them.
3. Write a more robust executable to monitor the stats and provide updates.

The first option is complex. It involves SQL queries and is limited to searching the whole MessageTrackingLog file. This is a bit of a problem as there is no way to get a consistent time to search over by just looking at the files.

The second option turned out to be fairly easy with Import-CSV and some basic logic. The problem here was, it's slow. ~30seconds for each lookup. If I was to call this from our monitoring tool every few minutes, it would timeout most of the time.

The third option was the most appealing. I'd already worked out most fo the logic I needed from the PS script. But with an exe I could have a thread doing the lookups on an infinite loop and storing the result, and another thread provisioning a way to view them. It turns out that setting this up in Go is trivial and that's what I'm going to look at here. We will start with the basic app outline, and how it serves the metrics. Then delve into how I got to a place where I was provisioning what I wanted.
