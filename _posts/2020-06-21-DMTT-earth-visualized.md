---
title: DMTT earth visualized
subtitle:
Description:
categories: [standards, curiosity]
tags: []
readtime: 4
---

I've had some discussions with people about the practical use of decimal time and its implications, and sometimes it's really hard to discuss these kinds of topics without visualizing what you're talking about in some way. In the process of discussing this, a friend even found an error in one of my previous articles that has been up for more than a year and a half, so it's obviously not an easy subject. I don't know how one and a half year passed already since my first article about time - Is it fair to blame time? - but I intended to continue the time series with some more follow-ups, so here's the first one.

A heads-up; I know some of my readers - like me - are browsing the web with Javascript disabled for privacy considerations. This blog will never have any kind of trackers, but I still understand if you decide to not enable Javascript for this domain. In that case you have to imagine the passing of time yourself as the examples run in Javascript.

## Common time system
Creating the "common" clocks in Javascript was very easy. Just run a function on an interval that creates a Date object for 'now', and the hour-, minute- and second-variables can be easily retrieved. Because the "setInterval" function in Javascript doesn't run exactly on the defined millisecond interval, some jumping of seconds was visible, so instead of updating the clock every second it gets updated 10 times every second.

As a reference here the common clock displaying your current local time. It seems a bit useless to put it here - you're not going to use this website as a clock I hope - but it is useful to compare it with the next clock.

{% include time/common.html %}

## Local DMTT
To calculate the local time in DMTT we need to do some calculations. There is not (yet) a javascript API available that can convert common time to DMTT. The best we can is calculate the local time based on the elapsed milliseconds since the start of the day. To get those, we have to call the same functions as the above clock, and do some multiplications;

> &Delta;t<sub>in ms</sub> = (&Delta;s + (&Delta;m * 60) + (&Delta;h * 60 * 60)) * 1000 + &Delta;ms

Where

> &Delta;t<sub>in ms</sub> = number of milliseconds since the start of the day <br>
> &Delta;h = number of hours since start of the day<br>
> &Delta;m = number of minutes since start of the hour<br>
> &Delta;s = number of seconds since start of the minute<br>
> &Delta;ms = number of milliseconds since start of the second

To get to the current time in ticks we need to find the proportion of elapsed number of seconds vs the total number of seconds in a day (86400) times the total number of ticks in a day (1000 or 1 kt). We therefor have to divide the number of elapsed milliseconds by 864 (86400 milliseconds in a day vs 1000 ticks in a day).

> &Delta;t<sub>in ticks</sub> = &Delta;t<sub>in ms</sub> / 864

{% include time/dmtt_local.html %}

## DMTT Earth
The above is the local time though. I proposed to have one global time, so conversions are not necessary anymore. The local time has the advantage that you can also do simple calculations like: It's now 750t, so 75% of the day has passed, but it doesn't travel well. If instead we use Greenwich time as the zero point for our earth time, the current time is: 

{% include time/dmtt.html %}

Where the time in the previous clock was different for each region, the above time is the same as other visitors of this page are seeing right now. That means that international train schedules and flight departures/arrivals are not in any local time anymore, but always in the same global unit. It also means that if you want to plan an international meeting or chat you don't have to have the confusion of calling a number of hours too early for example.

This also means that what time of the day corresponds to what amount of ticks is different for each locality. In the netherlands, the sun will rise at 250 and set at 750 as an example.