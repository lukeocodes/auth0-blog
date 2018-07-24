---
layout: post
title: "How Auth0 Makes Your Apps More Secure"
description: "Key solutions like anomaly detection, universal login, and custom security rules delivered by Auth0 helps its customers protect sensitive user data."
date: 2018-07-19 8:30
category: Auth0-based Tutorial, Tools
design: 
  bg_color: "#666363"
  image: https://cdn.auth0.com/blog/web-app-security/logo.png
author:
  name: Diego Poza
  url: https://twitter.com/diegopoza
  avatar: https://avatars3.githubusercontent.com/u/604869?v=3&s=200
  mail: diego.poza@auth0.com
tags: 
  - security
  - identity
  - infrastructure
  - anomaly
  - universal-login
  - breach
  - verification
  - authentication
related:
  - 2018-04-12-common-threats-in-web-app-security
  - 2018-02-14-what-is-data-security
  - 2018-04-27-strong-identity-management-system-eases-transition-to-hybrid-cloud
---

Tight infrastructure security is critical — but if you don't remember to secure your web applications, customer data remains exposed.

There are several [common threats to web-app security](https://auth0.com/blog/common-threats-in-web-app-security/):

- A lack of encryption between your web browser and their servers
- XSS (cross-site scripting) that occurs when malicious scripts are injected into web applications
- Clickjacking (when an attacker injects transparent or opaque objects into a web application)
- Cross-site request forgery (where the attacker impersonates an authorized user and requests sensitive information, such as passwords)

These threats are continuing to multiply and become more complex by the day. To mitigate breaches, particularly in a time of [heavy regulatory scrutiny](https://auth0.com/docs/compliance/gdpr), Auth0 offers three major strategies:

1.  Anomaly Detection
2.  Universal Login Support
3.  Customizable Rules

Auth0 allows you to combine these features to further amplify protection for your app's end users. While you can always attempt to develop these and similar solutions in-house, outsourcing app security will give you access to a team of experts who work 24/7 to keep up with the latest trends in a constantly evolving space.

## Anomaly Detection

Auth0 provides three built-in shields that [detect anomalies](https://auth0.com/docs/anomaly-detection) among users in an app's system. Used separately or in combination, the shields have the ability to stop malicious access attempts before they occur. Administrators receive alerts of suspicious activity and can then block future login attempts by an aberrant user.

{% include tweet_quote.html quote_text="Learn more about how Auth0 provides three built-in shields that detect anomalies among users in an app's system." %}

### Brute-Force Protection

Within its [Brute-Force Protection](https://auth0.com/docs/anomaly-detection) feature, Auth0 has two distinct shields:

1.  Shield 1 is triggered after 10 failed login attempts on a single account from the same IP address.
2.  Shield 2 is triggered after 100 failed login attempts from a single IP address via different usernames in 24 hours or after 50 sign-up attempts in one minute from the same IP address.

In both scenarios, the shield automatically sends an email to the affected user, which an administrator can edit however he or she wishes. This initial email looks like:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/how-auth0-makes-your-apps-more-secure/affected-user-email.png" alt="Affected user email">
</p>

An administrator can easily edit the template by accessing the Auth0 [Dashboard](https://auth0.com/docs/dashboard) and following Emails > Templates.

Brute-Force Protection will also block the suspicious IP address. When the issue is resolved, an administrator simply locates the user in the Auth0 [Dashboard](https://auth0.com/docs/dashboard) and files an action to unblock. A user can also click on the "unblock" link provided in the email above or change their password.

### Breached-Password Detection

The third shield is breached-password detection. This is easy to turn on and customize in the Anomaly Detection section of the Auth0 dashboard.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/how-auth0-makes-your-apps-more-secure/breached-password-shield.png" alt="Breached-password anomaly detection with Auth0">
</p>

Breached-password detection is useful in instances where Auth0 suspects that a user's email has been compromised in a major security breach. (Auth0 consistently tracks these on third-party sites.) If this occurs, Auth0 will trigger the shield, notify the user, and block them from logging in until they reset their information. Breached-password detection is becoming increasingly helpful as many individuals use the same or similar iterations of the same password on multiple sites.

{% include tweet_quote.html quote_text="When Auth0 suspects that a user's email has been compromised in a security breach, Auth0 will trigger a protective shield, notify the user, and block them from logging in until they reset their information" %}

## Universal Login

Auth0 supports [universal login](https://auth0.com/blog/authentication-provider-best-practices-centralized-login/) on customer apps. In contrast to embedded login, this offers increased security and a shorter setup time.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/how-auth0-makes-your-apps-more-secure/centralized-embedded-diagram.jpg" alt="Auth0 Universal Login offerings">
</p>

Universal login is a more seamless experience for the user. On the front end he or she sees a simple authentication request; on the back end, credentials remain within the same domain instead of being sent across origins. This makes it a far more secure method than exposing the information to outside sources. When personal data moves from one origin to another, [phishing attacks](https://auth0.com/blog/all-you-need-to-know-about-the-google-docs-phishing-attack/) and [man-in-the-middle attacks](https://auth0.com/docs/security/common-threats#man-in-the-middle-mitm-attacks) become common.

In addition to an enhanced customer experience, universal login is simpler for administrators to maintain and provides the benefits of single sign-on (SSO).

## Auth0 Security Rules

Auth0 has developed customizable [Rules](https://auth0.com/docs/rules/current) for its app-security features. Rules are JavaScript functions that execute when a user authenticates to your application. Rules can be helpful when customizing and extending Auth0's existing capabilities. For example, the [Force Email Verification](https://auth0.com/rules/email-verified) and [User Fraud Score](https://auth0.com/rules/socure_fraudscore) Rules can be incorporated into Auth0's existing tools like Anomaly Detection to better determine if a user truly is who they say they are.

### Force Email Verification

Force Email Verification only allows app access to users who have verified their emails. Incorporating Force Email Verification into an app instead of a browser version is a good UX practice with powerful back end capabilities.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/how-auth0-makes-your-apps-more-secure/verify-email-code.png" alt="Force Email Verification code">
</p>

[[Source](https://auth0.com/rules/email-verified)]

For example, if you are working with a large set of emails that belong to a network, e.g., `@university.edu` or `@company.edu`, Force Email Verification can help parse individuals in a group to make sure that a fake user hasn't set up an account to blend in with the others. Force Email Verification requests that users cross-check their information; anyone who returns information that doesn't match or does not return additional information at all will be blocked from logging in.

### User Fraud Score

Like Force Email Verification, you can tack on the User Fraud Score to a variety of features. If you've built an online marketplace — like Etsy, for example — you can use this rule to cross-check everyone conducting business and thus add an extra layer of protection to purchases. The Rule calculates a fraud score for every user, based on their email address and IP address.

```javascript
function (user, context, callback) {
  // score fraudscore once (if it's already set, skip this)
  user.app_metadata = user.app_metadata || {};
  if (user.app_metadata.socure_fraudscore) return callback(null, user, context);

  const SOCURE_KEY = 'YOUR SOCURE API KEY';

  if(!user.email) {
    // the profile doesn't have email so we can't query their api.
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
    const socure_response = JSON.parse(body);
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

In the Auth0 Dashboard, copy the code block above, and replace placeholders with the appropriate values. The rule will pull the fraud score from socure.com and store it on app_metadata. A full set of Auth0 Rules can be found [here](https://auth0.com/docs/rules/current).

For fraud detection, Auth0 partners with [ThisData](https://auth0.com/blog/anomaly-detection-safer-login-with-thisdata-and-auth0/). ThisData allows real-time detection of any account takeovers across both web and mobile apps.

## No Better Time To Secure Your Apps

Securing your app with the best tools is always a good decision — but it is especially timely, given the slew of new regulations: GDPR deadline has passed, but [it's not too late to comply](https://auth0.com/blog/your-quick-gdpr-compliance-checklist/), and several countries and states in the U.S. have followed the EU with [similar privacy initiatives](https://auth0.com/blog/brace-yourself-the-gdpr-ripple-effect-in-california/). In line with these events, Auth0 has developed additional [strategies](https://auth0.com/gdpr-assessment/) for getting your team into alignment quickly, including [customer identity and access management (CIAM) protocol](https://auth0.com/blog/digital-success-through-customer-identity-and-access-management/).

While it might be easy to forget your apps in the swirl of demands around your core operations — making sure you plug any holes here is key to ensuring that you don't become another [data-breach statistic](https://consequenceofsound.net/2018/06/ticketfly-hacker-data-breach/).
