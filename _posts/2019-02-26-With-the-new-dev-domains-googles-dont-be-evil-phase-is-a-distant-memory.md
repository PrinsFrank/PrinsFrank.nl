---
title: With the new .dev domains Google's "Don't be evil"-phase is a distant memory
subtitle: The dystopian reality of a monopolized internet
Description: 
categories: [privacy, standards]
canonical: https://medium.com/@prinsfrank/with-the-new-dev-domains-googles-don-t-be-evil-phase-is-a-distant-memory-34a4db39e41c
tags: [future, google, tld, privacy, DNS]
readtime: 4
---

> The choice for mankind lay between freedom and happiness, and, for the great bulk of mankind, happiness was better. <sup>1984 - George Orwell</sup>

**In December 2017 Google made some developers upset with releasing chrome version 63 by breaking their development environments. Three months later the same breaking change was made in firefox. Now - more than a year later - the change that resulted in thousands of upset developers worldwide can be reverted by purchasing the affected *.dev-domain from google. But why is Google now expanding to selling domain names, and why do i think this a bad thing?**

## DNS - The backbone of the internet
To fully understand the significance of what happened we need to go back some years. With the predecessor of the internet - ARPANET - somewhat grown up, it became clear that the implementation of keeping one list of all hosts and their corresponding ip-addresses centrally at the NIC was not sustainable anymore. It was agreed upon that the Network Information Center would supply this master list to any secondary volunteer hosts[<sup>RFC625</sup>](http://www.rfc-editor.org/rfc/rfc625.txt){:target="_blank" rel="noreferrer noopener"}, but after ten years of growing pains between 1973 and 1983 it was clear there was a solution needed. The hosts file was becoming larger, updates became more frequent and as the network expanded more hosts were copying the file every night.
>The conclusion in this area was that the current "user@host" mailbox identifier should be extended to "user@host.domain" where "domain" could be a hierarchy of domains.[<sup>RFC805</sup>](http://www.rfc-editor.org/rfc/rfc805.txt){:target="_blank" rel="noreferrer noopener"}

A new system was proposed and implemented, eventually growing into our current system. A simplified graph of how a user can type in a user friendly domain name and get the resulting web page can be seen below;

{% include puml.html graph_name="inner-working-dns-simple" alt="Inner working DNS graph"  aspect_ratio=43.49 %}

As you can see quite a few different servers are involved with fetching the correct ip address for a domain name. Most servers along the way will cache the results, so when you request a domain name probably not all these request will actually take place.

## Why is google registering Top Level Domains?
After Amazon withdrew their application for the .dev TLD at ICANN back in July 2014, Google was the only applicant remaining[<sup>1</sup>](https://icannwiki.org/.dev){:target="_blank" rel="noreferrer noopener"}. Probably as a result of an assumed private deal or auction Google became the official owner as of December 18<sub>th</sub>, 2014[<sup>1</sup>](https://icannwiki.org/.dev){:target="_blank" rel="noreferrer noopener"}. But why their interest?

If you look carefully at the above graph - specifically at the parties involved - maybe you'll notice something. At almost every step of this process google has some presence. Not even in this graph are android and chrome OS, both developed by Google, for a lot of people their first contact with Google when requesting a web page. Subsequently users will open their browsers. Google Chrome is the most well known baby of the aforementioned company, but Opera and a lot of other browsers are also based on Chromium, as will Edge starting from 2019[<sup>2</sup>](https://blogs.windows.com/windowsexperience/2018/12/06/microsoft-edge-making-the-web-better-through-more-open-source-collaboration/){:target="_blank" rel="noreferrer noopener"}. Googles DNS recursor service 8.8.8.8 has gained more than 10%[<sup>3</sup>](https://security.googleblog.com/2018/08/google-public-dns-turns-8888-years-old.html){:target="_blank" rel="noreferrer noopener"} of the worlds users relying on its services after its introduction in december 2009[<sup>4</sup>](https://googleblog.blogspot.com/2009/12/introducing-google-public-dns.html){:target="_blank" rel="noreferrer noopener"} and is the next step when requesting a web page. A lot of websites rely on the authoritative name server services and hosting services by google, but the _one_ place where google has been underrepresented is Domain Names.

Google launched Google Domains back in January 2015, their first step into Domain name sales reselling other Top Level Domains. In the same year google launched Google Registry [<sup>5</sup>](https://web.archive.org/web/20150929033350/https://www.registry.google/){:target="_blank" rel="noreferrer noopener"} As of February 26<sub>th</sub>, there are 1532[<sup>3</sup>](https://data.iana.org/TLD/tlds-alpha-by-domain.txt){:target="_blank" rel="noreferrer noopener"} Top Level Domains in existence. I can't seem to find any exact numbers on the amount of Top Level Domains owned by google, but is a relatively large portion of that number.

With over 400.000 .app domains registered since it first became available it's becoming harder and harder to avoid google on the internet. With the overall excitement within the developer community i don't doubt that the launch of .dev domains will be a huge success as well. Not only in registering the actual domain names but also in pulling developers more into the google ecosystem.

## So what? What is the big deal? 

>We know that no one ever seizes power with the intention of relinquishing it. Power is not a means, it is an end. <sup>1984 - George Orwell</sup>

As is public knowledge, google's _primary_ business model is selling advertising by delivering **relevant** ads to users. With more data about those users - you and me - ads become more relevant. When the internet was not all about a couple of social media all reposting eachother's memes it was very easy to not come in contact with the data hungry companies spying on your every move. As more sites are moving away from .com domains for example - those managed by countries - more power is transferred to private companies like Google. I will not quote spiderman here, but the responsibility that should be taken by especially the most powerful company in the world is not. In fact, Google removed their famous "Don't be evil" motto back in early 2018. Every day the internet becomes more centralized and further removed from the ideological image the pioneers envisioned. We are now in the dystopian reality of a centralized internet. Welcome to the 21<sub>st</sub> century, also known as the age of Google.

> The choice for mankind lay between freedom and happiness, and, for the great bulk of mankind, happiness was better. <sup>1984 - George Orwell</sup>

february 26<sub>th</sub>, 21 AG <sup>(After Google)</sup>