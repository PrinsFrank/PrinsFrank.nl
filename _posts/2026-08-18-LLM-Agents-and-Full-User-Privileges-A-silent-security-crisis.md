---
title: LLM Agents & Full User Privileges. A silent security crisis
subtitle: Abandoning all security principles to "not fall behind"
Description:
categories: [security]
tags: []
readtime: 5
---

The internet used to be a very insecure place. Cookies used to be transmitted cross-site. HTTPS wasn't a common occurrence until 10 years ago. JavaScript had access to more system resources than it should have, and browser permissions were immature. If you wanted to find vulnerabilities in web applications, it was relatively easy. Gladly, a lot of recent developments improved on that. Browsers started protecting users. Governments enforced protection of data. And companies were starting to implement Responsible Disclosure programs to allow white-hat hackers to report vulnerabilities, often rewarding them with a small sum of money. For a few years, it looked like the internet was growing up. Security was changing from an afterthought to a requirement.

That momentum shifted with the introduction of LLMs. Suddenly, people without a technical background had access to tools to build (web) applications. Vibe coding and "Agentic Engineering" became a thing. Tools that require some setup and understanding could now be set up in an instant by "asking" a chatbot to do it for you. But part of that setup and understanding was needed to make sure security is still part of the equation. You want a backend to store data? A Supabase database is set up for you in an instant. Don't mind that all the data you wanted to store privately is now public. AI companies might say it's a "user error" because you should have asked your LLM to "make it secure" and "make no mistakes".

LLMs also started to be used by professional developers. Some developers were curious about these new tools. Others were forced to use them by higher-ups that were excited about the 10x efficiency promised by the AI companies. Companies that have stated for a few years now that developers will be replaced within 6 months. We're still 6 months out.

---

The claims by these AI companies are a common thread throughout this series. Because they're not made without a specific motivation. These companies are hemorrhaging money. And they're afraid that the 'cheap' money they're borrowing is disappearing. That another company will become more successful than them. That we're on top of the hype cycle. And that the bubble will pop.

Eventually, these companies will have to start making money. But for now, we're still in the low-balling stage of the Uber model. A strategy to undercut the market in the hopes of outlasting competitors. This is currently keeping token costs artificially low. Any increase in pricing will result in users switching to competitors.

> Users are billed based on number of tokens consumed. Think of tokens as characters, words, and parts of words. The number of tokens in a generated piece of text is roughly similar across LLM providers, making cost-per-token a useful benchmark.

---

With each new model released, the output got slightly better. But the size of the model is not proportional to the quality of its output. A model double the size might give only a marginally better output. Scaling by itself wasn't the answer. So why not give it tools? To browse the web, run code, read files. And do it all with the same permissions as you. Agents were born. Nice side effect for these companies: they also use more tokens!

---

A big part of the maturing of the internet was that the principle of "least privilege" got more widely standardized. People got access to only the things they needed access to. Role-based access permissions got widely implemented as a requirement for enterprises. So did 2-factor authentication. Password reuse got discouraged, because now password managers provided automatically generated passwords, and for enterprises Single-Sign-On allowed secure authentication. All of this effort was put into making sure that only the user that was authorized could do certain things, and that the user was really who they said they were. The entire internet was working in a shared understanding: Limit access, verify identities and assume breach.

The early internet also placed a lot of trust on user input. But those users can be malicious. Cross-Site Scripting, SQL injections, and many similar attacks were possible because input from untrusted sources was improperly validated or sanitized.

---

All of these learnings seem to have been 'forgotten' with agents. Many agents use "blocklists" instead of "allowlists", so they can perform destructive actions by default until disallowed. They have the same access as users that they're installed for. They act like they are the user, and even 'are' the user as far as the system is concerned. Content scraped from web pages is being used as input, making prompt injections an inherent risk. 

While this seems like 'forgotten' practices, realistically, omitting standard security practices in agents means faster release cycles and outpacing competitors. Otherwise, these companies might lose and 'fall behind' themselves.

---

One might think: But I don't have anything to hide! Remember when Facebook intercepted all my internet traffic in exchange for a $20/month gift card? I don't care!

That was just your internet traffic they could read. These agents can do much more. They have access to all the passwords in your browser. Did you blocklist access to that file? An agent will happily create a bash script and execute that to work around that restriction. That funny internet joke that told you to execute a command that wipes your entire PC? You never executed that because you were smart enough not to. An agent will happily execute that if it encounters it in the content it processes. 

---

LLMs are trained with reward functions. Sycophantic answers result in you using the model more. Is the model blocked by something? It will attempt to work around it to produce a result, even if its side effects are destructive. You asked it to fix a broken page. Resetting the database also fixed the issue, but all your data is now also gone.

Agents are not the answer, at least not to the question that matters. AI companies are asking "How do we let people burn more tokens?". You should be asking "Where does this end?"

---

This is part 2 of my deep dive into LLMs. [In part 1 I looked into privacy implications for these models](/2026/07/09/LLMs-the-ultimate-corporate-surveillance-tool)
