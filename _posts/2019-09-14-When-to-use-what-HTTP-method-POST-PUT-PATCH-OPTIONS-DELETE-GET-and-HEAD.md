---
title: When to use what HTTP method.
subtitle: A guide on POST, PUT, PATCH, OPTIONS, DELETE, GET and HEAD
Description:
categories: [development, standards]
tags: [HTTP, POST, PUT, PATCH, OPTIONS, DELETE, GET, HEAD]
readtime: 
---

The last couple of years i've talked with multiple developers about proper use of HTTP methods.

One of my biggest pet peeves when it comes to improper use of HTTP methods is only using GET or POST requests, which subsequently results in endpoints with paths like "/user/update" where the ID is pulled from the request body. If only some smart people thought about semantic HTTP methods already. 

In [RFC 7231](https://tools.ietf.org/html/rfc7231) and [RFC 5789](https://tools.ietf.org/html/rfc5789) all of the ones below and some more are defined, but totaling at 111 pages it's not the easiest starting point. Frequent readers of my posts have probably noticed my love for making charts, and that's exactly what i did. This decision chart should help you on your way.

{% include puml.html graph_name="HTTP-methods-overview"  alt="An HTTP method decision chart" aspect_ratio=68.92 %}

You're free to update or redistribute this chart, the plantUML-file can be found [here](https://github.com/PrinsFrank/PrinsFrank.nl/blob/master/graphs/HTTP-methods-overview.puml). Please submit an issue or pull-request if I've missed anything!  