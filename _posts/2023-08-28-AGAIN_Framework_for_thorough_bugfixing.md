---
title: A.G.A.I.N.‽ Framework for thorough bug-fixing
subtitle: A deep dive
Description:
categories: [standards, development]
tags: []
readtime:
---

**_Again?_ This one word might be said too much by your users, customer support staff or developers and can say a lot about the quality of your bug fixes. Or you are just looking for a framework to guide your efforts in squashing bugs. Either way, you came to the right place!**

When working on a highly volatile application, changes happen quickly and often. Especially when working with a lot of people and an ever-growing application, that usually means that things break in the process; Bugs. 

> The A.G.A.I.N.‽ acronym was chosen to represent the 5 levels of bugfixing. The word "AGAIN" was chosen as a mnemonic device similar to "SOLID" and "KISS". Letters are seperated by a dot(.) and the acronym is closed by an interrobang(‽) to indicate inevitable frustration.

How easy it is to solve a bug differs per type of environment, but generally bugs cost a lot of money. The initial thought about bug costs might be about finding, reporting, debugging and fixing the bug. But a developer that is working on that debugging and fixing is also not working on other projects - the cost of opportunity. Add to that the time for context switching, costs for extra support staff, project managements for bug organization, loss of customers and productivity lost, the direct bug fixing effort is just the tip of the iceberg.

Most bugs get treated similarly: A workaround is communicated or one fix is applied for the _Actionable Bug_.

Typically, A bug is an annoyance to developers. They just want to crack on building that shiny new feature they have been working on. And when the _Actionable Bug_ is fixed, that's it. Once a project gets bigger, the amount of bugs generally increases though, and with the age of a product usually the percentage of time spent on fixing these bugs also increases. In the long term, this is not an efficient way of solving bugs. Every time a lot of effort is put into finding out what is happening, which is the most time intensive part of bug-fixing. Instead of just fixing the bugs, it makes sense to more structurally approach resolving defects, which is covered by this framework.

## **A**ctionable Bug

Solving the _Actionable Bug_ is usually the only step in the framework consistently taken to resolve an issue. Here it is the first of five. 

### Singular cause

Sometimes it is possible to point at a single line of code or a set of characters and say: "This is the cause of the bug". The _Actionable bug_ will have a simple fix in this case, and as the cause is already found when this statement can be made, applying a fix will be as easy as changing the problematic code. 

### Multi-faceted cause

Other times, the cause of a bug is a set of circumstances in the context of business logic. In this case, one bug might have multiple fixes, each of which individually solves the defect but applying a set of changes fixes the problem more thoroughly. As we want to kill current bugs but also prevent future bugs, applying a set of changes to fix the problem more thoroughly is preferred.

## **G**lobal Occurrences

After we fix the _Actionable bug_, often the bug is regarded as fixed and work is resumed on other tasks. The reported bug might not be the only occurrence of the issue though, and our house might be crawling with more bugs of the same species.

When fixing _Global Occurrences_, we look for bugs of the same kind, and fix them within the scope of the current bug to prevent repeatedly fixing the same bug in different parts of the codebase.

With a method or function that returns a (previously) unexpected value, that might be as easy as looking at all the usages of that code and checking if all the occurrences can deal with the unexpected value. If more occurrences are found that don't deal properly with the newly discovered scenario, those are fixed within the scope of this step.

With logical patterns, this might be more difficult. Personally, a solid understanding of regexes and a powerful IDE helps a lot here, but implementing some checks in _Analysis & Visibility_ might give some tools here as well.

## **A**nalysis & Visibility

If we have fixed the _Actionable bug_ and fixed _Global Occurrences_, the most annoying thing for everyone involved that can happen is that the bug gets reintroduced. As such, we assume that it will and make sure that in that case the reintroduction gets detected. 

Preferably bugs don't ever get reintroduced to production, so we implement some sort of protection preventing that from happening. There are several tools available in most languages to detect common bugs or that allow to write custom checks. These tools and checks can be run in merge-blocking pipelines so that issues are detected before they can be deployed.

If it's not possible to detect these issues before they occur we at least make sure that if the same scenario happens again, we know about it before our users do. Issues that can't be prevented using static analysis, at the build stage or by other tools and only happen at runtime, should always throw clear exceptions or write readable logs, and those exceptions or logs should be actively monitored, so that when an issue occurs it is fixed before a user can report it.

## **I**nsights

Most bugs are not self-contained, and might be the root cause of other reported bugs or might be the result of another bug higher up. When gaining _Insights_, we look at the bug in the greater context of the product. It might be a good idea to ask domain experts, customer support or colleagues about the issue that was found, and try to understand possible interactions with the rest of the product.

Any related issues that are discovered during this phase are linked if the used issue-tracking system allows so, otherwise a comment is added so the future self or colleagues gain more insight, and are informed that the issue might be solved or related.

Usually, a network of issues can be discovered that are closely coupled to the issue at hand, even sometimes gaining new insights in how to solve the issue even more thoroughly.

If the issue was already on the radar outside an issue tracking system but in a performance monitoring tool, a logging tool or some other tool and insights were already available, some research is done into why the issue was not already detected and/or fixed.

If there are no such insights and no history related to the bug, an effort should be made to provide more context for future reference. 

## **N**vironmental

_Nvironmental_ is an intentional misspelling of _Environmental_ to make the mnemonic work. At this stage we reflect on what fixes we applied, and take one last step back. "Why did this bug occur?" Is the final question we ask ourselves, and the answer to that question should be our final fix.

Development is hard, and developers are working in a fast and volatile environment. When the bug has been present for years, it might not make sense to reflect on the Environmental factor here as it might not be possible to know in what environment the bug was introduced.

When it is possible to reflect on the circumstances, we look at the entire picture: Is there a lot of pressure to deliver fast? How high is the workload? What is the mood of Developers? Are there workflows in place that hinder productivity? What are processes involved and can they cause issues like this? Is there a knowledge gap that can be fixed?

Of course these questions are not exhaustive. There are a plethora of reasons that can affect a bug getting introduced or not. The point here is not to tick of a list of questions and try to solve these specifically, but get into the mindset of the team at the moment the bug was introduced and see if we can change the environment to reduce the chance of a similar issue getting introduced in the future.

---

## Definition Of Fixed

When discussing or implementing this framework, it makes sense to write down criteria to which a complete fix must adhere to. This can be done in a similar way of committing to a Definition Of Done or Definition Of Ready, by creating a Definition Of Fixed.

A Definition Of Fixed can contain some of the following points:

<input type="checkbox" disabled checked> (A) All causes of the bug have been fixed.

<input type="checkbox" disabled checked> (G) All similar constructs to the cause have been investigated and where applicable also been fixed.

<input type="checkbox" disabled checked> (A) Measures have been put in place to prevent similar issues occurring in the future, or if not possible detect and fix them more easily.

<input type="checkbox" disabled checked> (I) The context of the bug is understood, and documented for future self and others.

<input type="checkbox" disabled checked> (N) Any contributing Environmental factors at time of introduction are understood, documented and where possible mitigated.

---

## Epilogue

The framework detailed above is just a written down version of a systematic way of solving bugs thoroughly. These steps are definitely not exhaustive, and will probably be updated in the future is this is not a blog but a bliki. If you have any additions, please open a PR on this post!
