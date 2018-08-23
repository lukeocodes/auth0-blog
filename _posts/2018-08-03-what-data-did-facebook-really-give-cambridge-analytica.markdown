---
layout: post
title: "What Data Did Facebook Really Give Cambridge Analytica?"
description: "An honest look at the Cambridge Analytica data breach numbers. What's next for social login authentication? Time to switch social providers?"
date: 2018-08-03 8:30
category: Security, Privacy, GDPR, UX
design: 
  bg_color: "#203259"
  image: https://cdn.auth0.com/blog/cambridge-analytica-facebook/logo.png
author:
  name: Diego Poza
  url: https://twitter.com/diegopoza
  avatar: https://avatars3.githubusercontent.com/u/604869?v=3&s=200
  mail: diego.poza@auth0.com
tags: 
  - facebook
  - gdpr
  - cambridge-analytica
  - privacy
  - security
  - breach
  - login
  - sso
  - authentication
  - social-media
  - progressive-profiling
  - policy
related:
  - 2018-07-02-breaking-down-master-data-management
  - 2018-08-02-four-cybersecurity-attacks-you-need-to-know
---

In 2015 Facebook learned that Dr. Aleksandr Kogan, a psychology professor at the University of Cambridge, illegally passed Facebook data that he collected with his app, “thisisyourdigitallife,” to the political and military strategy firm SCL/Cambridge Analytica.

Kogan billed “thisisyourdigitallife” as a research app used by psychologists and achieved [270,000 downloads](https://newsroom.fb.com/news/2018/03/suspending-cambridge-analytica/). Each user consented for Kogan to access their personal information, including location, Facebook content they had liked, and friends and connections. 

Cambridge Analytica also obtained data without users' consent from an [additional ~87 million](https://www.wired.com/story/facebook-exposed-87-million-users-to-cambridge-analytica/) accounts and sold their findings to political campaigns, including those of [Ted Cruz](https://www.theguardian.com/us-news/2015/dec/11/senator-ted-cruz-president-campaign-facebook-user-data), Ben Carson, and [Donald Trump](https://www.nytimes.com/2018/03/17/us/politics/cambridge-analytica-trump-campaign.html).

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/what-data-did-facebook-really-give-cambridge-analytica/Cambridge-Analytica-internal-email.jpg" alt="Cambridge Analytica data breach internal email discussion">
</p>

[Source](https://www.nytimes.com/2018/03/17/us/politics/cambridge-analytica-trump-campaign.html)

This data leak is one of the largest and most upsetting for users to date, given the strong evidence of how Cambridge Analytica used their information to influence divisive elections.

For the millions of companies that use [Facebook Login](https://auth0.com/learn/social-login/) to authenticate their users, this investigation poses new business and ethical challenges. This article will help teams grapple with how the scandal could affect their operations and offer solutions for moving forward.

## Should You Stop Using Facebook Login with Your Apps?

Shortly following news of the breach, [Facebook launched an apology ad campaign](https://www.fastcompany.com/40563382/facebook-says-sorry-sort-of-in-its-biggest-ever-ad-campaign) aimed at helping users steer clear of dishonest accounts on its platform that could be attempting to steal their information.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/what-data-did-facebook-really-give-cambridge-analytica/apology-ad-campaign.png" alt="Facebook post-data-breach apology ad campaign">
</p>

 [Source](https://www.wired.com/story/facebook-launches-a-new-ad-campaign-with-an-old-message/)

While the campaign provided some good insights, it also highlighted staggering issues within Facebook — particularly how the company has scaled without paying close enough attention to certain user behaviors. 

From fake accounts to clickbait, spam, and more, it appears like [Facebook could be a messy platform to continue to authenticate](https://auth0.com/learn/social-login/) with — at least in the near-term. 

Moreover, it could be a challenge to explain to your users why you are continuing to collect their Facebook data.

### Solution 1: Use Other Forms of Social Login

You have a series of other options for authenticating your users.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/what-data-did-facebook-really-give-cambridge-analytica/auth0-social-connectors.png" alt="Auth0 Social Connections dashboard">
</p>

[Auth0 supports 30+ social providers, including Twitter, Google, Yahoo, and LinkedIn](https://auth0.com/learn/social-login/). You can also just add any OAuth2 Authorization Server you need. Despite differences among each provider, Auth0 simplifies the process by unifying the way to call providers and the information you retrieve from them.

It's simple to switch providers in the Auth0 dashboard. If you haven't already, you can <a href="https://auth0.com/signup" data-amp-replace="CLIENT_ID" data-amp-addparams="anonId=CLIENT_ID(cid-scope-cookie-fallback-name)">sign up for a free Auth0 account here</a>. Then, simply click [Connections, then Social](https://auth0.com/learn/social-login/). To enable a new provider, just flip the switch next to their icon. You can then select the desired attributes and permissions you want to get from the provider in the configuration popup.

### Solution 2: Incorporate Simpler and More Visible Means of Obtaining User Information

[Progressive profiling](https://auth0.com/blog/progressive-profiling/) is another technique for gradually building up a profile of your customers. Each time they interact with your product, you display a short questionnaire. It can begin with something as simple as requesting a first and last name:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/what-data-did-facebook-really-give-cambridge-analytica/auth0-registration-form.png" alt="Auth0 registration signup with expanded first and last name fields">
</p>

From there,  you can continue to get more specific — but in small increments:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/what-data-did-facebook-really-give-cambridge-analytica/auth0-progressive-profiling-form.png" alt="Auth0 progressive profiling form">
</p>

Progressive profiling allows you to keep these forms short and to the point and collect data in a clear and transparent way. You will slowly build up a robust customer profile that affords you the same power as a Facebook profile does to send better, more targeted offerings to your customers.

### Employ Good Privacy Policy UX

Whichever authentication method you go with, make sure you clearly explain your process to your users. While this is critical for [complying with GDPR and related measures](https://auth0.com/docs/compliance/gdpr), it is also an opportunity to highlight the benefits of why you are doing so. Are you helping deliver to customers the content they actually want to receive? Avoiding showing them products or hyping services that are irrelevant? There is a lot of information you can incorporate alongside legal jargon that shines a light on how thoughtful your team is with its operations.

Starting gradually will help assure your users of your expertise at the outset without overwhelming them with technical information.
 
<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/what-data-did-facebook-really-give-cambridge-analytica/microsoft-form-explaining-usage-of-birthdate.png" alt="Microsoft form explaining usage of birthdate">
</p>

[Source](https://www.econsultancy.com/blog/69256-gdpr-how-to-create-best-practice-privacy-notices-with-examples)

Adding an automatic text window, like Microsoft does, that appears when requesting data points enhances the user experience in a clear and simple way. From there, you can prompt viewers to click through to a larger policy statement. If they continue to be curious, place links for them to dig even deeper:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/what-data-did-facebook-really-give-cambridge-analytica/microsoft-privacy-statement.jpg" alt="Microsoft privacy policy sample">
</p>

[Source](https://www.econsultancy.com/blog/69256-gdpr-how-to-create-best-practice-privacy-notices-with-examples)

If you're daunted by crafting all of this text yourself — ensuring it is both accurate and digestible —  there are many privacy policy templates online. If you're an e-commerce company, for example, [Shopify](https://www.shopify.com/tools/policy-generator) offers a series of tips and tricks. 

Remember: investing in current customers can actually produce better results than attracting new ones. Taking the time to open up to those currently engaged will spur them to create more value for you in the long run.

## Just Because You Rely on Facebook Login, Doesn't Mean There Isn't a Way Forward

Although Facebook has frustrated many companies with its lack of oversight in the wake of the Cambridge Analytica data breach, it still has enormous resources at its fingertips to (hopefully) help it improve. While we wait for the tech titan to rebuild user trust (and perhaps become a beloved tool for business partners once more), the above solutions will help you continue to research, protect, and delight your users. 

{% include asides/about-auth0.markdown %}
