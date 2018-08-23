---
layout: post
title: "Four Cybersecurity Attacks Your Employees Need to Know"
description: "Information and tools to help you mitigate risk against cyber attacks and data breaches. Learn more about Malware Infiltration, Phishing, USB Traps, and Mobile Attacks."
date: 2018-08-02 8:30
category: Security, Cybersecurity, Enterprise
design: 
  bg_color: "#222228"
  image: https://cdn.auth0.com/blog/security-whitepaper/logo.png
author:
  name: Diego Poza
  url: https://twitter.com/diegopoza
  mail: diego.poza@auth0.com
  avatar: https://avatars3.githubusercontent.com/u/604869?v=3&s=200
tags: 
  - security
  - cybersecurity
  - attacks
  - enterprise
  - social-engineering
  - malware
  - vulnerabilties
  - wannacry
  - phishing
  - usb-traps
  - mobile
  - mobile-attacks
related:
  - 2018-04-12-common-threats-in-web-app-security
---

In 2017, nearly [_179 million records_](https://www.statista.com/statistics/273550/data-breaches-recorded-in-the-united-states-by-number-of-breaches-and-records-exposed/) were exposed due to data breaches. It's clear that cyber criminals are growing more creative with how they gain access to networks and valuable data.

Many of today’s most common cyberattacks have been socially engineered to rely on human error. Even your best employees can become your greatest weakness if they open a suspicious email, click on a malicious link, or pick up and use an anonymous USB device.

There are ways to protect your organization against sensitive data breaches. Here are four of the most common cyberattack examples and the ways you can avoid them.

## **1. Malware Infiltration**

**What makes you vulnerable: outdated software and lack of backup protocols**

Malware is a term that encompasses many different types of malicious software, including ransomware and spyware. Each type of malware can wreak havoc once it gains access to a system.

WannaCry, for example, a form of ransomware, recently [took advantage of a weak spot in Microsoft’s operating system](https://www.symantec.com/security-center/writeup/2017-051310-3522-99). Banks, health care providers, manufacturers, and other businesses across the globe reported WannaCry's encryption.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/four-cybersecurity-attacks-you-need-to-know/WannaCry-encryption.png" alt="WannaCry encryption">
</p>
  
[[Source](https://blog.malwarebytes.com/cybercrime/2017/05/wanacrypt0r-ransomware-hits-it-big-just-before-the-weekend/)]

In order to regain access to their computers, along with any files that hadn’t been backed up, businesses had to pay a ransom to the creators of the WannaCry program in [_bitcoin_](https://bitcoin.org/en/). [_Sixteen National Health Service’s offices_](https://developers.redhat.com/blog/2017/05/19/wannacry-ransomware-who-it-affected-and-why-it-matters/) and over 200,000 PCs worldwide reported this.

_Solution: Regularly updating your systems will go a long way toward removing dangerous holes. It's a simple fix that many teams, including [Equifax](https://www.wired.com/story/equifax-breach-no-excuse/), have neglected._

_If you want added security, consider [moving to the cloud](https://auth0.com/blog/moving-your-app-to-cloud/). Not only will this allow your enterprise more flexibility to scale, but you can also delegate specialized security tasks like IAM and encryption to expert providers._

## **2. Phishing **

**What makes you vulnerable: lack of education and attention to detail in suspicious e-mails**

In 2016, [91% of cyberattacks](https://phishme.com/2016-enterprise-phishing-susceptibility-report/) were the result of a spear-phishing email. Phishing e-mails steal data either by tricking recipients into giving it to the cyber criminals or by getting users to download a file that installs spyware—software designed to steal information directly from a user’s device.

Children’s Mercy Hospital [_recently reported a breach_](https://healthitsecurity.com/news/childrens-mercy-hospital-phishing-attack-impacts-phi-of-60k) that exposed the health care information of at least 60,000 people. E-mails tricked users into disclosing their work e-mail credentials, which led to the exposure of patient health data when the hackers gained access to the inboxes of the targeted hospital workers. Children’s Mercy [_is now being sued_](https://www.hipaajournal.com/childrens-mercy-hospital-sued-for-63000-record-data-breach/) for this major breach.

Here’s an example of an imitation Dropbox e-mail asking for confirmation of a user’s e-mail:

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/four-cybersecurity-attacks-you-need-to-know/dropbox-email-asking-confirmation-of-user-email.png" alt="Imitation Dropbox email phishing scam asking for confirmation of a user’s e-mail">
</p>

[_Source_](https://www.bankinfosecurity.com/locky-returns-via-spam-dropbox-themed-phishing-attacks-a-10250)

When users click the “Verify your email” link, spyware software is automatically downloaded onto the users' computers.

_Solution: As these e-mails start to look more realistic, it’s important to educate employees on how to spot a questionable e-mail chain._

_Creative training programs include the following:_

- _Digital Defense's [SecurED training program](https://www.digitaldefense.com/professional-services/secured-training/) that works with award-winning Hollywood comedy writers_
- _Terranova's [customizable newsletters](https://terranovacorporation.com/solution/communication-material/)_
- _Inspired eLearning's [Security Awareness for the C-Suite](https://inspiredelearning.com/security-awareness/awareness-c-suite/)_
- _Wombat Security's [everyday tips](https://www.wombatsecurity.com/security-education/free-security-awareness-resources)_

_[See here for a larger list.](https://www.csoonline.com/article/3225471/security/please-dont-send-me-to-cybersecurity-training.html)_

## **3. USB Traps**

**What makes you vulnerable: lack of education and policy on outside device use **

Hackers who want a direct route to company data often use a hardware device, such as a USB drive. A [_recent Google study_](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/45597.pdf) revealed that at least 45% of people who find a USB drive will plug it into their computers. Planted USB drives may contain malware or a link that requests information, similar to a phishing e-mail.

The worst-case scenario is a drive that contains a HID spoof. This is where the USB drive tricks the computer into believing the drive is actually a keyboard and then issues a set of keystrokes that forces the computer to dump data back to the hacker. It can also allow them access to whatever network the computer is connected to.

<p style="text-align: center;">
  <img src="https://cdn.auth0.com/blog/four-cybersecurity-attacks-you-need-to-know/hid-spoof-process.png" alt="HID spoof process">
</p>

_Solution: Outlawing anonymous, external drives on company devices will send a clear message to your organization. Whether a drive was planted or simply purchased from an illegitimate source, removing the temptation to plug something in without properly vetting is a low-hanging fruit that can have enormous power to secure your team and your end users._

## **4. Mobile Attacks**

**What makes you vulnerable: employees accessing company data and networks via cell phones**

Hackers are well aware of the explosion of smartphone and tablet use and have created attacks specifically designed for mobile software. [_The Pegasus attack_](https://blog.lookout.com/lookout-trident-pegasus-enterprise-discovery) on Apple’s iOS software is a prime example. Pegasus infected iPhones through phishing text messages that asked recipients to click on a link inside the text message.

<p style="text-align: center;">
  <img style="width: 300px;" src="https://cdn.auth0.com/blog/four-cybersecurity-attacks-you-need-to-know/pegasus-mobile-attack-on-ios.png" alt="Example of Pegasus attack on Apple iOS mobile software.">
</p>

[_Source_](https://www.hax4us.com/2017/03/hindi-english-smishing-brother-of.html)

Phishing text messages are composed to look legitimate, just as phishing e-mails are. With Pegasus, clicking the link triggered the installation of spyware capable of monitoring people through their camera and microphone. The software also copied all of their calendar, contact, and e-mail data.

Pegasus was an especially frightening cyberattack because it took advantage of [_“zero day” vulnerabilities_](https://www.huffingtonpost.co.uk/entry/what-is-the-pegasus-iphone-spyware-and-why-was-it-so-dangerous_uk_57c0043fe4b0ba22a4d3f930?guccounter=1), meaning there wasn’t any prior knowledge of the vulnerabilities in Apple’s iOS until Pegasus struck. Once infected, users had their login credentials stolen from WhatsApp, Gmail, and other sensitive communication applications.

_Solution: If a mobile phone or tablet has been breached, you need a Plan B. Make sure employees quickly change their passwords by using a professional password manager, such as [LastPass](https://www.lastpass.com/) or [Valt](https://valt.io/). Many employees secure critical information with easy-to-guess iterations of a single password. Encouraging length and complexity in all passwords going forward will make it difficult for hackers to access multiple areas at once._

## Additional Security for All Systems at the Login Stage

Good password policy and sound employee education are key; however, taking the next step with [secure login procedures](https://auth0.com/b2c-customer-identity-management) will add a new dimension of safety to your system. Specialized identity solutions providers can offer support with [multifactor authentication](https://auth0.com/docs/multifactor-authentication) and [passwordless](https://auth0.com/docs/connections/passwordless) registration.

Tracking your users from the start will help eliminate fraud at its origin, helping provide a secure foundation to allow your team to scale.

{% include asides/about-auth0.markdown %}
