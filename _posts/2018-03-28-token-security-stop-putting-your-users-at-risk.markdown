---
layout: post
title: "Token Security: Stop Putting Your Users At Risk"
description: "Access tokens represent your authorization to access your application. We don’t store our login details insecurely. Why would we risk exposing our access tokens?"
date: 2018-03-28 23:23
category: Hot Topics, Security
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

**TL;DR:** We explore keeping your tokens and your user’s data safe. The [Web Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) has been used as an example of how to store access tokens, especially in single-page applications (SPAs). Access tokens represent your authorization to access your application. We don’t store our login details insecurely. Why would we risk exposing our access tokens?

## Storing tokens on the front-end, don’t do it

Access tokens can be used by a client to access a service. Just like credentials, they should be kept safe and secure.

{% include tweet_quote.html quote_text="Are you putting your users at unnecessary risk? If you store access tokens in your front-end app, you might be!" %}

You should not be storing any personal data or access tokens anywhere they can be accessed by a bad actor. Attackers are actively looking for places you might be exposing sensitive data and they’re using more elaborate methods all the time.

A common place for storing tokens on the front-end is JavaScript’s [Web Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage). Storing anything as sensitive as personal data, access tokens, and credentials with `localStorage` should be considered insecure.

The benefit of the `Web Storage API` is that it’s a client API that can be accessed by JavaScript, and allows you to store up to 5MB of string data. With `localStorage`, storage persists on that domain until such time as it’s deleted by the user or the site. This is unlike `sessionStorage` only in that `sessionStorage` persists data limited to the length of that browsing session.

The problem with the `Web Storage API` is that it assumes anyone who _can_ access it, also has _permission_ to access it. This is because its only access control is that is must be from the same origin. But, this means it’s vulnerable to XSS attacks. Especially so, because it’s stored in such a readily available format. It’s easier to use, but easier to abuse. For example, running the JavaScript below in your browser console will loop over every item on that site's `localStorage` and output the keys **and the values**.

```js
Object.keys(localStorage).forEach(function(key){
   console.log(`${key}: ${localStorage.getItem(key)}`);
});
```

> “So what? That doesn’t help an attacker get your tokens. That's just what I can run on my console!” —You (maybe)

Let's speak theoretically. Imagine for a moment that an attacker can place some JavaScript on your site. Replace the `console.log()` above with an [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) script and **off goes your users' data to some other server**.

What if you’re using [Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) to stop anything but your trusted vendor's scripts from running on your application? What if you’re using [Subresource Integrity](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity) to ensure even those vendor scripts can’t open up vulnerabilities on your application? Those **are** pretty good ways to protect your application, but there are other general security concerns around exposing tokens to the large variety of attack vectors in the browser. [Meltdown](https://meltdownattack.com/) and [Cloudbleed](https://en.wikipedia.org/wiki/Cloudbleed) are both good examples where vulnerabilities were discovered that could take advantage of person data and tokens being stored in the frontend.

{% include tweet_quote.html quote_text="Let's speak theoretically. Imagine for a moment that an attacker can place some JavaScript on your site. Would you store your access token where they could find it?" %}

## The complexity of web applications

In the 1990s world of basic web development, you had monolithic applications that handled user management, stored sensitive data, processed requests and returned some HTML to our screen. Those applications acted as both our server and our client application, all rolled into one.

In the years that followed, developers made that HTML prettier and prettier, and more interactive. But they essentially still had one application which kept our users' credentials and session secure, for the most part, in the backend and still returned HTML to the front-end. Our monolithic application is **one single application**, and it's just sending HTML down to our browser, which is more or less just a dumb client that lets us click on our new pretty buttons and links.

All you need to do with each request is to identify the user is who they were at the start of their browsing session. In such a case you would use cookies, because that's what they were designed for: a single party that has no need to expose anything with any potential sensitivity.

Roll on two decades of user experience improvements and technological advancements and you land smack bang in the world of single-page applications (SPAs). An SPA is essentially software that runs in the browser. When you load up a new website, you download the code that comes with all the necessaries to be able to communicate with a server.

The server remains essentially the same as our old 1990s application, but it’s learned some tricks. It has had an upgrade. It only talks in computer language now, sending and receiving code instead of giving you a nice pretty interface with button and links. It’s now called an API and it leaves the interface up to the SPA.

Web tokens were designed for use in cases where there are *two or more applications*. An example of this would be our API and our SPA. It allows the SPA to talk to the API, where you can identify the authorized user. If you don't protect your tokens with all the responsibility you give other credentials and then the token is accessed by a bad actor, they could access your API on behalf of that user.

**Now imagine that the API is a bank, and the SPA is a user's online banking software.**

## Auth0 is moving away from token-based auth

A lot of people will still go ahead and use `localStorage` assuming they’ll never be vulnerable to the variety of attacks out there.

That’s okay, but the responsible thing for Auth0 to do **is present you with the most secure way** we can.

Until recently, we presented simple and common steps to secure your SPAs against XSS and other attack vectors. This was done as a way to make our guides simple to understand and follow, which is one of the strengths of the Web Storage API. The reality is that the only way to build secure applications is to do absolutely **everything** you can to secure them, because ***attackers are always one step ahead***.

It’s not just Auth0 though, [more and more developers](https://www.rdegges.com/2018/please-stop-using-local-storage/) and [groups are putting up red flags on token storage](https://www.owasp.org/index.php/JSON_Web_Token_(JWT)_Cheat_Sheet_for_Java#Token_storage_on_client_side).

## Your returning users can still re-authenticate

So we’ve discussed the risk of storing tokens with the `Web Storage API` and how you might mitigate the risk if you're determined to do it that way. But as we said, that’s not enough. We need to show you the right way an SPA can authenticate and re-authenticate a returning user without that token appearing from `localStorage`.

The [`auth0-js` SDK](https://auth0.com/docs/libraries/auth0js) `checkSession()` method allows you to acquire a new token from Auth0 for a user who is already authenticated against Auth0 for your domain. The method accepts any valid OAuth 2.0 parameters that would normally be sent to our `authorize()` method, which is used to initiate authentication in the first place. This allows you to re-authenticate returning users without any dramatic re-engineering. Users won't be presented with a login screen unless required. `checkSession` will get a new token transparently when certain conditions are met.

The Auth0 guides would normally include an authentication utility script to provide some much-needed functions for authentication on an SPAs. Below you'll find an example using the `auth0-js` SDK. **This example isn't working code and shouldn't be expected to run**, but it gives you a good idea of what methods you should be looking to use in order to avoid storing tokens with the `Web Storage API`.

Our utility script needs our `auth0-js` SDK loaded and then it needs to configure it. You might load it from Node modules using [RequireJS](http://requirejs.org/), or just use it from the CDN using traditional `script` tags.

Here is the start of the utility script, that uses the `auth0-js` SDK.

```js
// authutil.js
window.addEventListener('load', function() {
  // const auth0 = require('auth0-js');
  var webAuth = new auth0.WebAuth({
    // ... configure auth0-js
  });
});
```

When someone clicks on login, you need to call `webAuth.authorize()`. This redirects the user to our identity service. When a user is authenticated they're sent back to the application, usually to a callback URL.

The utility script is designed to try and handle the callback when the script is loaded. It tries to read the authorization coming from the identity service, from the URL your user is sent back to.

```js
// authutil.js
window.addEventListener('load', function() {
  // ...
  
  function handleCallback() {
    if (window.location.hash !== undefined) {
      webAuth.parseHash(
        handleLogin(errors, authResult) // handle and store logged in user
      );
    }
  }
  
  handleCallback();
});
```

Previously, to handle writing and removing user access from the utility script, you would have two functions: One to add the info to `localStorage` and one to remove it. The callback would have parsed the authorization and stored the token in the SPA.

But, we can achieve the same result without exposing the token to the `Web Storage API`.

```js
// authutil.js
var accessToken = undefined;

window.addEventListener('load', function() {
  // ...
  
  function handleLogin(errors, authResult) {
    localStorage.setItem('expires', authResult.expiresIn); // magic with expires time
    accessToken = authResult.accessToken; // store our token in app memory
  }

  function handleLogout() {
    localStorage.removeItem('expires');
    accessToken = undefined;
  }
});
```

Using this setup as it is, a user on your SPA application would remain logged in until they re-request the SPA from the server. Something like a page refresh or browsing around without using the SPA's router will likely log them out.

To re-authorize a user when they return to the site, if they refresh, or clicked a normal link, the application can use `checkSession` in the utility class like this.

```js
// auth.js
window.addEventListener('load', function() {
  // ...
    
  function handleReturningUser() {
    if (localStorage.setItem('expires') > now) { // if session hasn't expired do a checkSession
      webAuth.checkSession({}, 
        handleLogin(errors, authResult) // handle and store logged in user
      );
    }
  }

  handleReturningUser();
});
```

At this point you might be wondering how `checkSession()` works, right?

`checkSession()` works by creating an invisible iframe which makes use of traditional Single Sign-on at your Auth0 domain – `your-domain.auth0.com/authorize` for example. What this request does is return a new `token` to our SPA.

It returns tokens if the user is still logged in to the authentication server. The authentication server uses a session to know whether the user is authenticated or not, just like like going back to Facebook a day later and it logging users back in.

That session will expire eventually, according to authorization server configuration. If the session has expired, the application will receive an `interaction_required` error in the response, which is when it’s time to log in again!

## Conclusion

Access tokens represent the application's permission access to some of your user data, until that expires.

Unnecessary risks with access tokens such as storing them in `localStorage` and storing them incorrectly in a cookie leaves them vulnerable to many attack vectors, some of which were only recently being discovered ([Meltdown](https://meltdownattack.com/) and [Cloudbleed](https://en.wikipedia.org/wiki/Cloudbleed)). While some of these attacks can be defended against, it’s only responsible of us to present you with a more secure way to handle access tokens and personal data.

That's why we're recommending using Single Sign-on to fetch your access token and only keep it in memory. Do not access or store unnecessary personal data in your frontend application. The [Auth0.js](https://auth0.com/docs/libraries/auth0js) library can help with this by retrieving a token from our authentication servers using Single Sign-on, to ensure we're never storing anything as sensitive as access tokens and personal data in the frontend.