---
layout: post
title: "Open standards won’t save you from vendor lock-in"
description: "...or at least, they won’t as often or as thoroughly as advertised."
longdescription: "In the general case open standards in identity won’t prevent vendor lock-in and they ought to be table stakes says Auth0’s Vittorio Bertocci."
date: 2018-06-21 10:30
category: Hot Topics, Authentication
author:
  name: Vittorio Bertocci
  url: https://twitter.com/vibronet
  mail: vittorio.bertocci@auth0.com
  avatar: https://cdn.auth0.com/blog/team/Vittorio.Bertocci.jpg
design:
  bg_color: "#00061F"
  image: https://cdn.auth0.com/blog/open-standards-vittorio/logo.png
tags:
- growth
- awards
- security
- entrepreneurship
- cybersecurity
- infosec
- identity-management
- idaas
- identity
- b2b
related:
- 2018-05-01-auth0-welcomes-vittorio-bertocci
- 2018-05-23-introducing-the-mfa-api
- 2018-03-02-open-auth-standards-psd2
---


Open standards in identity are a good thing. An excellent thing. They’re one of the few aces in the sleeve we have in our industry for keeping complexity under control. When it comes to selecting an open source SDK, assessing whether two or more solutions can collaborate, decomposing problems and requirements into well-known patterns and units of meaning that act as a common language the industry can use to communicate and make progress — open standards are the social norm that enables all that and more.



But it’s time for industry vendors to stop bragging about their support for open standards. 


For one, ***open standards are now table stakes***: Do you hear wearables suppliers brag about their support for Bluetooth, or refrigerator manufacturers boast that they can plug in your country’s power grid without adapters? *Of course* they support Bluetooth and come with standard power cord — that barely deserves a mention on the package — the battle for winning your favor is fought on completely different features. Positioning support for bleeding edge standards like token binding or WebAuthn as a differentiating factor is indeed informative, but in the year 2018 support for SAML, OAuth2, and OpenID Connect should be a given. 

{% include tweet_quote.html quote_text="Use of core identity open standards should be considered table stakes, says @auth0’s @vibronet." %}


But there’s a subtler point, an epiphany I myself had only recently. Contrary to what’s commonly boasted by providers, in the general case ***open standards in identity won’t prevent vendor lock-in***.

Can open standards help to decouple you from vendor-specific considerations? You bet they can — but only for the most basic entry points in their ecosystem. When considering solving the problem of identity and access management, it’s tempting to focus on the mechanics of authentication — and once that phase is solved, consider the matter closed. If you can get a JWT from vendor A using an OpenId Connect OSS SDK, you just proved to yourself that you can likely use the same SDK to repeat the process to get a token from vendor B — hence you feel locked to neither. Think about it for a moment longer, tho. Once the demo is done and all the hands have been shaken, your developers will start working with the vendor of choice and the tokens they’ll request won’t be for proving a point —they’ll be for invoking API, which in turn represents programmatic access to data and functionality. If you look at the reality of the industry today, you’ll see that very often the vendor supplying you with support for OpenId Connect & Co is also the owner of the API you are using to access data and functionality (documents, storage, calendars, inboxes and the like, plus any hosting/serverless capabilities). 


Imagine your team fully embracing those API for say a couple of years, going for the deep integration that Mr. Bezos describes [here](https://www-geekwire-com.cdn.ampproject.org/c/s/www.geekwire.com/2018/jeff-bezos-amazon-web-services-lock-never-want-customers-trapped/amp/), then deciding to switch to a new vendor. Sure, the use of OpenId Connect means that adapting your clients to requests tokens from a different source should amount to a simple string update; that’s vastly better than if you’d have to switch between proprietary systems … which incidentally, is exactly what you’ll have to do to migrate all the assets you created using the data and functionality API which are downstream from the token acquisition, use, and validation phases. Which migration task do you think will bring the highest costs and complexity, hence creating those inhibitors traditionally associated to vendor lock-in? 


To be clear, I am not saying that there’s anything wrong with the above; in fact, most vendors go out of their way to make your data exportable and help with transitioning it elsewhere if you so choose. The only thing I take exception with is the statement that a vendor using identity open standards will avoid vendor lock-in, as per the aforementioned argument identity is only a fraction of the things that you’ll most likely take a dependence on from a vendor’s offering.

{% include tweet_quote.html quote_text="Find out why @auth0’s @vibronet says open standards alone in identity won’t prevent vendor lock-in." %}

Now, with all that said. There is a notable exception in which the use of open identity standards won’t lead to vendor lock-in, and it occurs in the special case in which the vendor being considered only focuses on the identity aspects of the development of your solution. In that case, there is no upsell agenda! You already know where I am going with this: Auth0 falls exactly into that category of “pure breed” providers. If a vendor only focuses on helping you to easily implement standard-based authentication flows, if that’s the only thing you are paying that vendor for, then by definition, it will be easy to use the very same standards to point your applications to a different supplier. That’s not to say that the scenario prevents the solution from *also* taking dependencies on specific features — not every aspect coming into play in identity solutions is codified in form of standard —however, it is safe to say that “pure breed” identity vendors such as Auth0 come much closer to fulfilling the promise of sparing you from hard vendor lock-in. 


Open standards in identity are an excellent thing. So good, in fact, that I propose that by now use of core identity open standards should be considered table stakes, a commodity offered by everyone rather than something to brag about —along the lines of what Carr discussed in his seminal article [IT doesn’t matter](https://hbr.org/2003/05/it-doesnt-matter). Moreover, vendors should articulate the advantages of open standards without hyperbole — they carry enough advantages on their own without having to attribute them magical properties such as the ability of single-handedly avoiding vendor lock-in, without specifying the special conditions (pure identity vendor) where that boast is almost true. 

{% include asides/about-auth0.markdown %}
