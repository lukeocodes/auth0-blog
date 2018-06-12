---
layout: post
title: "Introducing the Multi-Factor Authentication API"
description: "Embed Multi-Factor Authentication using push notifications, SMS, or TOTP anywhere, taking full control of the experience."
longdescription: "We have developed a new API to make use of Multi-Factor Authentication through an HTTP API, allowing developers to take full control of the experience. Push notifications, SMS, TOTP, and even multiple authenticators are supported. In this article we take a look at the API while developing a CLI application."
date: 2018-05-23 08:30
category: Announcements, Feature
author:
  name: Sebastián Peyrott
  url: https://twitter.com/speyrott?lang=en
  mail: speyrott@auth0.com
  avatar: https://en.gravatar.com/userimage/92476393/001c9ddc5ceb9829b6aaf24f5d28502a.png?size=200
design:
  bg_color: "#222228"
  image: https://cdn.auth0.com/blog/guide-to-oauth2/logo.png
  image_size: "100%"
  image_bg_color: "#222228"
tags:
- oauth
- oauth2
- mfa
- rfc
- ietf
- network
- working
- group
- multi-factor
- authentication
- authorization
- otp
- totp
- push
- push-notifications
- guardian
- authenticator
- google-authenticator
- duo
related:
- 2018-02-07-oauth2-the-complete-guide
- 2017-10-19-oauth-2-best-practices-for-native-apps
- ten-things-you-should-know-about-tokens-and-cookies
---

We are pleased to announce the availability of our [Multi-Factor Authentication API](https://auth0.com/docs/api-auth/tutorials/multifactor-resource-owner-password). Up to now, we have provided support for MFA through a simple switch in the Auth0 Dashboard, following our premise of *simplicity* and ease of use. However, we also believe in providing powerful *building blocks* for our users. For this reason we have developed a new API to perform the full MFA flow, even with multiple authenticators, in a flexible and convenient way. In this article we will take a look at the API and build a simple CLI app to demonstrate how to use it. Read on!

{% include tweet_quote.html quote_text="The MFA API is now available, learn how to embed MFA in your apps taking full control of the experience!" %}

> **Note**: for most MFA scenarios, we still recommend the use of [Universal Login](https://auth0.com/docs/hosted-pages/login) whenever possible. This new API is meant for advanced use cases where Universal Login is not flexible enough. [Universal Login is the best alternative](https://auth0.com/blog/authentication-provider-best-practices-centralized-login/) in most cases.

---

## Why?
Up to now, enabling MFA at Auth0 was simply a matter of flipping a switch and optionally selecting which application you wanted to enable MFA for. When MFA is enabled, hitting the `/authorize` endpoint or starting an authorization flow through one of our libraries was enough to trigger MFA. Simple, convenient.

![MFA Switch](https://cdn.auth0.com/blog/oauth2-mfa-api/1-mfa-enable.png)

However, some of our customers have requested to have more control over the MFA process. For this reason, we have been working with [ATEA Norge](https://www.atea.no/) and other customers under a private BETA program to find the right implementation for this feature. The result: a new API for MFA to give back control to you when required. Let's check it out.

> **Note**: for most MFA scenarios, we still recommend the use of [Universal Login](https://auth0.com/docs/hosted-pages/login) whenever possible. This new API is meant for advanced use cases where Universal Login is not flexible enough. [Universal Login is the best alternative](https://auth0.com/blog/authentication-provider-best-practices-centralized-login/) in most cases.

## A Simple API
The API works by introducing some changes to how the `/token` endpoint behaves. Let's take a look.

{% include tweet_quote.html quote_text="The new MFA API is simple, it works on top of the existing /token endpoint." %}

The API works in four simple steps:

1. Access token request to `/token`.
2. Challenge request to `/challenge`.
3. Asking the user for a code (if necessary).
4. Final request to `/token`.

> This scenario assumes the user is already enrolled in MFA and has at least one authenticator enabled. There's also a [new API for enrollment](https://auth0.com/docs/multifactor-authentication/api/otp) to have full control over the enrollment process. This API also allows for obtaining a list of associated authenticators, useful for using multiple authenticator, as we will see in step 2 below.

### 1. Access Token Request to `/token`
When a request is made to the `/token` endpoint to get an access token, normally you either get an error, or you get an access token. However, when the MFA API is enabled, the `/token` endpoint may return a new error code: `mfa_required`.

```
POST /oauth/token HTTP/1.1
...

{
  "username": "...",
  "password": "...",
  "grant_type": "password"
  ...
}
```

Response:

```
HTTP/1.1 403 Forbidden
...

{
  "error": "mfa_required",
  "mfa_token": "..."
}
```

The `mfa_token` in the response value must be used in the following requests.

> MFA is also supported for the `refresh_token` [grant type](https://auth0.com/docs/applications/application-grant-types). This allows for use cases in which you want to require 2FA (TOTP, SMS, push notifications) when refreshing an access token, useful for extra security and step-up authentication.

### 2. Perform an MFA Challenge
When the `mfa_required` error is returned, the client must then perform a `challenge`. This is done by sending a request to the `/mfa/challenge` endpoint.

```
POST /mfa/challenge HTTP/1.1
...

{
  "mfa_token": "<from step 1>",
  "challenge_type": "otp oob",
  "authenticator_id": "optional, if known"
}
```

Response:

```
HTTP/1.1 200 OK
...

{
  "challenge_type": "oob",
  "binding_method": "prompt",
  "oob_code": "..."
}
```

The response tells the client which type of authenticator will be used (`challenge_type`) and also returns additional arguments relevant for that method. The `binding_method` parameter tells the client it should ask the user for a code.

The client may optionally request a specific authenticator through `authenticator_id`. This allows for multiple authenticators, even for the same resource.

> `OOB` means "out-of-band". All methods that are not [TOTP](https://auth0.com/blog/from-theory-to-practice-adding-two-factor-to-node-dot-js/) are OOB.

### 3. Prompt For Input (If Necessary)
If the `challenge_type` is `otp` or the `binding_method` parameter is set to `prompt`, the client should ask the user to input a code before continuing.

### 4. Get The Access Token
Finally, a new request to the `/token` endpoint is performed.

```
POST /oauth/token HTTP/1.1
...

{
  "mfa_token": "<from step 1>",
  "oob_code": "<from step 2, optional>",
  "binding_code": "<from step 3, if 'binding_method' === 'prompt'>",
  "otp": "<from step 3, if challenge_type === 'otp'>",
  "grant_type": "<one of two strings, see below>"
}
```

Grant type must be one of two values:
- For OTP: `http://auth0.com/oauth/grant-type/mfa-otp`
- For OOB: `http://auth0.com/oauth/grant-type/mfa-oob`

Response:

```
HTTP/1.1 200 OK
...

{
  "access_token": "...",
  "expires_in": "...",
  ...
}
```

Note that certain methods do not require the user to input anything (push notifications, for example). For those cases, this request must be performed repeatedly until an access token is returned or the authorization is denied. If the client is expected to repeat the request again in the future (after a certain amount of time) the response will be:

```
HTTP/1.1 400 Bad Request
...

{
  "error": "authorization_pending"
}
```

This means the user has not performed any actions yet and the client should wait a bit before trying again. The `error` can also be `slow_down` if the client is not waiting enough between requests.

That's it! If all went well, you now have an access token issued through MFA!

## Example: A CLI App with MFA
To give you a better understanding of how the API works we have developed a [simple CLI app that uses MFA](https://github.com/auth0-blog/oauth2-mfa-cli-example).

<video src="https://cdn.auth0.com/blog/oauth2-mfa-api/cli-demo-2.m4v" controls autoplay loop></video>

Take a look at the [full code in GitHub](https://github.com/auth0-blog/oauth2-mfa-cli-example). You will find the interesting bits in the `/token-endpoint.mjs` file, particularly in the `token` function.

To use the app, make sure that:
- You have created a new [Application](https://manage.auth0.com/#/applications) in the Auth0 Dashboard. Select the `Machine-to-Machine type`.
- The `token authentication method` is set to `none` in the Auth0 Application settings.
- You have enabled the `Password` and `MFA` grants in `Application -> Advanced Settings -> Grant Types`.
- You have MFA enabled. Go to [Multifactor Auth](https://manage.auth0.com/#/guardian) and enable SMS and push notifications for [Guardian](https://auth0.com/docs/multifactor-authentication/guardian/user-guide).
- You have set the right client ID (get this from the [application](https://manage.auth0.com/#/applications) settings area) in the MFA rule in the [Multifactor Auth](https://manage.auth0.com/#/guardian) section. Otherwise MFA will be enabled for all clients.

To use the app simply do:

```sh
git clone git@github.com:auth0-blog/oauth2-mfa-cli-example.git
cd oauth2-mfa-cli-example
npm install
node src/index.js login
```

The app supports some other commands. You can take a look at them by running it with no arguments.

## Other Use Cases
The sky is the limit! Here are some scenarios where using the new MFA API could make sense:

- Protect a VPN like OpenVPN (Pritnul) or Juniper with a second factor.
- Integrate with a PAM module to do second factor with SSH.
- Integrate with an LDAP service to provide a second factor.
- Step up authentication when refreshing an `access_token` (`refresh_token` grant).
- Trigger second factor from any backend process.

You are now free to build powerful MFA flows wherever you want!

## Conclusion
The MFA API brings flexibility to the use of MFA in your apps. One thing you may have noticed is that we are using the [OAuth 2.0 `/token` endpoint](https://tools.ietf.org/html/rfc6749#section-3.2). In fact, we designed this to be compatible with OAuth 2.0 right from the start. We have prepared two specification drafts, [one for MFA](https://github.com/jaredhanson/draft-oauth-mfa/blob/master/Draft-1.0.txt), [the other for authenticator association](https://github.com/jaredhanson/draft-oauth-mfa-assoc/blob/master/Draft-1.0.txt), to be sent to the [IETF OAuth 2.0 Working Group](https://tools.ietf.org/wg/oauth/). Other implementations of OAuth 2.0 can follow these specifications to implement MFA in a flexible, yet powerful way, while remaining compatible. We will have more information about this in an upcoming post.

We can't wait to see how you use this API to build powerful multi-factor flows! Let us know what you think in the comments.

{% include tweet_quote.html quote_text="We can't wait to see how you use this API to build powerful multi-factor flows!" %}
