---
layout: post
title: "Token Security: Stop Putting Your Users At Risk"
description: "Access tokens are a credential that can be used by a client to access a service, like an API. Like any credential—passwords for instance—they should be kept safe and secure."
longdescription: "Are you putting your users at unnecessary risk? If you store access tokens in your frontend app, you might be! Access tokens are a credential that can be used by a client to access a service, like an API. Like any credential—passwords for instance—they should be kept safe and secure."
date: 2018-03-28 23:23
category: Hot Topics, Security
author:
  name: Luke Oliff
  url: https://twitter.com/mroliff
  avatar: https://avatars1.githubusercontent.com/u/956290?s=200
  mail: luke.oliff@auth0.com
design:
  bg_color: <A HEX BACKGROUND COLOR>
  image: <A PATH TO A 200x200 IMAGE>
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

**TL;DR:** We explore keeping your tokens and you users data safe. `localStorage` has been used as an example of how to store access tokens, especially in single-page applications (SPAs). Auth0 has an alternative to storing a token for a persistent user session, so your returning users can stay logged in without putting them at unnecessary risk.

## Storing tokens on the frontend—don’t do it

Access tokens are a credential that can be used by a client to access a service, like an API. Like any credential—passwords for instance—they should be kept safe and secure.

{% include tweet_quote.html quote_text="Are you putting your users at unnecessary risk? If you store access tokens in your frontend app, you might be!" %}

We should not be storing anything as sensitive as credentials anywhere they can be accessed by a third party and unfortunately storing it in your frontend application is asking for trouble.

Attackers are actively looking for places we might be exposing sensitive data and they’re using more elaborate methods all the time. The last thing we need to do is make it easy for them.

A common place for storing tokens—which should be considered insecure—is JavaScript’s [`Web Storage API`](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage), which includes `localStorage` and `sessionStorage`.

The benefits of these are that they're purely JavaScript and allow you to store up to 5MB of data. The data can be stored as string data only. Therefore it has no logic or state. But it can be serialized along with its context (i.e. as JSON object). With `localStorage`, storage persists on that domain until such time as it's deleted by the user or the site. This is unlike `sessionStorage` only in that `sessionStorage` persists data limited to the length of that browsing session.

The problem with `Web Storage` is that it assumes anyone who can access it, also has permission to access it. This is because it’s only accessible by JavaScript and based on the current origin or domain. This also means it’s vulnerable to XSS attacks. Especially so because it's stored in such a readily available format. For example, running the JavaScript below in your browser console will loop over every item in your `localStorage` and output the keys **and the values**.

```js
Object.keys(localStorage).forEach(function(key){
   console.log(`${key}: ${localStorage.getItem(key)}`);
});
```

> So what? That doesn’t help an attacker get your tokens. That's just what I can run on my console!

Let's speak theoretically. Imagine for a moment that an attacker can place some JavaScript on your site. Replace the console log above with an [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) and **off goes your data to some other server**.

What if you’re using [Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) to stop anything but my trusted vendor's scripts from running on your application? What if you’re using [Subresource Integrity](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity) to ensure even those vendors scripts can’t open up vulnerabilities on your application? Those **are** pretty good ways to protect your application!

**Those security steps should be taken anyway!**

But can you be 100% certain it's safe? Avoiding storing tokens on the frontend is the best way.

**Remember, having someone’s access token can be as good as having their username and password, and you wouldn’t ever risk exposing your username and password.**

{% include tweet_quote.html quote_text="Having someone’s access token can be as good as having their username and password, and you wouldn’t ever risk exposing your username and password" %}

## The complexity of web applications

In the 1990's world of basic web development, we had monolithic applications that handled user management, stored sensitive data, processed requests and returned some HTML to our screen, acting as both our server and our client applications, all rolled into one.

In the years that followed, we made that HTML prettier and prettier, and more interactive. But we essentially still had one application which kept our user's credentials and session secure—for the most part—in the backend and still returned HTML to the frontend. Our monolithic application is **one single application**, and its just sending HTML down to our browser, which is more or less just a dumb client that lets us click on our new pretty buttons and links.

At this point, the browser is not actively involved in any logic. All we need with each request is to identify the user is who they were at the start of their browsing session. In such a case one would use cookies, because that's what they were designed for, a single party that has no need to expose anything with any potential sensitivity.

Roll on two decades of user experience improvements and technological advancements and we land smack bang in the world of single-page applications (SPAs). An SPA is essentially software that runs in the browser. When we load up our new website, we download very thin code (we hope) that comes with all the necessaries to be able to communicate with a server.

The server is essentially the same as our old 1990's application, but it's learned some tricks. It has had an upgrade. It only talks in computer language now – HTTP requests sending and receiving JSON payloads. We now call it an application programming interface (API) and it doesn't give us HTML anymore. Instead, it leaves that up to the SPA.

Web tokens were designed for cases like this, in which there are *two applications*, such as our API and our SPA. It allows the SPA and API to identify a user. But if we don't protect our tokens with all the responsibility other credentials (usernames and passwords) deserve, and the token it's accessed by a bad actor, then they can access our API on behalf of that user.

**Now imagine that the API is your bank, and the SPA is your bank's online banking software.**

## Auth0’s old guides—they store tokens in localStorage!

We’re way ahead of you! We’re working hard to update our existing guides.

A lot of people will still go ahead and use `localStorage` assuming they’ll never be vulnerable to XSS having taken the appropriate steps to protect against it.

That’s ok, but it’s only responsible for us to teach you the **most secure way**.

Until recently, we believed that with simple and common steps to secure your SPAs against XSS and other attack vectors, it was okay to store tokens in `localStorage`. It made our guides very simple to follow. The reality is that the only way to build secure appliations is to do absolutely **everything** you can to secure them, because ***attackers are always one step ahead***.

It's not just Auth0 though, [more](https://www.rdegges.com/2018/please-stop-using-local-storage/) and [more](https://www.owasp.org/index.php/JSON_Web_Token_(JWT)_Cheat_Sheet_for_Java#Token_storage_on_client_side) developers and groups are putting up red flags on token storage.

## Logging in your returning users, the right way

So we’ve discussed the risk of storing tokens with the `Web Storage API` and how you might mitigate the risk if you're determined to do it that way. But as I said, that’s not enough for us. We need to show you the right way an SPA can authenticate a returning user without that token appearing from `localStorage`.

The [Auth0.js](https://auth0.com/docs/libraries/auth0js) `checkSession()` method allows you to acquire a new token from Auth0 for a user who is already authenticated against Auth0 for your domain. The method accepts any valid OAuth2 parameters that would normally be sent to our `authorize()` method, which is used to initiate authentication.

Our guides would normally include an authentication utility script to provide some much-needed functions for authentication on our SPAs. So below you'll find an example using our `auth0-js` SDK. This example isn't working code and shouldn't be expected to run, but it gives you a good idea of what methods you should be looking to deploy in order to avoid storing tokens using the `Web Storage API`.

Our utility script needs our `auth0-js` SDK loaded and then it needs to configure it. You might load it using a loader like CommonJS or RequireJS, or fetch it from our CDN using traditional `script` tags. 

So here is the start of our utilily script, that uses our `auth0-js` SDK.

```js
// authutil.js
window.addEventListener('load', function() {
  // const auth0 = require('auth0-js');
  var webAuth = new auth0.WebAuth({
    // ... configure auth0-js
  });
});
```

Our application will call `webAuth.authorize()` which redirects the user to our identity service. When a user is authenticated they're sent back to our website, usually to a callback URL.

Our utility script will read the authorization from the identity service our of the URL when we send a user back to your application. This enables the first step.

```js
// authutil.js
window.addEventListener('load', function() {
  // ...
  
  function handleCallback() {
    webAuth.parseHash(
      handleLogin(errors, authResult) // handle and store logged in user
    );
  }
});
```

Previously we'd do something like this in our utility class:

```js
// authutil.js
window.addEventListener('load', function() {
  // ...
  
  function handleLogin(errors, authResult) {
    localStorage.setItem('expires', authResult.expiresIn); // magic with expires time
    localStorage.setItem('accessToken', authResult.accessToken); // store our token in localStorage! YUCK!
  }

  function handleLogout() {
    localStorage.removeItem('expires');
    localStorage.removeItem('accessToken');
  }
});
```

Instead, now we'll do this:

```js
// authutil.js
window.addEventListener('load', function() {
  // ...
  
  function handleLogin(errors, authResult) {
    localStorage.setItem('expires', authResult.expiresIn); // magic with expires time
    var accessToken = authResult.accessToken; // store our token in the DOM
  }

  function handleLogout() {
    localStorage.removeItem('expires');
    var accessToken = undefined;
  }
});
```

If we use this utility as it is, in an SPA you'll remain logged in until we re-request the SPA from the server. So, something like a page refresh or browsing to another page without using your SPA's router will likely log you out.

If we want to re-authorize a user when they return to the site, or if they refresh, we can add something like this to our utility class:

```js
// auth.js
window.addEventListener('load', function() {
  // ...
    
  function handleReturningUser() {
    if (localStorage.setItem('expires') < now) {
      webAuth.checkSession({}, 
        handleLogin(errors, authResult) // handle and store logged in user
      );
    }
  }

  handleReturningUser();
});
```

So at this point you might be wondering how `checkSession()` works, right?

`checkSession()` works by creating an invisible iframe which makes a request to `yourdomain.auth0.com/authorize`. What this request does is return a new `access_token` to your application (and optionally a new `id_token`).

It returns these tokens if the user is still logged in to the authentication server. The authentication server uses normal `web sessions` to know whether you're you and whether you're still logged in. Just like going back to Facebook a day later and it knowing who you are. You'll still be able to get an `access_token` from the authentication server while that session still exists.

That session will expire—eventually—according to authorization server configuration. If the session has expired, your application will receive an `interaction_required` error in the response, which is when it's time to show the login box again!

## Conclusion

Access tokens are to an application what your username and password are to a login box. Access tokens represent a user until it expires (if it expires).

Unnecessary risks with access tokens such as storing them in `localStorage` and storing them incorrectly in a Cookie leaves them vulnerable to XSS and CSRF attacks. While these attacks can be defended against, it's only responsible of us to teach you the most secure way to handle authentication.

That is why we're recommending that access tokens aren't stored in `localStorage` and if they're stored in a Cookie then you should apply `HttpOnly` and `SameSite` directives.

Instead, the [Auth0.js](https://auth0.com/docs/libraries/auth0js) library has a method of retrieving a token from our authentication servers using a web session. You can call this method on page load, so an authenticated user can be logged back in.