---
title: You might be sending random people your users' data
subtitle: because of one required input field
Description:
categories: [security, privacy]
tags: []
readtime: 4
---

_Imagine;_

You are working at a help desk or in a physical store signing people up for a service.

You just made a sale, and now you're inputting the personal data of your new customer.
Their first and last name are easy. But wait, they don't have an email address?
The field in the registration form is required, and has to contain a valid email.

{% include img.html src="/images/2023/06/email-required.png" aspect_ratio=38.17 %}

What if we put a random address here? <a href="mailto:user@hasnomail.com">user@hasnomail.com</a>? or <a href="mailto:user@example.com">user@example.com</a>?

The user is signed up (and hopefully happy about their new purchase).
But in the background the **confirmation of this signup was sent** to a **random person** that is trying to intercept your data. Why? And how?

An assumption was made by the creator of the registration form: that all prospective clients have access to email. 
In 2021, only 89% of people in the Netherlands had access to email <sup>[1](https://longreads.cbs.nl/ict-kennis-en-economie-2022/ict-gebruik-bij-personen/){:target="_blank" rel="noreferrer noopener"}</sup>. 

Another assumption by the internal system: That the email address connected to a client is accessible by that client and no-one else.

So a confirmation email is sent:

> Hello John Doe
> 
> 
> Welcome at FooBar! Great to have you as a customer!
> 
> 
> Just to confirm, we have this information about you:
> 
> 
> Name: John Doe<br>
> Phone number: +31 12 34 56 78<br>
> Bank account number: 123 456 789<br>
> 
> With Kind regards,

**Which is read by the attacker waiting for all these emails**

After I heard about a colleague receiving a bunch of emails that were not intended for him on a similar domain name and realising the above scenario, I registered a bunch of domain names:

- [hasnomail.com](https://hasnomail.com){:target="_blank" rel="noreferrer noopener"}
- [heeftgeenmail.com](https://heeftgeenmail.com){:target="_blank" rel="noreferrer noopener"} (Dutch translation of hasNoMail)
- [heeftgeenmail.nl](https://heeftgeenmail.nl){:target="_blank" rel="noreferrer noopener"} (Dutch translation of hasNoMail)
- [email-required.com](https://email-required.com){:target="_blank" rel="noreferrer noopener"}
- ... And a bunch more which I stopped paying for since the low volume of emails

**Together, these domain names received more than 2000 emails per minute** when I started this project, all of them not intended for me and a bunch of them containing personal information.

Now, three years later, after notifying hundreds of companies that number has decreased a lot. And as **a lot of similar domains are not guarded by me so might be actually used to harvest personal data**.

## What should I do?

First and foremost, check if you are affected. If you were sent to this website by me you probably are. Then:

- Don't require an email to register for an offline service.
- Update all incorrect email address in your database. Remove them if possible, otherwise contact your customers for an email address they have access to.
- Instruct personnel to not fill in an email address when a customer doesn't have one. If this is not directly possible because changes in your software are needed, instruct them to use <a href="mailto:foo@example.com">foo@example.com</a> instead as that is a reserved domain name<sup>[2](https://www.iana.org/domains/reserved){:target="_blank" rel="noreferrer noopener"}</sup>. 
- Don't send personal information in emails. If you really have to, only send these emails after you are sure the user has access to it. (Let them verify it's really them reading the emails)

---

## FAQ

### How do I know if my company is affected?

- If it is possible to sign up over phone or in person

AND

- If you are requiring an email address for signing up

You are probably affected.

### How do I know if my personal data is affected?

I don't read or process the emails that are coming in.
The only information that was available to me was the sender domain to notify the company to stop sending me mails.
Therefor I can't supply you with information about if your personal data is or was involved for the domain names that are under my control.
Other domain names might be controlled by malicious users.

### Are there certain kinds of companies that are more likely to be affected?

Yes.

This affects mostly companies that supply a service or product that is not online. For example:

- TV connection
- Cell phone plan
- Internet connection
- Utilities (electricity)

### Are there age groups that are more affected than others?

Yes. As discussed with several companies when notifying them about this issue: As people without an email address are either very young or very old<sup>[3](https://longreads.cbs.nl/ict-kennis-en-economie-2022/ict-gebruik-bij-personen/){:target="_blank" rel="noreferrer noopener"}</sup>, and most bigger purchases are done by grown ups, this affects mostly elderly.
To make even worse, this is also a demographic more affected by scams<sup>4</sup>

### Do you have access to all this personal data?

As I am the owner of several of these domains, I theoretically have the ability to read these emails.
As this data is personal, I don't look at it for ethical reasons.

In the past, I have set up automatic processes to gather statistics about the _sender domains_.
This was used to contact the companies and notify them about this issue.

You don't even have to believe me on my blue eyes. Right now, all DNS records related to email are removed so all automatic processing is disabled.
The only DNS settings that are still there are related to the hosting of the redirects to this domain.

### Why are you doing this?

I am a software developer who also likes to think about security.
The domains are solely under my control to mitigate the issue and prevent others from registering these domains with malicious intent.

### Why are you publishing now?

I thought about publishing this when I first registered all these domain names. But when things like these get publish, besides making the companies aware malicious people also get aware, and can register a bunch of similar domain names. But after talking with several security professionals and agencies over the past few weeks, it became clear that publishing was better than keeping this silent.

### How are you paying for this?

I am paying for these domain names every year out of my own pocket. That's also the reason that I stopped paying for the other 25 domains that got very minimal email traffic compared to these four remaining domain names.

If you want to sponsor me you can do so using the button below on Github:

<iframe src="https://github.com/sponsors/PrinsFrank/button" title="Sponsor PrinsFrank" height="32" width="114" style="border: 0; border-radius: 6px;"></iframe>
