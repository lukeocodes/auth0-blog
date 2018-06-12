---
layout: post
title: "Token Security: Stop Putting Your Users At Risk"
description: "Access tokens are a credential that can be used by a client to access a service, like an API. Like any credential, passwords for instance, they should be kept safe and secure."
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
- 2018-03-22-cambridge-analytica-and-facebook§t
---

**TL;DR:** We explore keeping your tokens and your user’s data safe. The [Web Storage API](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) has been used as an example of how to store access tokens, especially in single-page applications (SPAs). Auth0 has an alternative to storing a token for a persistent user session, so your returning users can stay logged in without putting them at unnecessary risk.

## Storing tokens on the front-end, don’t do it

Access tokens are a credential that can be used by a client to access a service, like an application programming interface (API). Like any credential, passwords for instance, they should be kept safe and secure.

{% include tweet_quote.html quote_text="Are you putting your users at unnecessary risk? If you store access tokens in your front-end app, you might be!" %}

You should not be storing anything as sensitive as credentials anywhere they can be accessed by a third party. Storing them your front-end application is asking for trouble. Attackers are actively looking for places you might be exposing sensitive data and they’re using more elaborate methods all the time. The last thing you need to do is make it easy for them.

A common place for storing tokens on the front-end is JavaScript’s [`Web Storage API`](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage), which should be considered insecure.

The benefit of the `Web Storage API` is that it’s purely JavaScript and allows you to store up to 5MB of data. The data can be stored as string data only, therefore it has no logic or state. But it can be serialized along with its context (i.e. as JSON object). With `localStorage`, storage persists on that domain until such time as it’s deleted by the user or the site. This is unlike `sessionStorage` only in that `sessionStorage` persists data limited to the length of that browsing session.

The problem with the `Web Storage API` is that it assumes anyone who _can_ access it, also has _permission_ to access it. This is because it’s only accessible by JavaScript and based on the current origin or domain. But, this means it’s vulnerable to XSS attacks. Especially so because it’s stored in such a readily available format. It’s easier to use, but easier to abuse. For example, running the JavaScript below in your browser console will loop over every item on that site's `localStorage` and output the keys **and the values**.

```js
Object.keys(localStorage).forEach(function(key){
   console.log(`${key}: ${localStorage.getItem(key)}`);
});
```

> “So what? That doesn’t help an attacker get your tokens. That's just what I can run on my console!” —You (maybe)

Let's speak theoretically. Imagine for a moment that an attacker can place some JavaScript on your site. Replace the `console.log()` above with an [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) script and **off goes your users' data to some other server**.

What if you’re using [Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) to stop anything but your trusted vendor's scripts from running on your application? What if you’re using [Subresource Integrity](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity) to ensure even those vendor scripts can’t open up vulnerabilities on your application? Those **are** pretty good ways to protect your application! ***Those security steps should be taken anyway!*** But can you be 100% certain it’s safe? Avoiding storing tokens with the `Web Storage API` is the best way.

**Remember, having someone’s access token can be as good as having their username and password, and you wouldn’t ever risk exposing your username and password.**

{% include tweet_quote.html quote_text="Having someone’s access token can be as good as having their username and password, and you wouldn’t ever risk exposing your username and password" %}

## The complexity of web applications

In the 1990s world of basic web development, you had monolithic applications that handled user management, stored sensitive data, processed requests and returned some HTML to our screen. Those applications acted as both our server and our client application, all rolled into one.

In the years that followed, developers made that HTML prettier and prettier, and more interactive. But they essentially still had one application which kept our users' credentials and session secure, for the most part, in the backend and still returned HTML to the front-end. Our monolithic application is **one single application**, and it's just sending HTML down to our browser, which is more or less just a dumb client that lets us click on our new pretty buttons and links.

At this point, the browser is not actively involved in any logic. All you need to do with each request is to identify the user is who they were at the start of their browsing session. In such a case you would use cookies, because that's what they were designed for: a single party that has no need to expose anything with any potential sensitivity.

Roll on two decades of user experience improvements and technological advancements and you land smack bang in the world of single-page applications (SPAs). An SPA is essentially software that runs in the browser. When you load up a new website, you download very thin code that comes with all the necessaries to be able to communicate with a server.

The server remains essentially the same as our old 1990s application, but it’s learned some tricks. It has had an upgrade. It only talks in computer language now, HTTP requests sending and receiving JSON payloads. It’s now called an API and it doesn't return HTML anymore. Instead, it leaves the HTML up to the SPA.

Web tokens were designed for use in cases where there are *two or more applications*, such as our API and our SPA. It allows the SPA to talk to the API, where you can identify the authorized user. If you don't protect your tokens with all the responsibility you give other credentials and then the token is accessed by a bad actor, they could access your API on behalf of that user.

**Now imagine that the API is a bank, and the SPA is a user's online banking software.**

## Auth0’s old guides, they store tokens in localStorage!

We know and we’re working really hard to update our existing guides.

A lot of people will still go ahead and use `localStorage` assuming they’ll never be vulnerable to XSS attacks, having taken the appropriate steps to protect against them.

That’s okay, but the responsible thing for Auth0 to do **is teach you the most secure way**.

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

`checkSession()` works by creating an invisible iframe which makes a request to `the-users-domain.auth0.com/authorize`. What this request does is return a new `token` to the application.

It returns tokens if the user is still logged in to the authentication server. The authentication server uses normal `web sessions` to know whether the user is authenticated or not, just like like going back to Facebook a day later and it logging users back in. They can still get a `token` from the authentication server while their session still exists.

That session will expire eventually, according to authorization server configuration. If the session has expired, the application will receive an `interaction_required` error in the response, which is when it’s time to log in again!

## Conclusion

Access tokens are to an application what usernames and passwords are to a login box, representing a user until expiry.

Unnecessary risks with access tokens such as storing them in `localStorage` and storing them incorrectly in a cookie leaves them vulnerable to many attack vectors. While these attacks can be defended against, it’s only responsible of us to teach you the most secure way to handle authentication.

That is why we're recommending that access tokens aren't stored with the `Web Storage API`, and if they're stored in a cookie then you should apply `HttpOnly`, `SameSite` and `Secure`  directives. Additionally, cookies can have `__Secure` and `__Host` prefixes. For more information on securing cookies, [read about common threats in web application security and how to prevent them](https://auth0.com/blog/common-threats-in-web-app-security/).

Instead, the [Auth0.js](https://auth0.com/docs/libraries/auth0js) library can help with user authentication and with re-authentication of returning users by retrieving a token from our authentication servers using a web session.