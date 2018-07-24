---
layout: post
title: "How Social Login Can Pave the Way for a Digital Shift in Grocery"
description: "A comprehensive view of your shoppers will create a secure and practical foundation for change."
longdescription: "A comprehensive view of your shoppers will create a secure and practical foundation for change."
date: 2018-06-11 08:30
category: Hot Topics, Trends
author:
  name: Martin Gontovnikas
  url: http://twitter.com/mgonto
  mail: gonto@auth0.com
  avatar: https://www.gravatar.com/avatar/df6c864847fba9687d962cb80b482764??s=60
design:
  image: https://cdn.auth0.com/blog/how-social-login-can-pave-the-way-for-a-digital-shift-in-grocery/logo.png
  bg_color: "#2E598C"
tags:
- social-login
- social
- login
- digital
- transformation
- grocery
related:
- 2017-12-18-retail-analytics-past-present-and-future
- 2018-02-02-3-ways-to-get-an-iam-budget-in-2018
- 2017-11-27-how-data-analytics-can-transform-your-business
---

With its 2017 acquisition of [Whole Foods](https://auth0.com/blog/why-amazon-and-whole-foods-will-change-how-you-shop/), Amazon is changing the competitive landscape in grocery. Now backed by the latest technology, Whole Foods recently announced an overhaul of [90%](https://diginomica.com/2016/06/24/whole-foods-is-replacing-up-to-90-of-its-systems-strong-focus-on-cloud-and-data/) of its operations, replacing legacy systems with digital ones. In order to remain competitive, other grocers must follow suit.

For small or medium-sized grocers, undergoing a [digital transformation](https://auth0.com/blog/3-iam-examples-to-support-digital-transformation/) can be daunting. In essence, it is a renewal of everything from back-office operations to how you engage with your customers. Particularly in an industry that relies on physical goods, this change might seem overly complicated and impersonal; however, with a clear set of steps in place, any team can make the change efficiently, remain relevant, and even deepen interactions with shoppers. 

The first step in making the shift to a digital business is implementing a solid [customer identity and access management](https://auth0.com/b2c-customer-identity-management) (CIAM) system. This is the lynchpin of any digital strategy: accounting for and having a clear view at any time of all of your customers will lay the foundation for creating a more secure and personalized shopping experience. 

While many tools are at your fingertips to craft a CIAM system, a [Social Login feature](https://auth0.com/learn/social-login/) will not only ease your customers' registration process but will also provide you with a rich amount of data to drive a more personalized shopping experience.

## Streamline Your Registration Process with Social Login

[86 percent of this survey's users](http://www.webhostingbuzz.com/blog/2013/03/21/whos-sharing-what/) have historically reported being bothered by having to create new accounts on websites. It's easy to click out of a site when the registration process is taking more than a couple of minutes. Social Login can, therefore, be key to avoid losing potential customers.

Social Login is also a more secure method for your shoppers to engage online. Studies show that [65% of internet users](http://www.pewinternet.org/2017/01/26/americans-and-cybersecurity/) do not practice safe password management techniques, preferring to memorize or write down a password instead. This can lead to having to constantly re-create passwords and making your sensitive data more vulnerable to hackers. 

Auth0 supports over [30 social providers](https://auth0.com/docs/identityproviders), including Facebook, Twitter, Google, and PayPal:

![Auth0 supports over 30 social providers, including Facebook, Twitter, Google, and PayPal.](https://cdn2.auth0.com/website/learn/assets/social-providers.png)

You also have the option to add any OAuth2 Authorization Server you would like.

Despite its powerful capabilities, Social Login is [simple to install](https://auth0.com/learn/social-login/). For grocers just beginning to explore the shift to digital systems and operations, adding Social Login is a logical first step.

After adding Social Login, when a user arrives at your store's login page, she or he will see something similar to the option below, depending on the providers selected:

![Auth0's social login](https://cdn2.auth0.com/docs/media/articles/libraries/lock/v10/customization/lock-theme-labeledsubmitbutton.png)

Instead of asking new and/or existing users to re-upload personal information, Social Login relies on existing information from provider profiles — making access a simple click.

## Use First Party Data to Personalize the Shopping Experience

Consumers find what they're looking for only [10%](https://hbr.org/2016/02/making-personalized-marketing-work) of the time when interacting with online content. And with U.S. marketers spending close to [$60 billion](https://www.emarketer.com/Article/US-Digital-Ad-Spending-Will-Approach-60-Billion-This-Year-with-Retailers-Leading-Way/1012497) annually in digital advertising, the potential to waste valuable funds by mis-targeting your customers is high. 

Social Login affords you the possibility of having a comprehensive view of each of your customers to better understand their needs. 

![Social Login affords you the possibility of having a comprehensive view of each of your customers to better understand their needs.](https://cdn.auth0.com/blog/digital-shift-grocery/comprehensive-view-customers.png)

Pulling existing information from the social provider can provide important details such as the user's full name, picture, gender they have identified, employment information, geographic location, and interests. You can even see their behavior on your site, i.e., have they logged in just once or are they a regular visitor? This wealth of data allows you to provide a [richer customer experience](https://auth0.com/blog/how-profile-enrichment-and-progressive-profiling-can-boost-your-marketing/). 

For example, some social networks log a user's search history. Spending time understanding what these customers are exploring can allow you to market specific products to them. If a set of customers repeatedly looks for “gluten-free,” your store might consider piloting a gluten-free section of snacks in a visible spot. 

If sales are strong, you could expand this to a larger aisle and/or diversify your offerings into gluten-free baking ingredients and condiments. A strong foundation of user data captured at the login stage not only helps you expand revenue streams but also ensures that you tailor your store's offerings to shoppers' preferences and health needs.

Social Login can rapidly bring your organization up to speed with industry leaders by allowing for hyper-targeted marketing and the ability to build a more loyal following.

## Know Your Customers. Prevent Fraudulent Users.

Making sure you know who is using your system at any point is critical for keeping consumer data safe. [Fake profiles](http://www.newsweek.com/hackers-use-fake-profiles-attractive-women-facebook-spread-viruses-814293) have historically lured company employees to divulge classified information and/or provide access to servers. If a cybercriminal is able to make it through your system in this way, the potential for a data breach is high.

A critical component of a Social Login feature is [email verification](https://auth0.com/learn/social-login/). Since each social network provider tries to verify its users' emails, it is likely that if the provider shares this information, you will get an authentic account. Knowing in advance that your new registrants have already been authenticated can give you peace of mind as you move your operations to the cloud.

Some providers like Twitter will not share their users' email addresses. In these cases, you can also incorporate an additional [email verification](https://auth0.com/rules/email-verified) rule. [Auth0's Rules](https://auth0.com/docs/rules/current) are arbitrary bits of JavaScript code that can be used to extend Auth0s default CIAM capabilities when authenticating users. To set up force email verification on the Auth0 platform, simply create a new rule, then copy the code below. Be sure to replace the placeholders with appropriate values:
Adding an email verification rule to Social Login will ensure that your new user's information is both authentic and current. When they are shopping for coupons, updating their account and/or payment information, signing up for store events, or engaging with other content you've posted — you can be sure they are who they say they are.

## Tools for Teams of All Sizes

Social Login and force email verification are tools that are available for teams of all sizes — not just the industry titans. While Whole Foods may have paved the way for a digital shift in grocery, other players have the ability to implement and get creative with the registration process and incorporation of shoppers' data, whether at the login stage or in [smaller marketing campaigns](https://www.marketingweek.com/2016/11/21/lidl-lets-customers-vote-christmas-prices-social-media-first/). These tools can be initial, concrete steps in a broader CIAM strategy — which will serve as the foundation for a full digital transformation. 
