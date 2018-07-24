---
layout: post
title: "3 Tools For Digital Transformation in the Airline Industry"
description: "Airline industry cybersecurity - map your customer journey, incorporate passwordless registration, and profile enrichment to create a more enjoyable and secure UX."
date: 2018-07-23 8:30
category: Growth, Industries, Travel
design: 
  bg_color: "#00604A"
  image: https://cdn.auth0.com/blog/3-tools-for-digital-transformation-in-air-travel/digital-transformation-air-travel.png
author:
  name: Diego Poza
  url: https://twitter.com/diegopoza
  avatar: https://avatars3.githubusercontent.com/u/604869?v=3&s=200
  mail: diego.poza@auth0.com
tags: 
  - identity
  - travel
  - digital-transformation
  - idaas
  - security
  - authentication
  - ux
  - usability
related:
  - 2018-06-22-the-best-and-worst-travel-sites-at-keeping-your-info-safe
  - 2017-03-15-5-reasons-your-company-needs-identity-and-access-management
  - 2017-12-18-retail-analytics-past-present-and-future
---

Digital transformation starts with the customer. Understanding the full scope of a customer's journey and the numerous points at which s/he interacts with your company's products and services will serve as a guide when modernizing operations. This is especially important for airlines. While many might think that the flight is the only important part of the customer journey in air travel, critical communications take place starting from the customer's initial decision to buy tickets and book a flight through post-flight feedback and promotions.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/3-tools-for-digital-transformation-in-air-travel/customer-communication-flow-for-air-travel.png" alt="Customer flow and journey map in air travel experiences">
</p>

While many airlines will have similar maps for their customers, you can tailor this to your specific brand using resources from companies like [ProsperWorks](https://www.prosperworks.com/blog/the-importance-of-customer-journey-mapping) and [Appcues](https://www.appcues.com/blog/user-journey-map) that delve into user personas and more advanced tools for approaching your research. However you go about visualizing the path your customer(s) take with your company, this exercise will help you notice the best places to make digital improvements.

## Enhance The Customer Journey: Key Tools In A Successful Digital Transformation

Once you've mapped out your customer journey, including details like booking processes and post-flight surveys that might be unique to your organization, it's time to hone in on key pain points. The customer's initial login experience and profile creation are areas that touch on and can help solve several issues at once.

### Passwordless Registration

Modernizing the first major point at which a customer or potential customer engages with your company online ensures a top-notch experience and sets the stage for enhanced security in the future. With [Auth0 Passwordless](https://auth0.com/passwordless), on the front end, you can offer one time codes or “magic links” delivered via SMS or e-mail.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/3-tools-for-digital-transformation-in-air-travel/pwdless.png" alt="Slack, Medium, and Google login screens">
</p>

{% include tweet_quote.html quote_text="Modernize the first major point at which a customer engages with your company online: account registration." %}

([Source](https://auth0.com/blog/auth0-passwordless-email-authentication-and-sms-login-without-passwords/))

Instead of prompting customers to re-enter a password every single time, passwordless login can ease any frustration and mitigates risk of churn early on when you need to delight your users the most.

On the back-end, your team can combine passwordless login with additional security features like [anomaly detection](https://auth0.com/docs/anomaly-detection), suspicious logins, and centralized session revocation to build out a robust authentication system that double-checks each user truly is who they say they are.

For example, Auth0 partners with ThisData to [add specific alerts](https://thisdata.com/blog/how-to-add-suspicious-login-alerts-to-auth0/) regarding suspicious logins. After creating a ThisData account in the Auth0 dashboard, just add the Login Anomaly Detection Rule in the Access Control section.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/3-tools-for-digital-transformation-in-air-travel/create_rule.png" alt="Adding a Login Anomaly Detection Rule in the Auth0 Access Control section">
</p>

([Source](https://thisdata.com/blog/how-to-add-suspicious-login-alerts-to-auth0/))

Auth0 [Rules](https://auth0.com/docs/rules/current) extend security features so that you can build and customize a solution on the back-end that supports all of your customers as they browse travel deals, flight times, select an itinerary, pay, check-in, and more. Not only will customers have an easier time exploring — you can ensure them that your app is a safe environment in which to share their personal information.

{% include tweet_quote.html quote_text="Auth0 Rules extend security features so that you can build and customize a solution on the back-end that supports all transactions with your customers. " %}

### Profile Enrichment

To better understand your customers and deliver more customized offerings throughout their journey, it's important to acquire all of the publicly available information you can to underpin your strategy. [Profile enrichment](https://auth0.com/blog/how-profile-enrichment-and-progressive-profiling-can-boost-your-marketing/) is a tool that captures this data using third-party data APIs, such as [Clearbit](https://help.clearbit.com/hc/en-us/articles/115004646673-Enrichment-Overview). Again, working with Auth0 Rules, you can integrate this information into a 360-view of any of your customers:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/3-tools-for-digital-transformation-in-air-travel/clearbit-bi.png" alt="360-view of a customer's data using Clearbit">
</p>

([Source](https://auth0.com/blog/how-profile-enrichment-and-progressive-profiling-can-boost-your-marketing/))

You can capture important points such as their location, employer, Facebook interests, and email address. Of course, given the strict rules about customer data in the wake of [GDPR](https://auth0.com/gdpr), it's important to communicate to your customers what data you've acquired, how, and why. Then, be sure you give them the option to edit or even opt out if they'd like. But it's also important that they understand how allowing you to view and work with this information can benefit them. For example, you can send them special deals in their geographic area or even tailor in-flight menu options that support a healthier lifestyle (if this is a clear interest of theirs).

Profile enrichment is a powerful feature that gives a team a more comprehensive understanding of its travelers and unveils myriad ways to support them along their path.

## Staying Secure in the Airline Industry

We mentioned this earlier and will say it again: in a challenging environment, airlines need all the support they can get in terms of security. The three resources above — a customer journey map, passwordless login, and profile enrichment — will help your company comply with laws like GDPR and help you build customer trust and loyalty down the line.

While physical security has been beefed up worldwide from more cameras, TSA guards, agents, guidelines, and rules, and even canines, it's cybersecurity that deserves equal if not more attention. Making sure you really know who is traveling on your aircraft — when, from where, their travel history, payment methods, and even friends and relatives — is all valuable data that, when stored in a central location and in an accessible format, can keep your entire company and all of its stakeholders safe.
