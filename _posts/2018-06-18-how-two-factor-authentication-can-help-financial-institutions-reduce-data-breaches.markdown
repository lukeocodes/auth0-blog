---
layout: post
title: How Two-Factor Authentication (2FA) Can Help Financial Institutions Reduce Data Breaches
description: "Getting customers to opt in is key."
longdescription: "Despite the proven ability of 2FA to reduce the threat of a security breach, getting customers to opt in is key."
date: 2018-06-18 14:50
category: Growth, Security
is_non-tech: true
author:
  name: Martin Gontovnikas
  url: http://twitter.com/mgonto
  mail: gonto@auth0.com
  avatar: https://www.gravatar.com/avatar/df6c864847fba9687d962cb80b482764??s=60
design:
  bg_color: "#003a95"
  image: https://cdn.auth0.com/blog/how-two-factor-authentication-can-help-financial-institutions-reduce-data-breaches/logo.png
tags:
  - 2fa
  - two-factor
  - security
  - growth
  - finance
  - fintech
related:
  - 2018-03-27-strong-identity-management-system-eases-transition-to-hybrid-cloud
  - 2018-03-09-3-reasons-your-data-integration-plan-is-important
  - 2018-05-21-digital-success-through-customer-identity-and-access-management
---

Financial institutions are under intense scrutiny by regulators as well as customers to be sure that their user data is secure. Legal pressures are mounting, and customers are increasingly [vocal and demanding](https://auth0.com/blog/cambridge-analytica-and-facebook/) that companies are more transparent and responsible with their information. Finance, over and above other industries, demands a higher level of protection. Behemoth institutions like [J.P. Morgan](https://dealbook.nytimes.com/2014/12/22/entry-point-of-jpmorgan-data-breach-is-identified/?mtrref=www.google.com&gwh=7D07D481AE355F1F8949A1C514F4B11C&gwt=pay&assetType=nyt_now) and [Equifax](https://finance.yahoo.com/news/equifax-reveals-many-ssns-credit-cards-passports-hacked-182355107.html) continue to be top targets for cybercriminals. 

One concrete step that these institutions, along with smaller firms, can take is to secure their consumer apps. Traditionally, apps with authentication systems require users to provide a single identifier, such as an email address, username, or phone number, along with a correct password or pin to gain access; however, with financial apps, this isn't always enough.

**Two-factor authentication** or ***2FA*** adds an additional step to the process. Like a double security door — if a thief unlocks the first one, he's blocked by another.

## Breaking Down Common 2FA Approaches

In general there are [three factors](https://preview.pcmag.com/feature/358289/two-factor-authentication-who-has-it-and-how-to-set-it-up) in authentication:

1. Something you know (such as a password you created)
2. Something you have (such as a cell phone or other piece of hardware)
3. Something you are (such as your fingerprint, gait, or facial features)

Two-factor authentication requires that you have two of these options. In addition to a password that you “know,” 2FA can request you “have” a cell phone or other device handy for a TOTP (a time-based one-time password algorithm) or that you present a biometric, such as a thumbprint. 

### Time-based one-time password algorithm (TOTP)

A time-based one-time password algorithm or TOTP generates one short-lived (~30 seconds or less) password from a secret key in combination with a current timestamp. A [cryptographic hash function](https://www.lifewire.com/cryptographic-hash-function-2625832) connects these two. 

The [Microsoft Authenticator](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/end-user/microsoft-authenticator-app-how-to) is a common option that generates a TOTP:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/how-two-factor-authentication-can-help-financial-institutions-reduce-data-breaches/microsoft-authenticator-generates-a-totp.png" alt="Microsoft Authenticator generates a time-based one-time password (TOTP)">
</p>

Prior to logging into an app, users will go to Microsoft Authenticator, generate their code, and, before it expires, use it to log into the app. Other leaders in the space include the Google Authenticator, along with smaller providers like Twilio, Authy, Duo Mobile, and the LastPass Authenticator. These providers offer 2FA solutions for both mobile and desktop platforms. 

### Biometrics

Biometrics have made big strides in recent years, including the launch of Apple’s iPhone X facial recognition feature. Widely used biometrics for identity verification include iris or retina recognition, facial, fingerprint, hand, and even DNA usage. 

Many consumer financial apps, like TD Bank's app for basic checking, savings, and transfer options, employ the thumbprint as a second authenticator: 

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/how-two-factor-authentication-can-help-financial-institutions-reduce-data-breaches/td-bank-employs-thumbprint-for-2fa.jpg" alt="TD Bank employs the thumbprint as a second authenticator.">
  <figcaption>
    <small>
      Source: <a href="https://www.phonearena.com/news/How-to-use-your-fingerprint-scanner-to-log-in-websites-on-a-Samsung-smartphone_id73835">phoneArena news</a>
    </small>
  </figcaption>
</p>

Users are prompted to place their thumb on the home button for identification prior to entering the app. Several established firms, including [Vanguard](https://personal.vanguard.com/us/insights/article/mobile-touch-id-062016?lang=en) and other asset managers, have incorporated Touch ID as well as [voice verification](https://investor.vanguard.com/account-conveniences/voice-verification) for certain transactions.

{% include tweet_quote.html quote_text="Despite the proven ability of 2FA to reduce the threat of a security breach, the trick is getting users to opt in." %}

## The Challenge: Get Users to Opt In

Despite the proven ability of 2FA to reduce the threat of a security breach along with the ease of setup (no advanced security or developer skills are [required](https://auth0.com/learn/two-factor-authentication/)), 2FA has yet to become mainstream. It can be unclear for many users how to set up 2FA. For example, with services like [PayPal](https://www.paypal.com/us/selfhelp/article/how-do-i-enable-2fa-(two-factor-authentication)-for-my-paypal-powered-by-braintree-user-faq3500), users have to log into their control panel, navigate to their account, and enable 2FA with a QR code or another supported app:

![PayPal supports 2FA with mobile or security key cards](https://cdn.auth0.com/blog/how-two-factor-authentication-can-help-financial-institutions-reduce-data-breaches/paypal-supports-2fa-with-mobile-or-security-keys.png)

It can be difficult for a user without 2FA knowledge to know which option to choose — after already puzzling out how to get to this stage without clear directions in the first place. While some may opt for SMS (first option), the second option (TOTP) doesn't rely on incoming text messages and has proven to be more secure. 

For financial firms looking to gain customer trust, making the 2FA setup simple (or figuring out a way to integrate it into their app's UX in a seamless way) will go a long way toward distinguishing themselves from the pack. Customers are particularly looking for a high level of security with their financial information. If you can clearly communicate your enhanced strategy, you will gain a competitive advantage.

{% include tweet_quote.html quote_text="Customer happiness and high engagement are the biggest determinants of its success." %}

### Discourage Password Use

One way to help users understand the importance of 2FA is to discourage the use of passwords alone. Passwords are historically vulnerable due to human error (we're often lazy about re-creating lengthy, secure, and unique passwords and instead recycle the same or iterations of the same password for the majority of accounts) and the ease with which hackers can access this first level of authentication. Explaining that breaches of personal information happen daily — due to phishing and additional criminal tactics that get the user to unknowingly reveal their password — can prompt your customers to consider an alternative. While a [password manager](https://www.pcmag.com/article2/0,2817,2407168,00.asp) is one option, 2FA brings another dimension to the security game altogether. If one or multiple passwords are compromised, a hacker still cannot access the account without acquiring the second factor.

### Balance UX and Practicality

In many cases, customer happiness with a new feature and a [high level of engagement](https://www.youtube.com/watch?v=30f_lVQBzYs) with it are the most important determinants of its success. With cybersecurity features, however, where do you draw the line between ease and enjoyment of use and practicality? Should something so critical be fun? Do users want to see the same types of design for 2FA integrations that they see with, say, e-commerce apps? You don't necessarily have to sacrifice a pleasant user experience due to the serious nature of a 2FA feature; at the same time, you should be conscious that 2FA is distinct in its functionality. Consider a design that is utilitarian, clear, and makes the user understand the power behind the tool. 

The Google Authenticator, for example, balances clarity, capability, and a splash of color:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/how-two-factor-authentication-can-help-financial-institutions-reduce-data-breaches/google-authenticator-balances-clarity-capability-design.jpg" alt="Google Authenticator balances clarity, capability, and design">
  <figcaption>
    <small>
      Source: <a href="https://www.geeky-gadgets.com/google-authenticator-ios-app-update-removes-user-accounts-04-09-2013/">GeekyGadgets blog</a>
    </small>
  </figcaption>
</p>

Whichever authenticator tool you offer your users it should reflect your brand and focus. Further reading on the topic that we've found helpful includes:

1. [UX vs. Cybersecurity](https://medium.com/@4Barel/ux-vs-cybersecurity-3eedf77ed6e7)
2. [Cyber Security Requires an Important Ingredient: Strong UX](https://hackernoon.com/cyber-security-requires-an-important-ingredient-strong-ux-d0727a0c076)
3. [What is two-factor authentication, and which 2FA apps are best?](https://www.pcworld.com/article/3225913/security/what-is-two-factor-authentication-and-which-2fa-apps-are-best.html)

## Conclusion: Be There for Your Customers 

Figuring out how to integrate 2FA into your consumer financial apps in ways that will make your customers opt-in and enjoy the process will give them peace of mind, help ensure your firm is [compliant](https://auth0.com/gdpr), and even allow you a competitive advantage in the race to gain user support and trust.

{% include asides/about-auth0.markdown %}
