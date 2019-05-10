---
title: Why you should always use your own VPN
subtitle: or; How to be somewhat secure at your local coffee shop
Description: 
categories: [privacy, security]
canonical:
tags: []
readtime: 
---

**It may just be the frequency illusion, but it seems VPNs and especially VPN providers have become more and more common. When companies like ExpressVPN and NordVPN market themselves as a way to protect yourself and your privacy from hackers in your local coffee shop I always get a bit itchy. It's not the coffeeshop-example that bothers me, it's a classic. And with the new WPA3-standard already proven vulnerable[<sup>1</sup>](https://wpa3.mathyvanhoef.com/) before any major implementation it doesn't look like it will ever get old. But with 'only' a reasonable change your internet traffic will be monitored in any coffee shop, you fully trust these VPN providers with all your internet traffic. What could ever go wrong?**

Firstly, what is the problem at my coffee shop? The coffee is good there, and they have fast wifi! Maybe i get a bit distracted by all the influencers and cat gifs, but who doesn't?

For starters, the wifi protocols have been broken for a long time, and as shown above it probably will not be any different soon. Besides, most coffee shop wifi routers are not set up by the most tech-savvy people around. When i did some freelance IT work a couple of years ago there were way too much routers at these shops without a password or with the default one. Some were even set up to redirect traffic to a different country. Not a great thing to have your financial or medical data intercepted like that, right? And while some might say they don't have anything to hide (More on that in a future post), there are probably some guilty pleasures you would want to keep hidden even from your best friends.

Even with HTTPS now securing a considerable part of the internet, sadly a lot of sites still don't support or redirect to it. As recently as August 2018 more than 48% of the Alexa top 1 million sites still didn't actively redirect to https[<sup>2</sup>](https://scotthelme.co.uk/alexa-top-1-million-analysis-august-2018/#https). And even when sites do support HTTPS, the first request after typing in your favorite website will be over HTTP when the site is not preloaded using HSTS. And a lot of cookies not properly set up to only be sent over HTTPS will be communicated at that first request. So who knows what information was interceptable even though you see the green lock?

Back to the coffee house, illustrated by the graph below. I marked vulnerable parts red, and you may notice the ISP is marked orange here as some caution is to be taken. While this may not be relevant to everyone, one of my favorite past times is travelling, including to destinations with different politics and ethics. And while it's best to always be respectful of local culture, there's always something that could get you in trouble, especially when you think your internet behaviour is private when in fact it's not.

{% include puml.html graph_name="internet-connection-without-vpn" alt="Traffic pattern when not using VPN" %}

So what do these VPN providers actually provide? It's fairly simple actually if you look at the graph below. Your entire communication with your favorite website is encrypted (again) and sent to the provider. The VPN provider subsequently decrypts your traffic and sends it via it's own Internet Service Provider onto the open internet, decrypted this time. When a hacker targets your connection to the wifi router or the wifi router itself, he won't be able to make any sense of the data as he won't be able to decrypt it, so that problem is solved. But there is a new issue here. The VPN provider has full access to all of your traffic, and we also trust that they made a good choice regarding their ISP.     

{% include puml.html graph_name="internet-connection-with-vpn-provider" alt="Traffic pattern when using VPN provider" %}

So what would be the solution here? Well, hopefully you have made a good decision about your home ISP. And I surely hope you have everything set up safely, and you don't have any backdoors in your modem or router. If both are the case, you can use your home internet connection as your single internet entry/exit point. A lot of routers have a built-in VPN server, that you only need to set up. In my case, i have a NAS that hase some free capacity. Make sure to choose a good protocol that is supported by your devices. DON'T use PPTP as it's not encrypted. I would suggest staying away from IKEv2 as well. The best solution as of may 2019 is probably OpenVPN depending on your situation, but please do your own research. Both android and iOS have options to always enable VPN for any connection, both via WIFI and cell service, if you want to be sure your connection is secure you should enable it.

{% include puml.html graph_name="internet-connection-with-personal-vpn" alt="Traffic pattern use of personal VPN" %}

What are your precautions to be safe on the internet?