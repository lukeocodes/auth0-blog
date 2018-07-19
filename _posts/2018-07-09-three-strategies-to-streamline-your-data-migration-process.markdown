---
layout: post
title: "Three Strategies to Streamline Your Data Migration Process"
description: "Tips for a secure and organized data migration process. 1. Workflow and project management tools 2. User identity and access management 3. Clear customer communication"
date: 2018-07-09 12:30
category: Growth, Data
author:
  name: Martin Gontovnikas
  url: http://twitter.com/mgonto
  mail: gonto@auth0.com
  avatar: https://www.gravatar.com/avatar/df6c864847fba9687d962cb80b482764??s=60
design:
  image: https://cdn.auth0.com/blog/data-migration/data-migration-logo.png
  bg_color: "#29335c"
tags:
- data
- migration
- data-migration
- user
- users
- efficiency
- business
related:
- 2018-02-05-managed-service-providers-myths
- 2018-03-09-3-reasons-your-data-integration-plan-is-important
- 2018-07-02-breaking-down-master-data-management
---

Data is the foundation of a company's success. It provides the basis for customer insights, corporate strategy, and operational efficiency. As more companies embark on [digital transformation](https://auth0.com/app-modernization), migrating this critical information — particularly sensitive customer data — to a new platform(s) can be a burden. Moving data can unearth inaccuracies, unknowns, and/or duplicate entries, making it insufficient to use and requiring significant changes. 

The tips below will help ensure that the movement of your most sensitive information is secure, organized, and accessible to your users.

{% include tweet_quote.html quote_text="Data is the foundation of a company's success. This post will help ensure that the movement of your most sensitive information is secure, organized, and accessible to your users!" %}

---

## Issues That Can Arise with Data Migration
Before we dig into strategies for success, it's important to understand the issues that can arise in data migration. The process can be thorny for several reasons.

### Lack of Consideration for the Process
Teams are generally focused on selecting and configuring their new system. Testing potential tools and comparing the models are generally more exciting and inspiring than the mechanics of lifting data out of the legacy package. If data is uploaded in its current state, any errors or irrelevant aspects could become amplified and increase the information's exposure to risk. Because of this, it's important to be sure your team is on the same page about the nuances of the process and has a specific roadmap marked with individual roles and responsibilities.

### Improperly Tracking Users
It's critical to manage user identities and monitor for suspicious activity when you're migrating sensitive information. Companies that rely on consumer data, in particular, must validate all entries against new regulations — [GDPR](https://auth0.com/docs/compliance/gdpr/features-aiding-compliance/protect-user-data), for example, along with more specialized industry standards such as Sarbanes-Oxley (SOX) for financial institutions and the Health Insurance Portability and Accountability Act (HIPAA) for many healthcare providers.

### Poor Communication of Updates
Making sure your end users understand how you are securing their data after you've updated your apps is important for maintaining their trust. If you can't articulate what you are doing with their information, it's unlikely they will stick around. Documenting your work and developing a clear communication strategy are essential to making sure your data migration supports your company's overall goals for growth.

To address these potential issues, the following strategies focus on workflow, user access, and communications.

## 1. Organize Your Workflow and Project Management
To clearly understand and outline the steps required for a successful data migration, take time to consider the following:

- What will cause the least disruption to the daily operations of your system? That is, when are the best times to work on and/or migrate data sets of highest sensitivity or usage?
- What aspects of your strategy can you tag to teammates in-house, and where might you need to bring in outside expertise?
- How will you document your process, ensuring that all information is accounted for?
- Will you enrich your files after they have been moved with further information (e.g., customer names and/or account numbers)?

While this list is not exhaustive and each strategy will be unique, depending on an organization's current systems and future goals, it should aid in understanding the depth of thought required for an efficient data migration.

It can be helpful to use a workflow tool to organize your team and define your process. [Airtable](https://airtable.com/product) has a proven track record for improving internal organization. When mapping out your data migration strategy, Airtable can help define your project scope, set deadlines, and assign individual responsibilities.

For any task that you're organizing, Airtable will help prioritize tasks and ensure that you adhere to your overall goals:

![Airtable](https://cdn.auth0.com/blog/data-migration/1-airtable.jpg)

> [Source.](https://airtable.com/product)

Within the grid view, you can check to see which tasks have been completed and quickly note whom to reach out to in case of a question or concern. Airtable also has a [calendar view](https://airtable.com/product) to better visualize the timeline of your data migration, which can quickly become lengthy and arduous without clear monitoring. Both views can help break down the potentially opaque data migration process into a concrete series of steps.

Additional tools to consider for managing the task of data migration include:

- [Asana](https://asana.com/guide/examples/company/project-management): Highly customizable, popular tool for engineering teams
- [Trello](https://trello.com/): Visual workflow; bulletin-board format
- [Jira](https://www.atlassian.com/software/jira): Supports distributed collaboration

Taking time to think through your steps at a granular level and selecting a tool that will hold your team accountable will help you succeed.

## 2. Clearly Track Who Accesses Customer Data
Understanding who is accessing and updating portions of sensitive customer data at any given time during the data migration process is critical to maintaining integrity. In today's environment of [increasing industry regulations](https://auth0.com/gdpr) around consumer data, this core function must be up-to-date and as tight as possible. 

Because of the complexity and urgency of this task, many teams choose to outsource their [identity management](https://auth0.com/app-modernization) when migrating data, particularly as part of a larger [digital transformation](https://auth0.com/learn/identity-management-drives-digital-transformation/).

[Auth0's user management dashboard](https://auth0.com/b2e-identity-management-for-employees) allows a systems administrator to authorize new users, track their behaviors, and evaluate their performance according to the company's policies.

![Auth0 Dashboard](https://cdn.auth0.com/blog/data-migration/2-auth0-manage.png)

> [Source.](https://auth0.com/learn/cloud-identity-access-management/)

In the Auth0 dashboard, an administrator will have a clear view of users at any point. In addition, Auth0's identity platform offers enhanced options like multi-factor authentication, anomaly detection, and advanced logging. For example, if the administrator notices a certain user has a series of failed login attempts, they can [block access](https://auth0.com/docs/anomaly-detection) to investigate further. 

From a user perspective, this function is an invisible and painless part of their workflow. 

## 3. Explain Your Data Policy Changes
If you can clearly communicate your data migration and updated data usage policies to your users, you will gain their trust — and an edge over your competitors. At the moment, very few company blogs or statements spell out the details of data usage in a digestible format — as we've recently seen with Facebook's [confusing policies](https://www.wired.com/story/mark-zuckerberg-congress-day-one/). 

You have an enormous opportunity to distinguish yourself in the field if you can fill this information gap. A major data migration is a perfect opportunity for you to document previous work and latest updates. Making this information immediately visible, user-friendly, and clearly accessible is key:

![Facebook](https://cdn.auth0.com/blog/data-migration/3-zuck.png)

> [Source.](https://www.recode.net/2018/3/28/17171496/facebook-settings-redesign-cambridge-analytica)

While hopefully your team will not be recovering from a data leak like [Facebook's](https://auth0.com/blog/cambridge-analytica-and-facebook/), the tech titan provides an excellent example of how to communicate a change in policy to users. Three key points to include when explaining your updates are:

1. Place a brief summary of the information on the front of the app where users will see it immediately without any digging.
2. Include a clear CTA or way in which users can click to read further details.
3. Make sure any actions your user must take to delete or update their data settings are very simple.

You must make your user feel as though they are well supported and in full control. Both sets of information — your summary and fine print — should feel friendly and encouraging. Employ your best writers and incorporate welcoming images or a set of bullet points to truly put your best foot forward.

## Don't Be Afraid to Change
[90%](https://auth0.com/learn/how-auth0-uses-identity-industry-standards/) of businesses today state that legacy software systems are keeping them from achieving their goals. Despite increasingly strict regulations, you should not be afraid to scale and innovate. Just be sure you take the process of data migration seriously as you search for your new platform. Make sure the right people access the correct pieces of data at the proper times during the migration and then seize the opportunity to demonstrate how bulletproof your new strategy is with excellent customer communications.

{% include tweet_quote.html quote_text="90% of businesses state that legacy software are keeping them from achieving their goals. Scale and innovate while paying attention to data migration to succeed!" %}
