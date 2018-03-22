---
layout: post
title: "10 Things You Should Know about Tokens"
description: "Explore ten important bits of information on tokens, their storage and their vulnerabilities."
longdescription: "Learn how to keep your tokens and you users safe. We explore ten important bits of information on tokens, their storage and common vulnerabilities."
category: Technical Guide, Identity, Cookies vs Tokens
date: 2014-01-27 12:30
updated: 2018-03-22 09:00
author:
  name: Matias Woloski
  mail: matias@auth0.com
  url: http://twitter.com/woloski
  avatar: https://secure.gravatar.com/avatar/0cd73f2f2f39709bd03646e9225cc3d3?s=60
design:
  image: https://cldup.com/_DrZQjW90p.png
tags:
- featured
- jwt
- json-web-tokens
- security
related:
- 2014-02-26-openid-connect-final-spec-10
- 2015-12-17-json-web-token-signing-algorithms-overview
- 2018-02-07-oauth2-the-complete-guide
---

<div class="alert alert-info alert-icon">
  <i class="icon-budicon-500"></i>
  This article has recently been updated <strong>due to relevant feedback on the security of token storage</strong>. We strongly recommend revising your position on token storage as described in the following article.
</div>

**TL;DR:** Tokens are a means of representing claims between parties. This means that, as far as a server is concerned, your token is you. If someone gets it, they can pretend to be you right up until that token expires. Treat that token with all the respect and security you would of your own personal information, because with it that is what is truly a risk.

<ol>
  <li><a href="#token-storage">Tokens can be stored, but they probably shouldn't be</a></li>
  <li><a href="#token-vulnerability">Even with CSP and SRI, local/session storage is still vulnerable</li>
  <li><a href="#token-expiration">Tokens can expire like cookies, but you have more control</li>
  <li><a href="#preflight">Preflight requests will be sent on each CORS request</li>
  <li><a href="#file-downloads">When you need to stream something, use the token to get a signed request</li>
  <li><a href="#xss-xsrf">It's easier to deal with XSS than XSRF</li>
  <li><a href="#token-size">The token gets sent on every request, watch out for its size</li>
  <li><a href="#confidential-info">If you store confidential info, encrypt the token</li>
  <li><a href="#token-oauth">JSON Web Tokens can be used in OAuth</li>
  <li><a href="#fine-grained-authorization">Tokens are not silver bullets, think about your authorization use cases carefully</li>
</ol>

<a name="token-storage"></a>
##1. Tokens can be stored, but they probably shouldn't be

There is ***no secure way*** for a single page application to both authenticate a user ***and store the token***. 

Sessions are a way to identify a user transitioning between pages on your site. Personal data can be associated to a user securely on a backend application by storing it against a session. Unfortunately, on a frontend only single page application (SPA), it is much harder to do that. We have no private server-side to hide that personal data and then associate it back to our user.

"But what about local storage", I hear you shout! Local storage is an entirely unencrypted, unsecurable key value store. The risk really comes from XSS (Cross-site scripting) attacks. What is going to happen if your friendly neighbourhood attacker manages to run say, the following JavaScript on your site;

```js
Object.keys(localStorage).forEach(function(key){
   console.log(`${key}: ${localStorage.getItem(key)}`);
});
```

Well, find a good website and give it a go! Drop that JavaScript in your console and run it. You'll see it loop through and output the key and value of each item in local storage.

<a name="token-vulnerability"></a>
##2. Even with CSP and SRI, local/session storage is still vulnerable

"But what about my security settings, like Content Security Policy (CSP) and Subresource Integrity (SRI)", I also hear you shout. Well sure, but what if your trusted vendors are vulnerable. Can you really trust that 3rd party analytics script to never be vulnerable? Do they really think about ***your security*** as much as you do? It would only need an attacker to insert a bit of JavaScript that sends a simple XHR request to an endpoint they've setup. ***You would never even know.***

***It's not all doom and gloom for SPAs though.*** I'd guess most websites these days will have a backend of some type or another [and if they don't there are very simple ways to create a quick one](https://webtask.io/), and that means you can use your backend to write your session identifier with cryptographically signed Cookies, with `HttpOnly` and `SameSite=strict` directives enabled. Every time a user makes a request to your server, we can use their session to retrieve their securely stored personal data from our storage, be it a cache or database. 

<a name="token-expiration"></a>
##3. Tokens can expire like cookies, but you have more control

Tokens have an expiration (in [JSON Web Tokens](http://tools.ietf.org/html/draft-ietf-oauth-json-web-token-15#section-4.1.4) is represented by `exp` property), otherwise someone could authenticate forever to the API once they logged in at least once. Cookies also have expiration for the same reasons.

In the world of cookies, there are different options to control the lifetime of the cookie:

  1. Cookies can be destroyed after the browser is closed (session cookies).
  2. In addition to this you can implement a server side check (typically done for you by the web framework in use), and you could implement expiration or sliding window  expiration.
  3. Cookies can be persistent (not destroyed after the browser is closed) with an expiration.

In the tokens world, once the token expires, you simply want to get a new one. You could implement an endpoint to refresh a token that will:

1. Validate the old token
2. Check if the user still exists or access hasn't been revoked or whatever makes sense for your application
3. Issue a new token with a renewed expiration

You can even store in the token the original issue date, and enforce a re-login after two weeks or so.

```js
app.post('/refresh_token', function (req, res) {
  // verify the existing token
  var profile = jwt.verify(req.body.token, secret);

  // if more than 14 days old, force login
  if (profile.original_iat - new Date() > 14) { // iat == issued at
    return res.send(401); // re-logging
  }

  // check if the user still exists or if authorization hasn't been revoked
  if (!valid) return res.send(401); // re-logging

  // issue a new token
  var refreshed_token = jwt.sign(profile, secret, { expiresInMinutes: 60*5 });
  res.json({ token: refreshed_token });
});
```

If you need revocation of tokens (useful if tokens are long-lived) you will need to have some sort of registry of issued tokens to check against.

<a name="preflight"></a>
##4. Preflight requests will be sent on each CORS request

Someone pointed out that the Authorization header is not a [simple header](http://www.w3.org/TR/cors/), hence a pre-flight request would be required for all the requests to a particular URLs.

```
OPTIONS https://api.foo.com/bar
GET https://api.foo.com/bar
   Authorization: Bearer ....

OPTIONS https://api.foo.com/bar2
GET https://api.foo.com/bar2
   Authorization: Bearer ....

GET https://api.foo.com/bar
   Authorization: Bearer ....
```

But this happens if you are sending `Content-Type: application/json` for instance. So this is already happening for most applications.

One small caveat, the `OPTIONS` request won't have the Authorization header itself, so your web framework should support treating `OPTIONS` and the subsequent requests differently (__Note:__ Microsoft IIS for some reason seems to have issues with this).

<a name="file-downloads"></a>
##5. When you need to stream something, use the token to get a signed request

When using cookies, you can trigger a file download and stream the contents easily. However, in the tokens world, where the request is done via XHR, you can't rely on that. The way you solve this is by generating a signed request like AWS does, for example. [Hawk Bewits](https://github.com/hueniverse/hawk) is a nice framework to enable this:

####__Request__:

```
POST /download-file/123
Authorization: Bearer...
```

####__Response__:

```
ticket=lahdoiasdhoiwdowijaksjdoaisdjoasidja
```

This ticket is stateless and it is built based on the URL: host + path + query + headers + timestamp + HMAC, and has an expiration. So it can be used in the next, say 5 minutes, to download the file.

You would then redirect to `/download-file/123?ticket=lahdoiasdhoiwdowijaksjdoaisdjoasidja`. The server will check that the ticket is valid and continue with business as usual.

<a name="xss-xsrf"></a>
##6. It's easier to deal with XSS than XSRF

Cookies have this feature that allows setting an `HttpOnly` flag from server side so they can only be accessed on the server and not from JavaScript. This is useful because it protects the content of that cookie to be accessed by injected client-side code (XSS).

If tokens are ever stored in local storage they are open to an XSS attack getting the attacker access to the token. For that reason you should never store your tokens client side. Keeping your token expiry low (below two hours) is a belt and braces way to limit the damage of a compromised token.

But if you think about the attack surface on cookies, one of the main ones is XSRF. The reality is that XSRF is one of the most misunderstood attacks, and the average developer, might not even understand the risk, so lots of applications lack anti-XSRF mechanism. However, everybody understands what injection is. Put simply, if you allow input on your website and then render that without escaping it, you are open to XSS. So based on our experience, it is easier to protect against XSS than protecting against XSRF. Adding to that, anti-XSRF is not built-in on every web framework. XSS on the other hand is easy to prevent by using the escape syntax available by default on most template engines.

<a name="token-size"></a>
##7. The token gets sent on every request, watch out for its size

Every time you make an API request you have to send the token in the `Authorization` header.

```
GET /foo
Authorization: Bearer ...2kb token...
```

vs.

```
GET /foo
connect.sid: ...20 bytes cookie...
```

Depending on how much information you store in that token, it could get big. On the other hand, session cookies usually are just an identifier (connect.sid, PHPSESSID, etc.) and the rest of the content lives on the server (in memory if you just have one server or a database if you run on a server farm).

Now, nothing prevents you from implementing a similar mechanism with tokens. The token would have the basic information needed and on the server side you would __enrich__ it with more data on every API call. This is exactly the same thing cookies will do, with the difference that you have the additional benefit that this is now a conscious decision, you have full control, and is part of your code.

```
GET /foo
Authorization: Bearer ……500 bytes token….
```

Then on the server:

```js
app.use('/api',
  // validate token first
  expressJwt({secret: secret}),

  // enrich req.user with more data from db
  function(req, res, next) {
    req.user.extra_data = get_from_db();
    next();
  });
```

It is worth mentioning that you could also have the session stored completely on the cookie (instead of being just an identifier). Some web platforms support that, others not. For instance, in node.js you can use [mozilla/node-client-sessions](https://github.com/mozilla/node-client-sessions).

<a name="confidential-info"></a>
##8. If you store confidential info, encrypt the token

The signature on the token prevents tampering with it. TLS/SSL prevents man in the middle attacks. But if the payload contains sensitive information about the user (e.g. SSN, whatever), you can also encrypt them. The [JWT spec](http://tools.ietf.org/html/draft-ietf-oauth-json-web-token) points to the [JWE spec](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption) but most of the libraries don't implement JWE yet, so the simplest thing is to just encrypt with AES-CBC as shown below.

```js
app.post('/authenticate', function (req, res) {
  // validate user

  // encrypt profile
  var encrypted = { token: encryptAesSha256('shhhh', JSON.stringify(profile)) };

  // sing the token
  var token = jwt.sign(encrypted, secret, { expiresInMinutes: 60*5 });

  res.json({ token: token });
};

function encryptAesSha256 (password, textToEncrypt) {
  var cipher = crypto.createCipher('aes-256-cbc', password);
  var crypted = cipher.update(textToEncrypt, 'utf8', 'hex');
  crypted += cipher.final('hex');
  return crypted;
}
```

Of course you can use the approach on #7 and keep confidential info in a database.

**UPDATE**: [Pedro Felix](https://twitter.com/pmhsfelix) correctly pointed out that MAC-then-encrypt is vulnerable to [Vaudenay-style attacks](http://www.thoughtcrime.org/blog/the-cryptographic-doom-principle/). I updated the code to do encrypt-then-MAC.

<a name="token-oauth"></a>
##9. JSON Web Tokens can be used in OAuth: Bearer Token

Tokens are usually associated with OAuth. [OAuth 2](https://auth0.com/blog/oauth2-the-complete-guide/) is an authorization protocol that solves identity delegation. The user is prompted for consent to access his/her data and the authorization server gives back an `access_token` that can be used to call the APIs acting as that user.

Typically these tokens are opaque. They are called `bearer` tokens and are random strings that will be stored in some kind of hash-table storage on the server (db, cache, etc.) together with an expiration, the scope requested (e.g. access to friend list) and the user who gave consent. Later, when the API is called, this token is sent and the server lookup on the hash-table, rehydrating the context to make the authorization decision (did it expire? does this token have the right scope associated for the API that wants to be accessed?).

The main difference between these tokens and the ones we've been discussing is that signed tokens (e.g.: JWT) are "stateless". They don't need to be stored on a hash-table, hence it's a more lightweight approach. OAuth2 does not dictate the format of the `access_token` so you could return a JWT from the authorization server containing the scope/permissions and the expiration.

<a name="fine-grained-authorization"></a>
##10. Tokens are not silver bullets, think about your authorization use cases carefully

Couple of years ago we helped a big company implement a token-based architecture. This was a 100.000+ employees company with tons of information to protect. They wanted to have a centralized organization-wide store for "authentication & authorization". Think about "user X can read field id and name of clincial trial Y for hospital Z on country W" use cases. This fine-grained authorization, as you can imagine, can get unmanageable pretty quickly, not only technically but also administratively.

* Tokens can get really big
* Your apps/API gets more complicated
* Whoever grants these permissions will have a hard time managing all this.

We ended up working more on the information architecture side of things to make sure the right scopes and permissions were created. Conclusion: resist the temptation of putting everything into tokens and do some analysis and sizing before going all the way with this approach.

----

**Disclaimer**: when dealing with security, make sure you do the proper due diligence. Any code/recommendation that you get here is provided as-is.

Please leave a comment or [discuss on HN](https://news.ycombinator.com/item?id=7137498).

Happy tokenizing!

_Photo taken from: http://alfanatic.webs.com/_
