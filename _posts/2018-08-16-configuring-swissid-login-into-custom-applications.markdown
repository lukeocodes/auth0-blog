---
layout: post
title: Supporting Government-Issued IDs as Social Connections with Auth0
description: Learn how to support government-issued IDs (like SwissID) as Social Connections into your applications with Auth0.
date: 2018-08-16 08:30
category: Technical Guide, Identity, SwissID
author:
  name: Peter Fernandez
  url: http://www.linkedin.com/in/pgfernandez
  mail: peter.fernandez@auth0.com
  avatar: https://cdn.auth0.com/blog/guest-authors/peter-fernandez.png
design:
  bg_color: #222228
  image: https://cdn.auth0.com/blog/swissid/swissid-social-connections-logo.png
tags:
- swissid
- oauth
- openid
- auth0
- social-connection
related:
- 2015-12-16-how-to-use-social-login-to-drive-your-apps-growth
- 2017-11-06-authenticated-identity-trusted-key-auth0
- 2018-06-11-how-social-login-can-pave-the-way-for-a-digital-shift-in-grocery

---

**TL;DR:** In this article, you will learn how Auth0 can help you support government-issued IDs as social connections in your own applications with ease. For demonstration purposes, you will learn how Auth0 can help you support SwissID (a popular digital identity solution in Switzerland) in your applications. However, the process explained here will be quite similar for other similar government-issued IDs.

{% include tweet_quote.html quote_text="With @auth0, developers can easily support government-issued IDs in their applications." %}

## SwissID Introduction

[SwissID](https://swissid.ch) is the standardized digital identity initiative for use across Switzerland. This initiative is a joint venture of state-affiliated businesses, financial institutions, and health insurance companies (like [Swiss Post](https://www.post.ch/en) and [SBB](https://www.sbb.ch/en/)) that provide a third-party [Identity Provider](https://auth0.com/docs/identityproviders) service to application vendors. With SwissID, users retain exclusive ownership over their personal data making online transactions easier to process and, more importantly, more secure.

Usually, application vendors implementing support for SwissID would require a working knowledge of the [OAuth 2.0 protocol](https://auth0.com/docs/protocols/oauth2) and of the [OpenID Connect specification](https://auth0.com/docs/protocols/oidc). However, as [Auth0](https://auth0.com) now supports SwissID as a [Custom Social Connection](https://auth0.com/docs/extensions/custom-social-extensions), developers can easily integrate this identity provider into their applications. Also, alongside with SwissID, by using Auth0 you can support a myriad of other Identity Provider services (e.g., Facebook, Google, Twitter, and LinkedIn) as well as add capabilities such as [Single Sign On](https://auth0.com/docs/sso/current) and [Multifactor Authentication](https://auth0.com/docs/multifactor-authentication).

{% include tweet_quote.html quote_text="Auth0 helps developers integrating the SwissID identity provider into their custom applications." %}

## Signing up to SwissID

As an application vendor (a.k.a. a [Relying Party](https://auth0.com/identity-glossary#r)), you will first need to sign-up to SwissID. This process starts by [contacting SwissID in order to become a Business Partner](https://www.swissid.ch/en/business-partners#become-a-part-of-a-success-story). After the sign up with SwissID, you will have to provide information about your redirection URI’s. In return, SwissID will provide you with a Client ID and a Client Secret. Keep handy this information as you will need both values while setting up SwissID within you Auth0 account.

## Signing up to Auth0

If you don't have an Auth0 account yet, <a href="https://auth0.com/signup" data-amp-replace="CLIENT_ID" data-amp-addparams="anonId=CLIENT_ID(cid-scope-cookie-fallback-name)">sign up for a free one right now</a>.

As part of [the free plan](https://auth0.com/pricing), you will get things like:
- 7,000 active users.
- Up to 2 [social identity providers](https://auth0.com/docs/identityproviders).
- Access to [Auth0's Passwordless feature](https://auth0.com/passwordless).

![Auth0 free plan](https://cdn.auth0.com/blog/swissid/auth0-free-plan.png)

While signing up to Auth0, you will have to define a _Tenant Domain_ (e.g., `your-company.auth0.com`) and you will have to choose a _Region_ (at the time of writing, three regions are available: US, Europe, and Australia). After filling in this form, you can complete your account creation, and Auth0 will redirect you to your dashboard.

![Auth0 dashboard](https://cdn.auth0.com/blog/secure-your-gaming-company-with-auth0's-user-fraud-score-and-minfraud/auth0-dashboard.png)

## Setup a Government-Issued ID as a Custom Social Connection in Auth0

With your Auth0 account properly created, you will have to install [the _Custom Social Connections_ extension](https://auth0.com/docs/extensions/custom-social-extensions) in your tenant. This extension provides an easy way to manage social connections within Auth0 that are not supplied as "first-class citizens" (i.e., they are not available out of the box). To install it, go to [the _Extensions_ section of your dashboard](https://manage.auth0.com/#/extensions) and click on the _Custom Social Connections_ option.

![Installing the Custom Social Connection extension](https://cdn.auth0.com/blog/swissid/custom-social-connection.png)

Once this extension is installed, you can use it to set up an integration with SwissID. So, if you click on its name in the _Installed Extensions_ table, Auth0 will redirect you to your extension's deployment.

![The Custom Social Connection extension' deployment](https://cdn.auth0.com/blog/swissid/custom-social-connect-deployment.png)

## Configuring the Government-Issued ID Connection in Auth0

From your deployment, you can click on the _New Connection_ button. This will bring up the _New Connection_ dialog, which you can use to configure your SwissID connection.

So, in this dialog, you will have to define a name for your connection (something similar to "SwissID-connection" would be descriptive enough). Besides that, you will also have to input your SwissID _Client ID_ and _Client Secret_ properties.

Then, you will have to define the _Fetch User Profile Script_ as follows:

```javascript
function (accessToken, ctx, cb) {
  // call SwissID to obtain user profile
  request.post({
    url: 'https://login.int.swissid.ch:443/idp/oauth2/userinfo',
    headers: {
      'Authorization': 'Bearer ' + accessToken,
    },
  }, function (err, res, body) {
    if (err) return cb(err);
    if (res.statusCode !== 200) return cb(new Error(body));

    // validate token using the node-jsonwebtoken library
    jwt.verify(body, 'SWISS_ID_SECRET', {}, function(err, decoded) {
      if (err) return cb(err);

      cb(null, {
          user_id: decoded.sub,
          family_name: decoded.family_name,
          given_name: decoded.given_name,
          gender: decoded.gender,
          email: decoded.email,
          provider: 'SwissID',
      });
    });
  });
}
```

> **Note:** You will have to replace `SWISS_ID_SECRET` with your own SwissID _Client Secret_.

After implementing the script above, you will have to define three more properties before wrapping up:

1. _Authorization URL_: In this field, you will have to input this: `https://login.int.swissid.ch:443/idp/oauth2/authorize`.
2. _Token URL_: In this field, you will have to input this: `https://login.int.swissid.ch:443/idp/oauth2/`.
3. _Scope_: In this field, you will have to input this: `openid profile email phone`.

With these changes in place, you can hit the _Save_ button to persist the new settings.

> **Note:** The function declared in the script above is fully customizable and is used to obtain the various supported claims from SwissID. You can tweak this script to build the profile from different perspectives for a particular user (for example, you could ask only for its email address, or its email and its first name, etc.). If you need help building up your users' profile, you can check [the reference application provided by SwissID](https://login.int.swissid.ch/swissid-ref-app) to explore what claims are available. 

## Testing the SwissID Custom Social Connection in Auth0

After hitting _Save_, a new set of buttons will appear on the screen. One of them will be the _Try_ button. Pressing it will present you the SwissID login page and, once you submit your credentials successfully, a page similar to the following will be displayed by Auth0:

![Testing the SwissID Custom Social Connection in Auth0](https://cdn.auth0.com/blog/swissid/testing-the-social-connection-extension.png)

That's it! Your custom connection between Auth0 and SwissID is already configured. Now, you can use the _Apps_ tab available on your custom connection to enable the applications you want to use with SwissID. For example, the screenshot below shows a configuration where only the _Some Awesome Product_ application has the SwissID custom connection active.

> **Note:** If you are experiencing problems with your SwissID Connection, please, ensure that the SwissID team have set the configuration to use client secret in the body of POST requests to their `access_token` endpoint.

![](https://cdn.auth0.com/blog/swissid/activating-the-custom-connection-to-different-apps.png)

{% include tweet_quote.html quote_text="With Auth0, developers can support SwissID login alongside with other features like multifactor authentication, single sign on, etc." %}

## Styling the SwissID Connection on the Auth0 Login Page

By default, [the Auth0 Login page](https://auth0.com/docs/hosted-pages/login) will automatically display all connections enabled for an application. For first-class connections like Facebook and Google, the login page will automatically display an appropriate icon for the connection. However, for custom connections (such as SwissID), only a default icon is displayed.

If needed, you can tailor the look and feel for any connection on either the login or the registration dialogs. This allows you to host a logo for SwissID on your own CDN, and then tell Auth0's login page to use this logo. For more information, check our documentation on [how to customize your login page](https://auth0.com/docs/hosted-pages/login#how-to-customize-your-login-page)

{% include asides/about-auth0.markdown %}
