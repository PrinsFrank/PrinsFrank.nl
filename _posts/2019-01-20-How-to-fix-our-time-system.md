---
layout: post
title: How to fix our time system
subtitle: Proposing Decimal Metric Time in Ticks
Description: Where our current time system is leaving much to be desired, i am taking a deep dive in what is wrong and how we can fix it. I am proposing an alternative with Decimal Metric Time in Ticks.
categories: []
canonical: https://medium.com/@prinsfrank/how-to-fix-our-time-system-2a4cedb8260b
tags: [science, future, innovation, world, time, proposal, metric system, decimal, standards]
readtime: 5
author: Frank Prins
---

![St Joseph's cathedral hanoi](/images/2019/01/header_time_system.jpg)
_St. Joseph’s Cathedral in Hanoi at 12:07_

**Most of us have mastered telling time by now. It is such a basic concept that it existed before the invention of the wheel. Our current time system with multiples of 24 and 60 dates back to ancient Egypt and Babylonian time and hasn’t changed a lot since. But why does a day divide into 24 pieces, hours and minutes into 60? And can we improve on that?**

---

Most of the modern world uses the Hindu-Arabic number system where a combination of 10 numerals and their position represent a number. A logic consequence is that our systems of measurements are decimalised too with the best example being the metric system. Simon Stevin, a Dutch-Flemish Polymath first came up with decimal number systems for everyday use in his booklet “De Thiende”, but it took a revolution in France before the metric system was born. France even used metric time for a while before reverting back to the 24 hour system. So why didn’t metric time catch on? And isn’t the fact that we still use our current time system obvious enough?

---

Part of the problem may be the reuse of already agreed upon unit names. The French system used decimal “Seconds”, “Minutes” and “Hours”, which has to have been confusing in conjunction with “normal” seconds, minutes and hours. And there’s already an international standardized system, so why change things up? There has to be a good reason to change something so universally used. Actually there are more than one:

---

## Why our time system is broken
To describe a specific second in a day we need 6 numerals in our current system. 2 For the hour, minute and second of the day each. As we all know the first second of the day is represented by 00:00:00, the last one by 23:59:59. For the hour indication we only use 24 of the available 100 combinations, for the minute and second we use 60 of the available 100. That gives us a total efficiency of the available character space of only 8.64%! `(0.24*0.60*0.60)`. If we use the AM/PM system where the last second of the day is 11:59:59 PM the efficiency drops to only 0.01% `(0.12*.6*.6*(2/(26*26)))`.

Adding and subtracting minutes and seconds is harder than it could be as well. If someone says something will happen in exactly 45 minutes for example, and the current time being 15:23, instead of one calculation taking place there are two! 23 And 45 make 68, minus 60 for an hour makes 6. In a fully decimal system the digit would already have shifted with the first addition and we wouldn’t need the following subtraction.

---

## Considerations for a better system
With a better time system we need to consider a few things. A day has to keep it’s current length so we can add and subtract days and get the same time on a different day. We also need to have parts of a day where a multiple of ten makes up a full day, so this is a metric system. The name of the base unit of such a system should also be not colliding with the current time system and should be catchy and very usable for everyday live but also for both astronomical and quantum time scales.

---

## Decimal Metric Time in “Ticks”
Our current definition of day consists of 86400 seconds. I am proposing that a day should consist of a different unit — a “tick” — so not to confuse it with the existing second but also using a familiar term. Because of the metric naming system that has names for 10-³ all the way up to 10³ but leaves gaps of two factors of ten after that, we need to define a tick to be in a usable sweet spot so we don’t need names outside of that range in our everyday live.

That logically leaves us with a that day should be exactly a kilotick or “kt” long. The length of a tick is then the same as 86.4 seconds, almost 1,5 minutes. Should you want to express a shorter time you can use a decitick for 8.64 seconds, a millitick for .864 seconds. Scientists have a lot of room left for much smaller or bigger numbers while our everyday time units are very logical and every factor of 10 has it’s own name.

We can now express any part of the day with as many digits precision as we want. If we talk about noon we can just say .5 day, 5ht or 500 t. If we would want to express 1 millitick before midnight we can note that as .99999 day or 999.99 ticks. With one digit less —And without the AM/PM indication — we can express a specific time of the day with greater precision than a second and an efficiency of a 100%!

Adding and subtracting numbers is also no hassle at all anymore. Multiplication has never been easier and conversions between different multiples and submultiples is just a matter of moving a dot.

---

## International System of Units (SI) redefinition
We have one thing left to do; The international standard unit for time is the second, but when the Decimal Metric System in Ticks takes over we need to redefine the time standard in ticks. Luckily this is relatively simple. Before 1967 the second was defined by a factor of a day or a tropical year, but since then it is defined as “9,192,631,770 periods of radiation corresponding to the transition between the two hyperfine levels of the ground state of the caesium-133 atom at a temperature of 0 K”. If we add the definition of a tick to the SI we can simply define it as 794,243,384,928 of the same periods. We also need to redefine several of the base units based on this definition as they are dependent on the definition of the second, but that is out of scope for this article.

What do you think? Should we convert to Decimal Metric Time in Ticks and why / why not?