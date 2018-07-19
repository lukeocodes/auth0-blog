---
layout: post
title: "Phishing Attacks with Auth0? Facts First"
description: "A security researcher claimed that an attacker could perpetrate a phishing scam that could target a company using the Auth0 platform based on domain. Learn about this attack, the prevalence of phishing in the industry, and how to mitigate these scams."
date: 2018-06-08 9:00
category: Security
is_non-tech: true
banner:
  text: "Auth0 makes it easy to add authentication to your application."
author:
  name: "Joan Pepin"
  url: "https://twitter.com/cloudciso_joan"
  mail: "joan.pepin@auth0.com"
  avatar: "https://cdn.auth0.com/blog/meltdown-spectre/joanpepin.jpg"
design:
  image: "https://cdn.auth0.com/blog/series-c/auth0-logo.png"
  bg_color: "#222228"
tags:
- security
- phishing
- auth0
- attacks
- mitigation
related:
- 2018-03-28-security-vs-convenience
- 2017-11-03-how-to-protect-yourself-from-security-oversights
---

A security researcher working for another company recently published a blog post stating that they could potentially perpetrate a phishing attack targeting users of a website that uses Auth0 authentication. Note that phishing is not the same as hacking a code vulnerability, and therefore, no software patches can be applied because no flaw exists in Auth0's system. Let’s explore the mechanism behind this theoretical phishing attack, the prevalence of social engineering scams in today’s tech industry, and what companies and their users can do to better protect themselves.

## What is Phishing?

[Phishing](https://www.csoonline.com/article/2117843/phishing/what-is-phishing-how-this-cyber-attack-works-and-how-to-prevent-it.html) is a type of social engineering cyber attack that has been around since the 1990s and is still extremely prevalent today. Phishing typically begins with email. Emails are sent to target individuals, and these emails contain links or attachments that have malicious intent: either to install malware on the user’s device, or get them to enter sensitive data into a website that masquerades as legitimate.

Phishing attacks are [growing increasingly sophisticated](https://www.menlosecurity.com/blog/from-your-account-is-deactivated-to-oauth-the-evolution-of-phishing) in the modern landscape of the internet, and the intent is to deceive people into providing protected information, often credentials. Regardless of sophistication, however, the premise of phishing is still very straightforward. 

Consider this sequence of events, for example:

1. A user of Service A (`service-a.com`) is sent an email that appears to be from Service A. The email tells the user they need to log in using a link in the email.
2. The user clicks on the link, which takes them to a login page at `malicious-service-a.com` (_not_ the real website at `service-a.com`).
3. The page has been styled to look like a legitimate page from Service A, so the user enters their credentials into the login form.
4. The credentials have then been given to the attacker, who can now access the real Service A with the victim’s stolen login information. In addition, due to the common practice of [password reuse](https://www.troyhunt.com/password-reuse-credential-stuffing-and-another-1-billion-records-in-have-i-been-pwned/), the attacker may even have access to other accounts that the victim holds on other sites.

This is just one possible example of a phishing scam flow. The attacker could just as easily ask for other kinds of personal information, or request that the user download a malicious email attachment.

## Phishing with Auth0 Subdomains

The specific idea behind the security researcher’s phishing scam was a way to target a website that uses Auth0 authentication. Auth0 supports regional subdomains: `auth0.com`, `eu.auth0.com`, and `au.auth0.com`. A bad actor could potentially attempt to scam users of a website or application that uses one of the subdomains by registering any of the other regional subdomains while using the same name. The attacker could then set up a custom page on their subdomain and, _assuming that they had access to the email addresses of users_, send them a link and attempt to solicit secure information from them. Similar scams could be attempted using _any_ domain that users could mistake for a legitimate one.

<a href="https://www.owasp.org/index.php/Cross-site_Scripting_(XSS)">Cross-Site Scripting, or XSS</a>, was not used in the exploration of a phishing attack that could use Auth0 regional subdomains to trick users. [XSS describes the injection of malicious scripts into a vulnerable web application](https://www.incapsula.com/web-application-security/cross-site-scripting-xss-attacks.html). This exploration relied on using a custom Auth0 page in a subdomain in another region, but [no malicious cross-site code could be injected](https://auth0.com/docs/hosted-pages#why-use-hosted-pages). The ability to include JavaScript in custom pages is a feature available to Auth0 customers to enable necessary flexibility. However, _cross-site code_ cannot be executed from these pages.

## This Attack is Not New and Not Unique

As stated previously, phishing attacks have been around for decades: almost thirty years, in fact. They have become more sophisticated and insidious in their execution, but they rely on tricking people into divulging confidential information: these kinds of attacks are referred to as <a href="https://en.wikipedia.org/wiki/Social_engineering_(security)">social engineering</a>. In these scams, it is common for malicious actors to use domains that look very similar to the target domain to make their phishing attempt more convincing. This is still true now, as well as historically.

> "It's unclear why the researcher used Auth0 as a common example of phishing targeting Auth0. There are no vulnerabilities here and their research doesn't show anything new. It is simply another example of possible phishing. The same could be done with the `anycompany.com` website by using a look alike domain and then setting up a login page same as this one `https://www.anycompamy.com/Login`."
>
> –Cesar Cerrudo, globally recognized security expert and CTO at [IOActive Labs](https://ioactive.com/)

**No company that has users with email addresses is impervious to phishing scams.** There are thousands of ways to perpetrate the [same kind of phishing attempt](https://auth0.com/blog/all-you-need-to-know-about-the-google-docs-phishing-attack/) [on any company](https://www.justice.gov/usao-sdny/pr/lithuanian-man-arrested-theft-over-100-million-fraudulent-email-compromise-scheme), aside from Auth0, [making the attacks quite prevalent](https://www.tripwire.com/state-of-security/security-data-protection/three-quarters-organizations-experienced-phishing-attacks-2017-report-uncovers/) in the tech industry.

## Phishing Relies on Tricking Users

[Phishing is not the same as hacking a code vulnerability](https://blog.varonis.com/whats-difference-hacking-phishing/), and therefore, no software patches can be applied because no flaw exists in Auth0’s system. The particular phishing attack described by the security researcher is not being actively used. Auth0 provides security measures to help prevent credential harvesting via phishing, such as [Single Sign-On](https://auth0.com/docs/sso/current), [Multifactor Authentication](https://auth0.com/learn/get-started-with-mfa/), and [Passwordless](https://auth0.com/passwordless). In addition, [Breached Password Protection](https://auth0.com/breached-passwords), [Brute Force Protection](https://auth0.com/docs/anomaly-detection#brute-force-protection), and [Anomaly Detection](https://auth0.com/docs/anomaly-detection) can help mitigate the potential outcomes of phishing attacks. Auth0 also supports the use of [Custom Domains](https://auth0.com/docs/custom-domains), which removes `auth0.com` (or regional `auth0.com` subdomains) from your application and replaces with a domain of your choosing, which completely eliminates the ability of an attacker to perform this scam using Auth0 subdomains.

However, it is important to remember that **phishing scams are quite common and easy to execute if an attacker already has your users’ email addresses**. Although using an Auth0 Custom Domain or registering all regional Auth0 subdomains eliminates the attack avenue described in this specific case, an attacker could still register any other Top Level Domain name that is similar to yours and attempt to deceive your users. For example, if your company’s login domain is `login.real-company.com`, a phishing attack could be perpetrated from a similar domain, such as `login.rea1-company.com`. In addition, a bad actor could just as easily send a malicious email attachment to your users instead.

## Protecting Your Company and Users

The safety of companies and their users should be the paramount concern when discovering and sharing information regarding security. Not committing to thorough research can negatively impact customers, users, and the general technology community. 

Any company implementing authentication of any kind — proprietary or using an Identity and Access Management platform such as Auth0 — should have security practices in place to mitigate phishing attacks. These practices should include regular internal anti-phishing campaigns and training, as well as Multifactor Authentication or Passwordless. Monitoring for lookalike domain names is also recommended. With the proliferation and increasing cleverness of phishing scams, the importance of awareness and training cannot be understated.