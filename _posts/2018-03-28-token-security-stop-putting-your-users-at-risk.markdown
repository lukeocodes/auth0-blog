---
layout: post
title: "Authentication best practices: Single-Page Applications"
description: "Learn about authentication best practices in Single-Page applications. How to authenticate and how to store access tokens securely."
date: 2018-03-28 23:23
category: Hot Topics, Security, Access Tokens
author:
  name: Luke Oliff
  url: https://twitter.com/mroliff
  avatar: https://avatars1.githubusercontent.com/u/956290?s=200
  mail: luke.oliff@auth0.com
design:
  bg_color: "#222228"
  image: https://cdn.auth0.com/blog/series-c/auth0-logo.png
tags:
- security
- access-tokens
- token-storage
- localStorage
- jwt
- auth
- breaches
related:
- 2018-02-27-8-ways-to-avoid-healthcare-breaches
- 2018-02-14-what-is-data-security
- 2018-03-22-cambridge-analytica-and-facebook
---


persona: SPA/frontend developers who works with React (but is familiar with Angular and/or Vue). They have used Google Sign-in and similar offerings using identity provider's own SDKs such as Google Platform, but have no specific experience with OIDC
goals (after reading this article, the reader will understand):
  - what is OIDC is and what is implicit grant
  - how to protect an SPA using OIDC
  - the tradeoffs between storing access_tokens in localStorage and memory
  - how to verify an SPA access_token in an API
  - how Auth0 can help secure their app
  - auth0's position and how it is reflected in our quickstarts, documentation and blog posts







**TL;DR:** Single-Page Applications (SPAs) aren't always the easiest thing to keep secure. But if you're developing an SPA, there are some best practices that you should know. After reading this article, I hope to have given you the tools you need to keep your SPAs as secure as they can be.

## What is OIDC and how can it help me?

OpenID Connect (OIDC) is an identity layer that sits on top of [OAuth 2.0](https://auth0.com/blog/oauth2-the-complete-guide/), which is an authorization framework. It's was designed to allow a [**client**](https://auth0.com/identity-glossary#client) to identify an already authenticated user. 

The first time the user authenticates with an authorization server that conforms to OIDC, they are prompted to authorize the clients claim to certain resources. This part of the process is defined by OIDC. It’s the client (e.g. auth0.com) claiming access to information from a resource, which in this case includes an identity provider (e.g. linkedin.com)
 
![Auth0 requesting access to my LinkedIn profile using OIDC](/Users/olaf/Desktop/auth0-requesting-access-to-linkedin-profile-via-oidc.png)
 
Once authorized the client receives an `access_token` that holds its claim to the resource. The `access_token` can be used to make requests to a protected endpoint on the resource to fetch this data, and nothing else. Tokens ***generally*** expire quick enough, so that if a bad-actor were to gain a copy of the `access_token`, the damage will be limited to a smaller window. 

You'll hear the term **Flow** mentioned a lot in identity posts, and this one is no different—***sorry***.

**Flows** are an underpinning of the OAuth 2.0 specification and they describe different methods to securely identify a user from different client types. The limitations and needs of a website (with a backend server) and a mobile app are quite different. They both have the requirement to identify a user, but they might not be able to do it in quite the same way. **Flows** address our different requirements and we have a great article to help explain [which OAuth flow to use](https://auth0.com/docs/api-auth/which-oauth-flow-to-use).

When you're building an SPA and you don't have a backend server, you're requirements are different again. Auth0 provides a [nifty SDK for JavaScript](https://auth0.com/docs/libraries/auth0js/v9) that allows you log in a user, gain an access token and use it for authorized requests to you

![Single-Page Applications (SPAs) aren't always the easiest thing to keep secure](https://i.redd.it/5zm413x9m7c01.png)

## Implicit