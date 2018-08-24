---
layout: post
title: Use Auth0 Tools To Securely Manage Your User Database
description: "Multifactor authentication and social login are just two of several tools that Auth0 offers on its platform to guard its customers against breaches."
date: 2018-08-17 08:30
category: Business, Identity, User Database
is_non-tech: true
author:
  name: Martin Gontovnikas
  url: http://twitter.com/mgonto
  mail: gonto@auth0.com
  avatar: https://www.gravatar.com/avatar/df6c864847fba9687d962cb80b482764??s=60
design:
  bg_color: "#53116A"
  image: https://cdn.auth0.com/blog/auth0-tools/use-auth0-tools-to-securely-manage-your-user-database-logo.png
tags:
  - iam
  - digital-transformation
  - auth0
  - users
  - mfa
  - outsourcing
  - identity-management
  - saas
  - social login
  - database
related:
  - 2018-03-27-strong-identity-management-system-eases-transition-to-hybrid-cloud
  - 2018-03-09-3-reasons-your-data-integration-plan-is-important
  - 2018-05-21-digital-success-through-customer-identity-and-access-management
---

The number of attacks on individuals' data is astounding. From the [Equifax breach](https://www.washingtonpost.com/news/the-switch/wp/2018/03/01/equifax-keeps-finding-millions-more-people-who-were-affected-by-its-massive-data-breach/?noredirect=on&utm_term=.4bbb572bb730) that continues to worsen to attacks on high-tech companies like [Uber](https://www.wired.com/story/uber-paid-off-hackers-to-hide-a-57-million-user-data-breach/) — it seems as if no one is immune to cybercrime. 

Several common threats exist to web apps:

* XSS (cross-site scripting), when malicious scripts are injected into otherwise trusted web applications
* Clickjacking, which happens when an attacker introduces transparent or opaque objects into your web application
* Cross-site request forgery, in which an attacker impersonates an authorized user and makes requests on their behalf, like resetting a password.

And it doesn't stop there. These threats are constantly evolving. As soon as IT teams and experts root out dishonesty,  it seems like the criminals pivot to find a new path. 

So what can you do, now, to secure your user database in this difficult landscape?

{% include tweet_quote.html quote_text="What can you do to secure your user database when there are so many threats like XSS, Clickjacking, Cross-site request forgery, etc?" %}

This piece will offer concrete tools and approaches for keeping your customers safe and help you build a solid technical foundation on which to scale. 

## Why Is Your User Database At Risk?

A major reason that user databases are at risk is inherent software vulnerability. For example, Apache servers have historically been exposed to the [Heartbleed](http://heartbleed.com/) bug. Heartbleed is unique to OpenSSL (the data encryption technology used by a huge chunk of the internet) and allows attackers to intercept vulnerable information between one's browser and computer. 

If you're entering a password or payment information into a browser, for example, although this data will be encrypted during the transfer to the computer on one level; a second connection, often described as a “heartbeat” between the browser and computer can be intercepted.

![The heartbleed bug](https://cdn.auth0.com/blog/auth0-tools/the-heartbleed-bug.png)

([Source](https://www.youtube.com/watch?v=WgrBrPW_Zn4))

The Heartbleed bug manipulates the secret keys that the browser and computer use to encrypt the names and passwords of the users, along with other sensitive content. In vulnerable versions of OpenSSL software, hackers can break in and steal data directly from the services and users. You can read more about it on the [Heartbleed website](http://heartbleed.com/). 

### Why Not Just Patch The System?

If you find holes or vulnerabilities in your system, why not just fix them up and continue with business-as-usual? 

First, taking a user database offline creates downtime for a business. This can result in frustrated users who enjoy and rely on your service. Down the line, it can lead to a loss in revenue. 

Patching software is also risky. Particularly if you work with a lot of sensitive customer information, such as [financial transactions](https://auth0.com/blog/how-two-factor-authentication-can-help-financial-institutions-reduce-data-breaches/), making changes to a software library can break critical processes, leaving your users out to dry with payments incomplete and exposed. 

In some dire cases, such as a medical facility, it could truly be a matter of life and death if your system, full of patient data, shuts down (even temporarily). 

## A Better Solution: Secure Your User Database With Auth0 Identity Tools

Outsourcing your cybersecurity needs, particularly for a user database, can be an excellent solution. Only a few teams have the in-house capability to build and customize cutting-edge tools without support. Particularly if your company is constantly growing and changing as it should — you need to both brace your systems against attackers and make them flexible enough to allow you to scale. 

Auth0 has [several tools](https://auth0.com/why-auth0) to help you do this.

### Content-Aware MFA Technology

Amid the many features that Auth0 offers customers on its universal authentication and authorization platform, [multifactor authentication (MFA)](https://auth0.com/docs/multifactor-authentication) helps ensure that only the right users gain access to your system. Even if a hacker obtains a user's login credentials, s/he still needs a second piece of information, such as the user's thumbprint or mobile device, to make it through. 

It's simple to add MFA to your app. For example, if you want to add the Google Authenticator, which provides a six to eight digit one-time password to users as an alternate token, you can turn this on by first visiting Auth0's [Multifactor Auth](https://manage.auth0.com/#/guardian) page from the dashboard. 

![Auth0 multifactor authentication feature](https://cdn.auth0.com/blog/auth0-tools/auth0-multifactor-authentication.png)

If you want to use a different provider like Duo, just click on the link to switch. You can then select if you want your users to receive the password via a push notification or SMS. 

While MFA is proven to reduce breaches of user databases, few organizations have successfully integrated it. Why? Making the 2FA setup simple for customers is a challenge. Even advanced services like [PayPal](https://www.paypal.com/us/smarthelp/article/how-do-i-enable-2fa-(two-factor-authentication)-for-my-paypal-powered-by-braintree-user-faq3500) have made users opt-in through a lengthy process that entails their first logging into a control panel, navigating to their account, and then independently enabling 2FA with a QR code or another supported app.

Auth0 can help you figure out ways to integrate and communicate MFA capabilities for your users in a more seamless and self-aware way. Visit our [business blog](https://auth0.com/blog/business/) for further resources.

### Social Login Integrations

You can also build robust profiles of all of your users using Auth0's [social login functions](https://auth0.com/learn/social-login/). Social login allows users to register and access your app by connecting via a provider like Facebook.

Auth0 supports over 30 social providers, including Facebook, Twitter, Google, Yahoo, Windows Live, LinkedIn, GitHub, PayPal, Amazon, VKontakte, Yandex, Basecamp, Box, Salesforce, and more.

![Auth0 social login integrations](https://cdn.auth0.com/blog/auth0-tools/auth0-social-login-integrations.png)

In addition, you can add any OAuth2 Authorization Server you need. To enable social login, just click Connections and then Social in the Auth0’s Management Dashboard, then flip on the switch of the social network provider you'd like in the app(s) of your choice.

The advantages of social login are numerous. A few are:

* You know your users are verified since the social provider is already responsible for verifying the user’s email. 
* If you want to double check an individual, you can immediately access information such as their interests, location, connections, and more to make sure the account is real. 
* This data can also go a long way towards underpinning new marketing and product tactics.

You can take this a step further and build even richer customer profiles by integrating with a provider like [Clearbit](https://blog.clearbit.com/avoid-tedious-signup-forms-with-auth0-and-clearbit/). Clearbit sharpens customer intelligence, allowing teams access to valuable public information that can help them outperform.

## Why Auth0?

Outsourcing your user database needs allows you to turn over these critical tasks to experts, who are up-to-date on the latest trends and are keeping pace with evolving threats. An identity services provider like Auth0 doesn't have to balance these tasks with regular maintenance projects like most IT teams do. 

{% include tweet_quote.html quote_text="Outsourcing your user database needs allows you to turn over these critical tasks to experts, who are up-to-date on the latest trends and are keeping pace with evolving threats." %}

Auth0's user database tools support all major industry standards such as SAML, OpenID Connect, JSON Web Token, OAuth 2.0, OAuth 1.0a, WS­-Federation and OpenID, preventing vendor lock-in.

If you're looking for a quick solution to help you come into [GDPR](https://auth0.com/gdpr) compliance — as part of a larger customer identity and access management (CIAM) platform — MFA and Social Login will support your getting there in an efficient manner.

If you are just starting out or [modernizing](https://auth0.com/app-modernization) legacy apps from years of prior use, integrating tools to protect user identities from the outset will build a solid foundation for your products for years to come.
