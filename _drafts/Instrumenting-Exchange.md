---
layout: post
title: Instrumenting Exchange - Are you mad?!?!
categories: [Exchange, Go]
---

I recently saw an issue which lead to an Exchange server passing mails over an IP that hadn't been anticipated. That meant that a whole heap of mails got dropped. Whilst troubleshooting this issue I discovered that there are not many ways of finding how many of the mails passing through your exchange system are being dropped/rejected etc. So I decided to make myself one.

In the modern web application world, and particularly from an SRE point of view, we talk a lot about instrumenting web apps. A core component of this is monitoring the web server's request rate. Service Level Indicators (SLIs) such as rate of responses that aren't 200; or percentile of responses that return in x milliseconds are common. I thought it would be easy for one of our many monitoring tools to give me a similar set of data but for the SMTP process. It does after all have a similar, well defined, error code system, recorded in log files. This is where my problems started.

