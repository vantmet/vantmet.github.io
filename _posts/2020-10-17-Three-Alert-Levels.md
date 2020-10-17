---
layout: post
title: Three Alert Levels
categories: Life
---

I've read far more self-help books than is good for me. I've read about far too many *productivity systems* too. In my work as a SysAdmin I get a *lot* of emails, chat notifications, and general interruptions. I bullet journal. I've tried the Pomodoro technique. But one of the most useful things I've come across, applied to both my personal and work lives is a little thing I picked up for the [Google SRE books](https://landing.google.com/sre/books/). The concept that information coming from systems should be classified as one of three types.

## Type 0 - File under B

That email newsletter you signed up for 5 years ago and don't even open; SPAM; All of those emails you hit "delete" on without even finishing the subject. These are type 0. Bin them. Preferably with a filter. This is not one of the three types, but it's a useful catchall for the rest. If you can't place something into the types that follow, it goes here.

## Type 1 - Informational

That newsletter you receive from that games company; That list of what backups happened last night (and how they were all successful); Last month's bank statement. This is anything that you don't need to **act** on. It needs to be filed. You might want to look it up later. But if you don't need to do anything with it, it falls here. In systems terms, this stuff should end up centrally logged, and possibly on a dashboard you can go look for. In personal terms, this is the vast majority of email. Turn the notifications off, and look at it when it's reasonable.

## Type 2 - Actionable

The SAN hit the point where it needs more disks ordering. Reminder it's your anniversary next week. These are action items. They aren't hurting you yet though. You have time to order those disks and get them installed. You have time to go shopping for a gift and order the flowers. These are things that should be in a ticket system. Ideally they hit the ticket system and not your mail box. Don't get me started on ticket systems that email about every new ticket, those emails are Type 0. In a work environment that's exactly where these end up. As a ticket in someone's queue. In a personal environment, these end up in my Bullet Journal, on the future log; Or in [RTM](https://www.rememberthemilk.com/) depending on my mood.

## Type 3 - Critical

Power just went off in the DC; A critical service died; Account doesn't have funds for the payment due today; Partner's car broke down; Call from School. These are things that need action **now**. Not tomorrow, not when you get around to it. Stop what you are doing and fix this. These are the ones that go PING on your phone. In a work environment, I've often filtered these alerts through to [OpsGenie](https://www.atlassian.com/software/opsgenie) so that the right person bets the message in the right way, no matter the day or night, and the issue can be escalated if it's not picked up. Personally, it's the contacts that bypass do not disturb.

I like to think of this as, Ignore it, Plan It, Do it. Stuff I can ignore shouldn't hit my inbox. Stuff that needs planning is a ticket, and stuff that needs to be done now, is. That is the key for me to Inbox Zero.
