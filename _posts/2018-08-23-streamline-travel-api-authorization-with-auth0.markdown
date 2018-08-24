---
layout: post
title: "Streamline Travel API Authorization With Auth0"
metatitle: "Streamline Travel API Authorization With Auth0"
description: "Travel is one of the world’s fastest-growing sectors and demand for the travel sector has reached an all-time high. Supercharge API Authorization With Auth0 and streamline API development"
metadescription: "Travel is one of the world’s fastest-growing sectors and demand for the travel sector has reached an all-time high. Supercharge API Authorization With Auth0 and streamline API development"
date: 2018-08-23 11:26
category: Growth, Industries, Travel
is_non-tech: true
author:
  name: Luke Oliff
  url: https://twitter.com/mroliff
  avatar: https://avatars1.githubusercontent.com/u/956290?s=200
  mail: luke.oliff@auth0.com
design:
  bg_color: "#3f3442"
  image: https://cdn.auth0.com/blog/the-best-and-worst-travel-sites-at-keeping-your-info-safe/logo.png
tags: 
  - identity
  - travel
  - digital-transformation
  - idaas
  - security
  - auth
  - authentication
  - authorization
  - m2m
  - machine
  - machine-to-machine
  - machine-2-machine
  - api
related:
  - 2018-06-22-the-best-and-worst-travel-sites-at-keeping-your-info-safe
  - 2018-07-23-3-tools-for-digital-transformation-in-airline-industry
  - 2018-07-11-using-m2m-authorization
---

Travel is one of the world’s fastest-growing sectors and demand for the [travel sector has reached an all-time high](https://www2.deloitte.com/content/dam/Deloitte/us/Documents/consumer-business/us-cb-2018-travel-hospitality-industry-outlook.pdf). Directly or indirectly, economic contributions in [travel and tourism now account for a staggering 10.4 percent of global GDP](https://www.wttc.org/-/media/files/reports/economic-impact-research/regions-2018/world2018.pdf). Travel investment is set to reach $925bn in 2018, and rise by an average of 4.3% over the next ten years to [$1,408.3bn by 2028](https://www.wttc.org/-/media/files/reports/economic-impact-research/regions-2018/world2018.pdf).

In this fiercely contested market, digital transformation is key to staying agile. Application programming interfaces (APIs) play a large role in digital transformation and are key tools that allow travel businesses to provide unique value to customers and, ultimately, compete better. From hotels and flights to cars and buses, there is an API for every area of travel.

> _"Digitisation represents an exciting opportunity for the aviation, travel and tourism ecosystem, with the potential to unlock $1 trillion of value to the industry and wider society over the next decade (2016 – 2025)"_ —World Economic Forum

Even mega-giants like [Amadeus](https://amadeus.com), [SABRE](https://www.sabretravelnetwork.com) and [Travelport](https://www.travelport.com), who are the dominant players in the area of one-stop shop travel solutions, have their own APIs for comprehensive booking and reservations services. But they didn’t start with APIs - they’ve been dominating this sector for much longer than APIs have been popular.

{% include tweet_quote.html quote_text="In the fiercely contested travel industry, digital transformation is key to staying agile. Auth0 streamlines API development." %}

## A Brief History of Digital Transformation in Travel

With the boom of general aviation in the 1950s, the travel industry was forced to scale quickly. Digital tools were developed to automate how airlines track and sell their inventory. This was the first digital transformation in Travel, even before the beginning of the internet. From there, SABRE—or Semi Automated Business Research Environment—was born. The first global distribution system (GDS), SABRE was started in the 1960s with a collaborative effort by American Airlines and IBM. Travelport was developed by United Airlines in the 1970s and Amadeus soon followed, developed by a group of European airlines in the 1980s.

These three major players still make up more than [90% of the global market in airline bookings](http://www.businesstravel-iq.com/article/2018/08/08/gds-market-share-second-quarter-2018) – and their dominance is down to digital transformation. This gave them the ability to streamline business processes and reach into more areas of the travel industry with relatively little friction.

![Dominance in travel is driven by the web of apis](https://cdn.auth0.com/blog/streamline-travel-api-authorization-with-auth0/the-web-of-apis-behind-travels-apps.png)

## APIs Have a Big Impact in Travel

[Amadeus](https://amadeus.com), [SABRE](https://www.sabretravelnetwork.com) and [Travelport](https://www.travelport.com) didn’t stop at simply automating processes to manage inventory. Suppliers like hotel companies and airlines need to connect with distributors such as wholesalers and GDSs to reach as large an audience as possible for their products. In this scenario, without an API, the suppliers must upload their inventory to the GDS, for example, and there would be a cost associated with the distribution. GDSs and similar companies that help connect partners and products to consumers, have a cost to use them. This has an impact on ticket prices.

However, if a supplier has an API it is easier for them to connect supply and demand directly - and this equals less cost that is then passed on to the consumer. By building their own APIs, suppliers like the fictional airline.com are almost becoming direct-to-consumer distributors of their own products, offering their own sites or apps to book on, and possibly marketing through a metasearch engine. 

Additionally, with APIs, Airline.com can also sell ancillaries such as rental cars, or even hotel rooms through the APIs offered by those ancillary services. 

This means that suppliers have built APIs that help them sell more direct, market through metasearch, and integrate with a rental car or a hotel room providers APIs as well to bolster their package or complementary travel offering.

> _"The whole industry is underpinned by a complex layer of networks and APIs - the plumbing of the B2B travel industry – e.g. communication links and API connectors between merchandising platforms, central reservation systems, global distribution systems, industry transaction hubs and gateways, etc."_ —Sonja Woodman, Triometric: APIs – The Plumbing of the B2B Travel Industry

## Metasearch Simplifies the Consumer Search Process

Metasearch engines like [Skyscanner](https://www.skyscanner.net/) and [Trivago](https://www.trivago.co.uk/) take data from airlines, hotel chains, travel agents, global distribution systems, and aggregate the results. By aggregating this data, they can provide a website and app that greatly improves customers’ experience in finding the cheapest rates for flights (now including hotels and vehicles) and then linking the customer directly to the retailer to make their booking.

Metasearch engines ultimately aggregate prices and shows the best options. Potentially, a simple search for flights could run through hundreds of sources of data all linked in a web of APIs, pulling data from different sources.

![Metasearch engines connect to everything](https://cdn.auth0.com/blog/streamline-travel-api-authorization-with-auth0/meta-search-connects-to-everything.png)

## TripAdvisor Encourages Partners to Develop APIs

Online travel agents (OTAs) like [TripAdvisor](https://www.tripadvisor.com), [Hotels.com](https://www.hotels.com) and [Booking.com](https://www.booking.com/) offer hotel rooms, flight tickets, holiday packages, train tickets and other tourism products directly to consumers, by charging a commission. 

The power of traditional OTAs like these usually comes from partners prepared to regularly upload their inventory. The overhead of conforming with APIs, developing software to mass upload inventory, is on the partner. The effort of integrating with an API, typically, does not go the other way around. In other words, TripAdvisor would not spend time integrating with a retailer or hotel chain’s APIs unless they were pretty big— think Hilton, Westin or FourSeasons.

TripAdvisor’s new service, the TripAdvisor Self Implemented Partner program, is ready to integrate with any partner APIs, so long as those APIs had been built to TripAdvisors specifications.

This model that TripAdvisor has developed, has benefits across the board. TripAdvisor and suppliers get the benefit of valuable live pricing, by allowing TripAdvisor to pull prices on demand. This is instead of having to rely on daily uploads of inventory. With traditional inventory uploads, there is a risk that rooms can become unavailable between the upload and the customer trying to make a booking. In this new API driven model, consumers gain access to great rates that don’t disappear once they try to book them.

![Supercharge API Development with Auth0](https://cdn.auth0.com/blog/streamline-travel-api-authorization-with-auth0/supercharge-api-development-with-auth0.png)

{% include tweet_quote.html quote_text="New APIs have the inevitable overhead of developing security, and dedicating resources to it has a cost. Supercharge API security development with Auth0." %}

## Supercharge API Development with Auth0

Creating new APIs has the inevitable overhead of developing security. At a basic level, secure and authorized API integration is required for partners to communicate securely, to identify partner functionality and features, and to apply pricing and billing mechanisms. Dedicating engineering resources to these areas, such as authentication, identity, security, and compliance comes at a high opportunity cost but it can also represent a larger risk than relying on experts for the same.

> _"Why then, in 2018, do organizations think that they should be reinventing the wheel on identity and access management each time? By doing so they’re taking on an undue security risk and burden. The developer who may be an expert in a particular domain is expected to also become an expert in the most up to date security practices. That is an unreasonable expectation for companies to have."_ —Dr. Manu Kumar, Founder, K9 Ventures: Identity and Access Management: Leave it to the Experts

Apart from expertise in identity which ensures security, Auth0 provides the necessary building blocks to secure your APIs much more quickly. With products like [Machine-to-Machine Authorization](https://auth0.com/blog/using-m2m-authorization/), we allow companies to focus on the core value-add capabilities of their product versus focusing time and effort on implementing capabilities that Auth0 provides out of the box. Devote more resources toward differentiation through the core value of your product versus dedicating them to identity.

Think of the tens, hundreds, or even thousands of API calls that could be triggered behind the scenes by a search for flights. Each one of those calls is made across the internet, secured each time by a layer of security and access management. Building this type of access management is time consuming, and can difficult to secure.

Auth0 can help keep APIs secure, more secure than trying to develop your own API authorization and access management. [APIs are broadly responsible for digital transformation right now](https://deloitte.wsj.com/cio/2016/06/27/apis-help-drive-digital-transformation/). Auth0 provides Machine-to-Machine Authorization that helps keep APIs secure, while being easy to use and implement. Don’t reinvent the wheel and never compromise on security.

{% include asides/about-auth0.markdown %}

