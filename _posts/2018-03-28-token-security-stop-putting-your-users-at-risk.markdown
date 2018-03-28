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

**TL;DR:** We explore keeping your tokens and you users data safe. `localStorage` has been used as an example of how to store access tokens, especially in SPAs. Auth0 has an alternative to storing a token for a persistent user session, so your returning users can stay logged in without putting them at unnecessary risk.

## Storing tokens on the frontend—don’t do it

Access tokens are a credential that can be used by a client to access a service, like an API. Like any credential—passwords for instance—they should be kept safe and secure.

{% include tweet_quote.html quote_text="Are you putting your users at unnecessary risk? If you store access tokens in your frontend app, you might be!" %}

We should not be storing anything as sensitive as credentials anywhere they can be accessed by a third party and unfortunately storing it in your frontend application is asking for trouble.

Attackers are actively looking for places we might be exposing sensitive data and they’re using more elaborate methods all the time. The last thing we need to do is make it easy for them.

A common place for storing tokens—which should be considered insecure—is JavaScript’s [`localStorage`](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage).

The benefits of `localStorage` are that it’s purely JavaScript and it allows you to store up to 5MB of data. The data is string data only, has no logic or state, but it can be serialized with it’s context (i.e. a JSON object). It also allows for the storage of data against an origin that spans browsing sessions, or only lasts for that session if you use `sessionStorage`.

The problem with `localStorage` is that it assumes anyone accessing it has permission to access it. This is because it’s only accessible by JavaScript and based on the current origin or domain. That means it’s vulnerable to XSS attacks.

For example, running the JavaScript below in your browser console will loop over every item in your `localStorage` and output the keys and values.

```js
Object.keys(localStorage).forEach(function(key){
   console.log(`${key}: ${localStorage.getItem(key)}`);
});
```

> So what? That doesn’t help an attacker get your tokens. That's just what I can run on my console!

Let's speak theoretically. Imagine for a moment that an attacker can place some JavaScript on your site. Replace the console log above with some [XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) code, and **off goes your data to someone else's servers**.

What if you’re using [Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP) to stop anything but my trusted vendors scripts from running on your site? What if you’re using [Subresource Integrity](https://developer.mozilla.org/en-US/docs/Web/Security/Subresource_Integrity) to ensure even those vendors scripts can’t open up vulnerabilities on your site? Those **are** pretty good ways to protect your `localStorage`.

**Those security steps should be taken anyway!**

But can you be 100% certain it's safe? Avoiding storing tokens on the frontend is the only sure way.

**Remember, having someone’s access token can be as good as having their username and password, and you wouldn’t ever risk exposing your username and password.**

{% include tweet_quote.html quote_text="Having someone’s access token can be as good as having their username and password, and you wouldn’t ever risk exposing your username and password" %}

## Auth0’s other guides—they store tokens

We’re way ahead of you! We’re working hard to update our existing guides.

A lot of people will still go ahead and use `localStorage` assuming they’ll never be vulnerable to XSS having taken the appropriate steps to protect against it.

That’s ok, but it’s only responsible for us to teach you the **most secure way**.

Until recently, we believed that with simple and common steps to secure your frontend applications and SPAs, it was okay to store tokens in `localStorage`, and it made our guides very simple to follow. The reality is that the only way to build secure apps is to do absolutely **everything** you can to secure them, because ***attackers are always one step ahead***.

It's not just us though, [more](https://www.rdegges.com/2018/please-stop-using-local-storage/) and [more](https://www.owasp.org/index.php/JSON_Web_Token_(JWT)_Cheat_Sheet_for_Java#Token_storage_on_client_side) developers and groups are putting up red flags on token storage.

## Logging in your returning users, the right way

So we’ve discussed the risk of storing tokens in local storage and how you might mitigate the risk. But as I said, that’s not enough for us. We need to show you the right way an SPA can authenticate a returning user without that token appearing from local storage.

The Auth0js `checkSession()` method allows you to acquire a new token from Auth0 for a user who is already authenticated against Auth0 for your domain. The method accepts any valid OAuth2 parameters that would normally be sent to `authorize`.

Our guides would normally include an authentication utility class to handle the Auth0 webauth object and provide some much needed functions.

Let's review the auth service class from one of [our more popular Angular apps](https://github.com/auth0-blog/angular-auth0-aside), highlighting the differences.

This is the older version of our AuthService class.

```js
// src/app/auth/auth.service.ts

import { Injectable } from '@angular/core';
import { BehaviorSubject } from 'rxjs/BehaviorSubject';
import * as auth0 from 'auth0-js';
import { AUTH_CONFIG } from './auth0-variables';
import { UserProfile } from './profile.model';

@Injectable()
export class AuthService {
  // Create Auth0 web auth instance
  // @TODO: Update AUTH_CONFIG and remove .example extension in src/app/auth/auth0-variables.ts.example
  private _auth0 = new auth0.WebAuth({
    clientID: AUTH_CONFIG.CLIENT_ID,
    domain: AUTH_CONFIG.CLIENT_DOMAIN,
    responseType: 'token',
    redirectUri: AUTH_CONFIG.REDIRECT,
    audience: AUTH_CONFIG.AUDIENCE,
    scope: AUTH_CONFIG.SCOPE
  });
  userProfile: UserProfile;

  // Create a stream of logged in status to communicate throughout app
  loggedIn: boolean;
  loggedIn$ = new BehaviorSubject<boolean>(this.loggedIn);

  constructor() {
    // If authenticated, set local profile property and update login status subject
    if (this.authenticated) {
      this.userProfile = JSON.parse(localStorage.getItem('profile'));
      this.setLoggedIn(true);
    }
  }

  private _setLoggedIn(value: boolean) {
    // Update login status subject
    this.loggedIn$.next(value);
    this.loggedIn = value;
  }

  login() {
    // Auth0 authorize request
    this._auth0.authorize();
  }

  handleLoginCallback() {
    // When Auth0 hash parsed, get profile
    this._auth0.parseHash((err, authResult) => {
      if (authResult && authResult.accessToken) {
        window.location.hash = '';
        this.getUserInfo(authResult);
      } else if (err) {
        console.error(`Error: ${err.error}`);
      }
    });
  }

  getUserInfo(authResult) {
    // Use access token to retrieve user's profile and set session
    this._auth0.client.userInfo(authResult.accessToken, (err, profile) => {
      this._setSession(authResult, profile);
    });
  }

  private _setSession(authResult, profile) {
    const expTime = authResult.expiresIn * 1000 + Date.now();
    // Save session data and update login status subject
    localStorage.setItem('access_token', authResult.accessToken);
    localStorage.setItem('profile', JSON.stringify(profile));
    localStorage.setItem('expires_at', JSON.stringify(expTime));
    this.userProfile = profile;
    this._setLoggedIn(true);
  }

  logout() {
    // Remove tokens and profile and update login status subject
    localStorage.removeItem('access_token');
    localStorage.removeItem('profile');
    localStorage.removeItem('expires_at');
    this.userProfile = undefined;
    this._setLoggedIn(false);
  }

  get authenticated(): boolean {
    // Check if current date is greater than expiration
    // and user is currently logged in
    const expiresAt = JSON.parse(localStorage.getItem('expires_at'));
    return (Date.now() < expiresAt) && this.loggedIn;
  }
}
```

You'll see we re-fetch our profile information from localStorage if the user is already authenticated.

```js
// src/app/auth/auth.service.ts

  constructor() {
    // If authenticated, set local profile property and update login status subject
    if (this.authenticated) {
      this.userProfile = JSON.parse(localStorage.getItem('profile'));
      this.setLoggedIn(true);
    }
  }
```

And in our `_setSession()` function we're still using `localStorage` to store our profile and access token. All of a sudden our access to the app and some potentially sensitive and personal data is all in `localStorage`. 

```js
// src/app/auth/auth.service.ts

  private _setSession(authResult, profile) {
    const expTime = authResult.expiresIn * 1000 + Date.now();
    // Save session data and update login status subject
    localStorage.setItem('access_token', authResult.accessToken);
    localStorage.setItem('profile', JSON.stringify(profile));
    localStorage.setItem('expires_at', JSON.stringify(expTime));
    this.userProfile = profile;
    this._setLoggedIn(true);
  }
```

This is the newer version of our AuthService class. We're careful not to store any sensitive information outside of memory.

```js
// src/app/auth/auth.service.ts

import { Injectable } from '@angular/core';
import { BehaviorSubject } from 'rxjs/BehaviorSubject';
import * as auth0 from 'auth0-js';
import { AUTH_CONFIG } from './auth0-variables';
import { UserProfile } from './profile.model';

@Injectable()
export class AuthService {
  // Create Auth0 web auth instance
  // @TODO: Update AUTH_CONFIG and remove .example extension in src/app/auth/auth0-variables.ts.example
  private _auth0 = new auth0.WebAuth({
    clientID: AUTH_CONFIG.CLIENT_ID,
    domain: AUTH_CONFIG.CLIENT_DOMAIN,
    responseType: 'token',
    redirectUri: AUTH_CONFIG.REDIRECT,
    audience: AUTH_CONFIG.AUDIENCE,
    scope: AUTH_CONFIG.SCOPE
  });
  userProfile: UserProfile;
  accessToken: string;

  // Create a stream of logged in status to communicate throughout app
  loggedIn: boolean;
  loggedIn$ = new BehaviorSubject<boolean>(this.loggedIn);

  private _setLoggedIn(value: boolean) {
    // Update login status subject
    this.loggedIn$.next(value);
    this.loggedIn = value;
  }

  login() {
    // Auth0 authorize request
    this._auth0.authorize();
  }

  handleLoginCallback() {
    // When Auth0 hash parsed, get profile
    this._auth0.parseHash((err, authResult) => {
      if (authResult && authResult.accessToken) {
        window.location.hash = '';
        this.getUserInfo(authResult);
      } else if (err) {
        console.error(`Error: ${err.error}`);
      }
    });
  }

  getUserInfo(authResult) {
    // Use access token to retrieve user's profile and set session
    this._auth0.client.userInfo(authResult.accessToken, (err, profile) => {
      this._setSession(authResult, profile);
    });
  }

  private _setSession(authResult, profile) {
    const expTime = authResult.expiresIn * 1000 + Date.now();
    // Save session data and update login status subject
    localStorage.setItem('expires_at', JSON.stringify(expTime));
    this.accessToken = authResult.accessToken;
    this.userProfile = profile;
    this._setLoggedIn(true);
  }

  logout() {
    // Remove token and profile and update login status subject
    localStorage.removeItem('expires_at');
    this.accessToken = undefined;
    this.userProfile = undefined;
    this._setLoggedIn(false);
  }

  get authenticated(): boolean {
    // Check if current date is greater than expiration
    // and user is currently logged in
    const expiresAt = JSON.parse(localStorage.getItem('expires_at'));
    return (Date.now() < expiresAt) && this.loggedIn;
  }
}
```

If we use this service, as it is, in a single page application you'll remained logged in until we re-request the application from the server. So something as simple as a page refresh will log you out.

The main difference is here in our `_setSession` method. We no longer place anything sensitive inside `localStorage`. Because of that there is no need for a constructor to try and retrieve anything out of localStorage.

```js
// src/app/auth/auth.service.ts

  private _setSession(authResult, profile) {
    const expTime = authResult.expiresIn * 1000 + Date.now();
    // Save session data and update login status subject
    localStorage.setItem('expires_at', JSON.stringify(expTime));
    this.accessToken = authResult.accessToken;
    this.userProfile = profile;
    this._setLoggedIn(true);
  }
```

But what if we want to keep users logged in when the return to the site?

First of all we'll need a function that invokes Auth0's `checkSession()` method to check their service to see if our users is already authenticated.

```diff
 // src/app/auth/auth.service.ts
 ...

+  getAccessToken() {
+    this._auth0.checkSession({}, (err, authResult) => {
+      if (authResult && authResult.accessToken) {
+        this.getUserInfo(authResult);
+      } else if (err) {
+        console.log(err);
+        this.logout();
+      }
+    });
+  }

 ...
```

Then we need to call it whenever we load a page. The JavaScript class is loaded on page load and a constructor is ran when it is loaded. So we'll call it from there.

```diff
 // src/app/auth/auth.service.ts
 ...

+  constructor() {
+    this.getAccessToken();
+  }

 ...
```

So at this point you might be wondering how `checkSession()` works, right?

`checkSession()` works by creating an invisible iframe which in turn makes an invisible request to `yourdomain.auth0.com/authorize`. What this request does is return a new `access_token` to your application (and optionally a new `id_token`).

It returns these tokens if the user is still logged in to the authentication server. The authentication server uses normal `web sessions` to know whether you're you and whether you're still logged in. Just like going back to Facebook a week later and it knowing who you are. You'll still be able to get an `access_token` from the authentication server while that session still exists.

That session will expire (or not) according to authorization server configuration. If the session has expired, your application will receive an `interaction_required` error in the response.

If you get that specific error response, it's time to show the login box again!

## Conclusion

Access tokens are to an application what your username and password are to a login box. Access tokens represent a user until it expires (if it expires).

Unnecessary risks with access tokens such as storing them in `localStorage` and storing them incorrectly in a Cookie leaves them vulnerable to XSS and CSRF attacks. While these attacks can be defended against, it's only responsible of us to teach you the most secure way to handle authentication.

That is why we're recommending that access tokens aren't stored in `localStorage` and if they're stored in a Cookie then you should apply `HttpOnly` and `SameSite` directives.

Instead, the [Auth0.js](https://auth0.com/docs/libraries/auth0js/v9) library has a method of retrieving a token from our authentication servers using a web session. You can call this method on page load, so an authenticated user can be logged back in.