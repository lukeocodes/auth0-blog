---
layout: post
title: "Secure Your Gaming Company With Auth0's Fraud Protection"
description: "Rapid gaming industry growth across mobile, console, and PC platforms can lead to disaster without proper security and anti-fraud protections."
date: 2018-08-10 8:30
category: Security, Identity
design: 
  bg_color: "#4A4A4A"
  image: https://cdn.auth0.com/blog/secure-games/logo.png
author:
  name: Diego Poza
  url: https://twitter.com/diegopoza
  avatar: https://avatars3.githubusercontent.com/u/604869?v=3&s=200
  mail: diego.poza@auth0.com
tags: 
  - gaming
  - security
  - enterprise
  - login
  - anomaly
  - fraud
  - fraud-score
  - minfraud
  - rules
  - authentication
  - socure
  - ciam
related:
  - 2018-02-16-the-6-billion-reason-your-business-needs-advanced-fraud-protection
  - 2017-10-27-where-video-game-authentication-falls-short-and-how-gamers-can-stay-safe
  - 2017-08-30-battlefy-is-major-player-in-esports-arena-with-help-from-auth0
---

In April 2018, Epic Games’ [Fortnite generated $296 million](https://techcrunch.com/2018/05/25/fortnite-had-a-296-million-april/)—more than double what it generated in February. Growth was rapid across mobile, console, and PC platforms.

At the same time as users peaked in February 2018 with 3,400,000 simultaneous logins, [mysterious charges began to appear in users' accounts](https://kotaku.com/whats-really-going-on-with-all-those-hacked-fortnite-ac-1823965781), ranging from $99.99 to $149.99. Hackers made off with one-off charges and often racked up multiple thefts from single users. [One player even struggled to pay his rent](https://www.reddit.com/r/FORTnITE/comments/841zdn/account_hacked_epic_please_help_thats_my_rent/) as a result of the total financial damage. Thieves exposed critical data via multiple log-in attempts, posted sign-in codes, and offered upgrades at steep discounts.

The example isn't isolated. Annually, [cyber thieves make off with over 40 percent](https://www.panopticonlabs.com/our-technology/) of video-game publishers' in-game revenue. As the global [gaming industry's sales top $100 billion](https://seekingalpha.com/article/4184342-video-games-taking-will-esports-become-larger-sports), more criminals are taking notice, and more companies are at risk.

In 2018, it will be critical for gaming companies to know exactly who is logging into their system, when, how, and from where — in order to create a safe and productive environment that users trust and continue to visit.

## What Are Auth0 Rules? How Can They Help?

[Auth0 Rules](https://auth0.com/docs/rules/current) are short JavaScript functions that are executed each time a user authenticates to an application. For example, if you want to know what country a user is logging in from, you can pull this data with the [Add Country Rule](https://auth0.com/rules/add-country) in the Auth0 user dashboard. This can be helpful in locating individual hackers or clusters of them working closely together. In the case of Fortnite, several thefts corresponded to Russia. 

While Auth0 has several Rules, all of which developers can use to customize and [extend Auth0's current suite of capabilities](https://auth0.com/b2c-customer-identity-management) to understand and protect end users, we've highlighted two particular ones below that growing video game companies can implement for better security as they scale.

These can be used separately or chained together for modular coding.

### User Fraud Score

The User Fraud score is particularly helpful in confirming that users are who they say they are and in quickly blocking those who are behaving inappropriately. 

Auth0 has partnered with [Socure](https://www.socure.com/) to develop a user fraud score for each account that authenticates, based on their email address and IP address.

```javascript
function (user, context, callback) {
  // score fraudscore once (if it's already set, skip this)
  user.app_metadata = user.app_metadata || {};
  if (user.app_metadata.socure_fraudscore) return callback(null, user, context);

  var SOCURE_KEY = 'YOUR SOCURE API KEY';

  if(!user.email) {
    // the profile doesn't have email so we can't query their API.
    return callback(null, user, context);
  }

  // socurekey=A678hF8E323172B78E9&email=jdoe@acmeinc.com&ipaddress=1.2.3.4&mobilephone=%2B12015550157
  request({
    url: 'https://service.socure.com/api/1/EmailAuthScore',
    qs: {
      email:  user.email,
      socurekey: SOCURE_KEY,
      ipaddress: context.request.ip
    }
  }, function (err, resp, body) {
    if (err) return callback(null, user, context);
    if (resp.statusCode !== 200) return callback(null, user, context);
    var socure_response = JSON.parse(body);
    if (socure_response.status !== 'Ok') return callback(null, user, context);

    user.app_metadata = user.app_metadata || {};
    user.app_metadata.socure_fraudscore = socure_response.data.fraudscore;
    user.app_metadata.socure_confidence = socure_response.data.confidence;
    // "details":[  
    //     "blacklisted":{  
    //        "industry":"Banking and Finance",
    //        "reporteddate":"2014-07-02",
    //        "reason":"ChargeBack Fraud"
    //     }
    // ] 
    user.app_metadata.socure_details = socure_response.data.details;

    auth0.users.updateAppMetadata(user.user_id, user.app_metadata)
      .then(function(){
        context.idToken['https://example.com/socure_fraudscore'] = user.app_metadata.socure_fraudscore;
        context.idToken['https://example.com/socure_confidence'] = user.app_metadata.socure_confidence;
        context.idToken['https://example.com/socure_details'] = user.app_metadata.socure_details;
        callback(null, user, context);
      })
      .catch(function(err){
        callback(null, user, context);
      });
  });
}
```

Socure incorporates information about individuals from social media, offline identity verification services, and publicly available data, to determine if an account is real or fake. While other vendors generally aggregate static points, such as a user's name, date of birth, and/or social security  number, Socure + Auth0 focus on behavioral cues (e.g., how a user has transacted on other platforms, when, and from where; if they have a stable history; or if there are gaps and inconsistencies). 

 If a user has a high fraud score, you can put them on a watch list or require them to [provide additional information before they can transact](https://www.teampay.co/blog/finance-stack-startups/). 

### minFraud (For Games with Payments)

In addition to rooting out suspect account logins and signups, minFraud helps secure your online marketplace by identifying possible fraud in online transactions. 

The minFraud Rule will send the user’s IP address, email address, and username to MaxMind’s [minFraud API](https://dev.maxmind.com/minfraud/). Similar to the user fraud score, the API will return the risk score — but this time the focus is on the specific transaction (instead of the user as a whole). 

```javascript
function (user, context, callback) {
  var _ = require('underscore');
  var request = require('request');
  var crypto = require('crypto');

  var MINFRAUD_API = 'https://minfraud.maxmind.com/app/ccv2r';

  var data = {
    i: context.request.ip,
    user_agent: context.request.userAgent,
    license_key: 'YOUR_LICENSE_KEY',
    emailMD5: user.email &&
        crypto.createHash('md5').update(user.email).digest("hex") || null,
    usernameMD5: user.username &&
        crypto.createHash('md5').update(user.username).digest("hex") || null
  };

  request.post(MINFRAUD_API, { form: data, timeout: 3000 }, function (err, res, body) {
    if (!err && res.statusCode === 200 && body && body.indexOf(';') >= 0) {
      var result = _.reduce(_.map(body.split(';'), function(val) {
        return { key: val.split('=')[0], value: val.split('=')[1] };
      }), function(result, currentItem) {
        result[currentItem.key] = currentItem.value;
        return result;
      });

      console.log('Fraud response: ' + JSON.stringify(result, null, 2));

      if (result && result.riskScore && (result.riskScore * 100) > 20) {
        return callback(new UnauthorizedError('Fraud prevention!'));
      }
    }

    if (err) {
      console.log('Error while attempting fraud check: ' + err.message);
    }
    if (res.statusCode !== 200) {
      console.log('Unexpected error while attempting fraud check: ' + err.message);
    }

    // If the service is down, the request failed, or the result is OK just continue.
    return callback(null, user, context);
  });
}
```

MinFraud collects information from over [2 billion transactions](https://www.maxmind.com/en/minfraud-services) in its network. The tool assesses signs of fraud, such as

* suspicious pairings of user location with address and IIN data;
* risk associated with the IP address; and
* reputation checks for street addresses, email addresses, phone numbers, and devices.

In cases like _Fortnite_, minFraud could have helped the team block transactions that didn't meet standards for the above criteria, stopping theft from spreading throughout its network.

## Use Rules As Part of a Larger CIAM Strategy

Adding individual Rules that pertain to certain users on your platform is a step in the right direction. Setting up a comprehensive strategy for more deeply understanding all of your users is the next. Either [building or outsourcing](https://auth0.com/b2c-customer-identity-management) customer identity and access management solutions can bring a new dimension to your insights.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/secure-your-gaming-company-with-auth0's-user-fraud-score-and-minfraud/auth0-dashboard.png" alt="Auth0 dashboard">
</p>

[Source](https://auth0.com/docs/getting-started/dashboard-overview)

With a well-crafted tool like a centralized dashboard (above), admins are afforded a bird's-eye view of all current and past activity. They can drill down into the granular details of each user, if need be, including when they joined the platform, their latest logins, and heaviest usage times. Admins can easily troubleshoot issues that arise by logging in as any user — or block access if they note multiple failed logins or excessive transactions. 

As the threat landscape quickly evolves, particularly in fast-moving and highly technical spaces like gaming, coming up with solutions that are cutting edge and safe against the latest attacks can be enormously challenging — particularly for IT teams working 24/7 just to keep a game running. But taking time to consider and create a broad CIAM strategy, centered on end users, will make your game a safe space for competition for years to come.


{% include asides/about-auth0.markdown %}
