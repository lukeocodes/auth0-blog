---
layout: post
title: "Cloud-Scale Thinking from Day One for Your SaaS Products"
description: "Learn how utilizing identity solutions like Auth0 allows your engineers to focus on what matters most to your company - developing your SaaS product's unique features."
date: 2018-07-26 12:30
category: Growth, Identity
is_non-tech: true
author:
  name: Saravana Kumar
  url: http://twitter.com/saravanamv
  mail: saravana.kumar@biztalk360.com
  avatar: https://cdn.auth0.com/blog/guest-authors/saravana-kumar.png
design:
  bg_color: "#232D84"
  image: https://cdn.auth0.com/blog/cloud-scale/cloud-scale-saas.png
tags:
  - scalability
  - identity
  - saas
  - growth
related:
  - 2018-03-27-strong-identity-management-system-eases-transition-to-hybrid-cloud
  - 2018-03-09-3-reasons-your-data-integration-plan-is-important
  - 2018-05-21-digital-success-through-customer-identity-and-access-management
---

<div class="alert alert-info alert-icon">
  <i class="icon-budicon-500"></i>
  <strong>Forrester Consulting analysis determines that using Auth0 can yield a 548% ROI and $3.7M in identity-related savings. Read the full report: <a href="https://resources.auth0.com/forrester-tei-research-case-study/">Total Economic Impact of Auth0</a>.</strong>
</div>

---

**TL;DR**: In this article, we will explore some of the key technical considerations you have to make while building highly-scalable SaaS products. The idea is to show that it's better to use reliable PaaS (Platform as a Service) vendors available in the market instead of utilizing your valuable engineering resources on infrastructure requirements. As you will learn, this will make your engineers available to focus on what matters the most, the key features of your project.

{% include tweet_quote.html quote_text="Relying on battle-tested solutions allows your engineers to focus on what matters most to your company — the unique features of your SaaS products." %}

## Introduction

Before going into the details, I need to give you a short background of Document360, which will help you to understand the context of the article better.
 
[Document360 is a self-service knowledge base product](https://document360.io/) that helps you to create public or private product documentation. Take as an example [Auth0's own documentation](https://auth0.com/docs). Using Document360, you can easily build such product documentation.

![Document360 logo](https://document360.io/wp-content/uploads/2018/02/document360_logo.png)
 
When the idea for Document360 was conceived at the end of 2017, one of the key decisions we have made was to build a solution to scale to unlimited users without any architectural changes. This means that the product has to serve 500 customers today and it shouldn't suffer big architectural changes to serve 500,000 customers in a few years down the line.
 
Architecturally, this requires a different level of thinking and infrastructure requirements when you are designing a system that's capable of 500 customers and 500k customers. You need to be careful you are not over-engineering the solution, which will end up being difficult to manage and very expensive.
 
For example, let's say you have the requirement to pick a video file from a storage location to do some processing (like creating a transcript) and to store the result in a third-party storage location. When you are looking at processing 5 video messages per day, the solution is pretty simple, but imagine if you are YouTube or Wistia and need to process 100,000+ videos per day. Then, this single piece of functionality will require significant architecture and infrastructure investment.
 
If you do not plan correctly and decide to build scalable components yourself, your valuable software engineering time will be spent on creating basic versions of those functionalities. The cloud Platform as a Service (PaaS) market has evolved significantly in the past 5-8 years and, today, there is a specialized offering for each piece of the puzzle. It might be a smart move to pick the required Lego blocks and assemble it together rather than building everything yourself.

## Core Services for Cloud Scaling

For Document360, there are five core pieces of the product that required cloud-scale thinking:

1. the Identity Management (user authentication) system;
2. an user-facing web application;
3. technical-writers-facing web application;
4. a search engine;
5. and data storage.

Before going into identity management, which is the core of this article, let's quickly take a brief look at the background about other scalable parts of the architecture.

### User-Facing and Technical-Writer-Facing Web Apps
 
These web applications are straightforward requirements. Both applications should be scalable, reliable, and fast to provide a great experience to both end customers and technical writers. We decided to use [Microsoft Azure Web Apps](https://azure.microsoft.com/en-gb/services/app-service/web/) as the core front-end facing technology with [Azure Traffic Manager](https://azure.microsoft.com/en-gb/services/traffic-manager/) to route the traffic to the closest location.

### Search Engine
 
For any self-service knowledge base product, a good search engine is the key. These days, people do not navigate through your structure, they will just go and hit that big search box to look out for answers. We need to make sure the search feature is robust and, again, scalable when 1000's of customers arrive. There is no point in building this in-house. As such, we decided to go with [Algolia as our search provider](https://www.algolia.com/).

### Data Storage
 
A document management product like Document360 is full of text documents, so we need a reliable storage (database) with fast read and write capabilities and that can also scale as we grow. For this feature, we decided to go with [MongoDB cloud as our data storage provider](https://www.mongodb.com/cloud).

## Identity and Access Management with Auth0

Now, let's get a bit deeper into the main part of this article: ["Identity Management as Service"](https://auth0.com/learn/cloud-identity-access-management/). Document360 got various types of users logging into the system. When you are building a public facing self-service knowledge base, your draft writers and editors need to safely login to the system to produce relevant content. In some scenarios, where the customer decided to keep their self-service knowledge base private to the organization, then the whole organizations need to securely login in.

![Managing Identity with Auth0](https://cdn.auth0.com/website/assets/pages/dashboard/img/p-dashboard-6fc11ba51b.png)

Out of the five core areas of Document360, getting the initial authentication part in a secure, reliable, and scalable part is the most important aspect. For this, we decided to go with [Auth0 to provide the initial login and user authentication for our product](https://auth0.com/).

There are few reasons why we decided to use Auth0 instead of custom building our own identity providers:

- **Enterprise-Ready**: Even though capturing credentials (username/password), validating them against your database, and issuing a validation token might look like a simple task, soon, everything get complicated when you look at the scale factor. It would have taken months for our developers to build [an identity solution with the B2C features that Auth0 provides](https://auth0.com/b2c-customer-identity-management). More than that, it would take years to reach the level of quality and security of what Auth0 can provide.
- **Hack-Proof**: These days, when you are building a SaaS product like Document360, you need to worry about the DDOS attack and identity hacking. Every now and then, there is something on the news saying that one of the top websites was hacked. As such, it's not worthing the risk for us to do it ourselves. A product like Auth0 deals with billions of identity validation every day will be [better equipped and more secure than any in-house solution that we come up with](https://auth0.com/security). Enabling functionalities like [breached password detection](https://auth0.com/breached-passwords) enables that our users data is secure..
- **Trivial to add more providers**: Initially, we are starting with a standard username/password for identity verification. However, our plan in later stages is to support more identity and [single sign on (SSO)](https://auth0.com/single-sign-on) capabilities. By using Auth0, we can rest assured that, when the time comes, we will be able to [easily support other identity providers like Azure Active Directory, LDAP, and social identities (like Facebook, Google, LinkedIn etc)](https://auth0.com/docs/identityproviders). With Auth0, this is more of a trivial configuration change rather than spending weeks building support for new identity providers.
- **Low cost**: If we put together the total investment required to build and manage an identity solution, we would be amazed. The effort and the investment will all add up pretty quickly because you will need specialized engineering resources blocked just for this feature and for the infrastructure requirement. By using Auth0, we save money by simply [paying based on our usage](https://auth0.com/pricing). That is, we pay more when we make more money.
- **Rules trigger on identity**: This is one of the cool features we take advantage of at Auth0. We have requirements like, whenever a new user signup, we need to notify the sales team. With Auth0, we achieved this by plugging in [the Zapier integration after a successful authentication](https://auth0.com/rules/zapier-new-user). This was trivial.
- **Single Sign On**: In the Document360 case, we have two different web portals. One for where the Technical writers produce knowledge base content and another one for user/customer facing website (public or private). When the customer decides to go private, then [a seamless Single Sign On experience](https://auth0.com/docs/sso/current) is required between both the portals. This is achieved seamlessly using Auth0.

{% include tweet_quote.html quote_text="By using Auth0, we save money by simply paying based on our usage. That is, we pay more when we make more money." %}

## Summary

If you are building a SaaS product today, you really shouldn't think about building things yourself. The modern way of building stuff is all about assembling the best solutions in the industry together and constructing a solid application. That's exactly what we have done in our case. The core parts of the business are running with the help of great products in the market and we act more or less like an orchestrator to assemble and create a beautiful music out of all these talents.

{% include asides/about-auth0.markdown %}

## About the Author

Saravana Kumar is the Founder of [Document360](https://document360.io/), a  product that helps your team create, collaborate, and publish a self-service knowledge base for your software with ease.  Saravana is passionate about technology and he holds the famous Microsoft Most Valuable Professional title for 12 years in a row. He founded and scaled three other successful enterprise products ([BizTalk360](https://www.biztalk360.com/), [Serverless360](https://www.serverless360.com/) and [Atomic Scope](https://www.atomicscope.com/)) in the last 7 years.
