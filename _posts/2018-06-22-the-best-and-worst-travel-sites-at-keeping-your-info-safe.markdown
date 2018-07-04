---
layout: post
title: The Best and Worst Travel Sites At Keeping Your Info Safe
description: "Guidelines for customers, along with strategies for managers at travel companies to increase their security measures."
date: 2018-06-22 08:30
category: Growth, Security
is_non-tech: true
author:
  name: Diego Poza
  url: https://twitter.com/diegopoza
  avatar: https://avatars3.githubusercontent.com/u/604869?v=3&s=200
  mail: diego.poza@auth0.com
design:
  bg_color: "#3f3442"
  image: https://cdn.auth0.com/blog/the-best-and-worst-travel-sites-at-keeping-your-info-safe/logo.png
tags:
  - digital-transformation
  - innovation
  - security
  - travel
  - 2fa
  - passwordless
  - dashlane
  - gdpr
related:
  - 2018-03-27-strong-identity-management-system-eases-transition-to-hybrid-cloud
  - 2017-12-29-why-every-business-needs-two-factor-authentication-security
  - 2018-05-21-digital-success-through-customer-identity-and-access-management
---

A recent study by Dashlane unveiled that [89%](https://blog.dashlane.com/travel-password-power-rankings-2018/) of travel sites have unsafe password practices. This includes airlines (e.g., United) as well as booking sites like Trivago and TripAdvisor. Users who recycle passwords among multiple accounts when booking a flight, hotel room or other service on one of these sites are highly vulnerable.

{% include tweet_quote.html quote_text="Dashlane unveils that 89% of travel sites have unsafe password practices." %}

Even if you do take precautions to create passwords you think are long and complicated, many travel companies aren't holding up their end of the bargain (and your data could still be acquired by the wrong hands). How do you know which sites to trust? Which ones should you avoid?

On the flip side, if you're a travel company re-thinking your strategy, we offer concrete approaches to improve your security measures.

## The Best and Worst Performers

Dashlane used five cybersecurity criteria to determine the safest and riskiest travel sites to book with:

1.  Does the site prompt users to enter passwords with more than eight characters?
2.  Does it suggest including both letters and numbers?
3.  Does the site evaluate and send back to the customer the strength of the password they're using?
4.  Does the company offer and facilitate a [two-factor authentication](https://auth0.com/learn/two-factor-authentication/) step?
5.  Does the customer receive a confirmation of sign-up and/or activation email for a new account?

Since 2009, Dashlane has compiled data from [more than five million users](https://techcrunch.com/2016/05/25/password-and-id-startup-dashlane-now-with-5m-users-raises-22-5m-led-by-transunion/). Today it publishes those insights in several forms, including publicly on its [blog](https://blog.dashlane.com/), to help protect not only its own customers but everyone trying to figure out strategies to stay safe in an era of increased cyberthreats and [non-transparent data practices](https://auth0.com/blog/cambridge-analytica-and-facebook/).

### And now - the results:

We'll start with the bad news. The worst performers from the 2018 study were (in order):

1.  Norwegian Cruise Line
2.  Trivago
3.  TripAdvisor
4.  Air Canada
5.  Allegiant Air

Additional companies in the lowest quartile of a sample of 55 travel companies were: Carnival Cruise Line, InterContinental Hotels Group, Choice Hotels, Cruise Critic, Agoda, Skiplagged, Hostelz, and Hotwire.

The best (most secure) travel companies of the year for customers were:

1.  AirBnb
2.  United Airlines
3.  Hawaiian Airlines
4.  Hilton Hotels
5.  Royal Caribbean International

Other top quartile companies were Marriott, Best Western, Booking.com, Budget, Spirit Airlines, Southwest Airlines, Delta Air Lines, Enterprise, and Travelzoo. Only Airbnb received the top score of 5 (a point for fulfilling each criterion), with the next five receiving a score of 4, and the remaining top 25% of companies with scores of 3.

In contrast, all of the worst performers had a score of 0 or 1. In sum, the worst performers were far less secure than the best companies were secure. Much can be done to improve. Full data is [here](https://blog.dashlane.com/travel-password-power-rankings-2018/).

{% include tweet_quote.html quote_text="If you're a travel company or booking site, you can increase your score for next year's rankings using Auth0." %}

## Increase Your Score

If you're a travel company or booking site, you can increase your score for next year's rankings by focusing on two areas:

1.  Password Strength
2.  Two-Factor Authentication (2FA) Implementation.

While there are additional strategies for tightening your security like [hiring a managed services provider](https://auth0.com/blog/what-are-managed-service-providers/), these are steps you can take on your own or via an outsourced provider that specializes in these tasks, with no need to make a system overhaul.

### Tips for increasing password strength

Passwords are inherently risky, and most cybersecurity experts recommend using a password manager like Dashlane, Valt, or 1Password. While there are other suggested strategies such as going [passwordless](https://auth0.com/passwordless), and/or incorporating multifactor authentication like Touch ID or other biometrics like voice or even facial recognition — the following are a few important points you can communicate to your users if they do still rely on passwords.

-   Length v. complexity.
-   Don't "bunch up" special characters. ([Source](https://www.wired.com/2016/05/password-tips-experts/))
-   Stay away from trendy words/phrases (see [Worst Passwords of 2017](https://www.entrepreneur.com/article/306499)).
-   Use passwords as part of an overall plan-of-attack, including a password manager + 2FA.
-   Finally, consider steering clear of passwords that have already been unveiled in a breach. (This data is publicly available [here](https://www.troyhunt.com/introducing-306-million-freely-downloadable-pwned-passwords/).)

### Implementing 2FA

2FA is your best friend for securing consumer data. In the increasingly likely event that a hacker gets your customer passwords correct, they still won't be able to access your system and obtain critical information like financials and even travel itineraries.

What is 2FA? The concept adds an additional step to the authentication process, requiring a one-time token that only the user has access to or a biometric, like a fingerprint, that is unique to the user.

![Auth0's Guardian app is a popular choice for two-factor authentication](https://cdn.auth0.com/blog/the-best-and-worst-travel-sites-at-keeping-your-info-safe/auth0-guardian-is-a-popular-choice-for-2fa.png)

To implement 2FA in your apps, you can rely on popular choices like the [Auth0 Guardian app](https://auth0.com/learn/two-factor-authentication/), Google Authenticator, or Duo Security. By default, 2FA is requested once per month; however, with most tools you can define your own rules to trigger 2FA (e.g., weekly).

To enable 2FA with Auth0's Guardian app, simply navigate to the Multifactor Auth section in the Auth0 management dashboard.

![Easily enable Auth0 Guardian from the Guardian dashboard](https://cdn.auth0.com/blog/the-best-and-worst-travel-sites-at-keeping-your-info-safe/easily-enable-auth0-guardian-from-the-guardian-dashboard.png)

From there, you can choose how users will receive their 2FA codes (e.g., push notifications, SMS, or both). Once you configure which of your Auth0 applications will use 2FA, users will immediately be prompted to set up 2FA before gaining access.

An additional word of advice: in a time of murky data transparency tactics (e.g., [Facebook and Cambridge Analytica](https://auth0.com/blog/cambridge-analytica-and-facebook/)), you might consider customizing your 2FA notification prompt with a sentence describing the importance of 2FA and/or a link for more information to clearly spell out the effort your team is taking to protect its users.

## Don't Wait. Act Now.

With less than a year to go until the next rankings appear, make sure your team has moved up the list! If you're in the top quartile, aim for at least a 4 in the major categories of password length, diversity, strength evaluation, 2FA, and customer notifications. Steer clear of previously breached passwords, and communicate your new strategy to your users for better transparency. Supporting your users in avoiding a breach can save you [millions](https://www.ibm.com/security/data-breach) and help you build a stronger reputation in the years to come.

{% include asides/about-auth0.markdown %}
