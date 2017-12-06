---
layout: post
title: "Build your first app with Polymer 2 and Web Components"
description: "Build a Polymer 2 app using Web Components that interacts with an API and authenticates with Auth0's Centralized Login Page"
date: 2017-12-05 17:30
category: Technical Guide, Frontend, Polymer
author:
  name: Luke Oliff
  url: https://twitter.com/mroliff
  avatar: https://avatars1.githubusercontent.com/u/956290?s=200
  mail: luke@lukeoliff.com
design:
  image: https://cdn.auth0.com/blog/logos/polymer2.png
  bg_color: "#324090"
tags:
- polymer-2
- web-components
- javascript
related:
- 2016-08-17-aurelia-1.0-how-to-build-a-simple-secured-application
- 2016-10-05-build-your-first-app-with-polymer-and-web-components
- 2016-11-21-building-and-authenticating-nodejs-apps
---

**TL;DR:**

Link to older article.

## What are we going to use?

In this article we're going to build a simple Polymer 2 application using Web Components.

### Polymer 2

**[Polymer](https://www.polymer-project.org)** is an open-source project created by team of developers at Google that enables us to build cross-browser compatible apps and elements with Web Components. It provides [polyfills for browsers](https://www.polymer-project.org/2.0/docs/browsers) that don't support web components yet. Shadow DOM is difficult and costly to polyfill, so Polymer uses [shady DOM](https://www.polymer-project.org/1.0/blog/shadydom) to [implement the features of shadow DOM](https://www.polymer-project.org/1.0/blog/shadydom#shadow-dom-is-awesome-why-is-there-a-shady-dom) in browsers that lack support.

### Web Components

About Web Components

## What exactly are we going to build?

### The gist

In our Polymer 1 guide to [build your first app with Polymer and Web Components](https://auth0.com/blog/build-your-first-app-with-polymer-and-web-components/) we build an app that called an external Node API to get Chuck Norris quotes. It posted to the API to register and log in users, used [JSON Web Tokens](https://jwt.io/) to fetch protected Chuck Norris quotes for authenticated users, stored tokens and user data with local storage and it logged users out by clearing tokens.

This time, we're building an application that appeals to the *Jedi* in all of us. We're going to randomly assign us a character from a film and provide details on other characters from the same film. Once we're finished no amount of *force* will gain you access. You'll need to authenticate through [Auth0's Centralized Login Pages](https://auth0.com/docs/hosted-pages/login).

### About SWAPI

> All the Star Wars data you've ever wanted.

[Paul Hallet](https://phalt.co/) built two APIs based on sources of current popular culture. [PokéAPI](https://pokeapi.co/) followed shortly by [SWAPI](https://swapi.co/). Paul realised that _if you provide data easily, someone will consume it_, and with hundreds of millions of requests between the sites, he's onto something. Paul has implemented an easy to use, well documented API. 

In his own words, "The Star Wars API is the world's first quantified and programmatically-formatted set of Star Wars data". Clearly the result of time spent watching a lot of movies and reading [Wookiepedia](http://starwars.wikia.com/wiki/Main_Page). 

In my opinion, the only things missing from Paul's API is links to images for the resources– there is probably a reason for that, though.

The API follows the [HATEOAS](https://en.wikipedia.org/wiki/HATEOAS) constraint of [RESTful API](http://searchmicroservices.techtarget.com/definition/RESTful-API) architecture, which in layman's terms means every API response has a link to itself and any related data. RESTful API requests are effectively stateless, having no awareness of the previous request. HATEOAS not only allows the API to self-document–to a certain degree–but also provides us the ability to know what we can do next based upon the context of our request. For example, a request using an `Authorization` header to the `/` root of a HATEOAS API may indeed get a different list of of available resources. Perhaps without an `Authorization` header, the response shows you that your only callable resource is `/login`. Very cool!

> All the Star Wars data you've ever wanted.

[![Run in Postman](https://run.pstmn.io/button.svg)](https://app.getpostman.com/run-collection/c9ff4d265572dd8fc461#?env%5BDefault%5D=W3siZW5hYmxlZCI6dHJ1ZSwia2V5IjoiYmFzZVVybCIsInZhbHVlIjoiaHR0cHM6Ly9zd2FwaS5jby9hcGkiLCJ0eXBlIjoidGV4dCJ9LHsiZW5hYmxlZCI6dHJ1ZSwia2V5IjoicGVyc29uIiwidmFsdWUiOiIxIiwidHlwZSI6InRleHQifSx7ImVuYWJsZWQiOnRydWUsImtleSI6ImZpbG0iLCJ2YWx1ZSI6IjEiLCJ0eXBlIjoidGV4dCJ9LHsiZW5hYmxlZCI6dHJ1ZSwia2V5Ijoic3RhcnNoaXAiLCJ2YWx1ZSI6IjIiLCJ0eXBlIjoidGV4dCJ9LHsiZW5hYmxlZCI6dHJ1ZSwia2V5IjoidmVoaWNsZSIsInZhbHVlIjoiMjAiLCJ0eXBlIjoidGV4dCJ9LHsiZW5hYmxlZCI6dHJ1ZSwia2V5Ijoic3BlY2llcyIsInZhbHVlIjoiMyIsInR5cGUiOiJ0ZXh0In0seyJlbmFibGVkIjp0cnVlLCJrZXkiOiJwbGFuZXQiLCJ2YWx1ZSI6IjEiLCJ0eXBlIjoidGV4dCJ9XQ==)
 
## Before we start

We need npm, bower, git and the **Polymer CLI**.

### Check that you have node and npm installed

To check if you have Node.js installed, run this command in your terminal:

```bash
node -v
```

npm is distributed with Node.js– which means that when you download Node.js, you automatically get npm installed on your computer.

> **Node.js** is an open-source, cross-platform JavaScript run-time environment for executing JavaScript code server-side. **npm** is a package manager for the JavaScript programming language. It is the default package manager for the JavaScript runtime environment Node.js.

To confirm that you have npm installed you can run this command in your terminal:

```bash
npm -v
```
If you don't have Node.js or npm installed, [download Node.js and npm](https://nodejs.org/) here.

Update npm to the latest version.

```bash
npm install npm@latest -g
```

### Install git

Ensure that Git is installed. If it isn't, you can find it on the [Git downloads page](https://git-scm.com/downloads).

> **Git** is a free and open source distributed version control system designed to handle everything from small to very large projects with speed and efficiency.

```bash
git --version
```

### Install bower

Install the latest version of Bower.

```bash
npm install -g bower
```

### Install Polymer CLI

> Polymer CLI is the official command line tool for Polymer projects and Web Components.

```bash
npm install -g polymer-cli
```

## Polymer 2 Starter Kit

**Polymer 2** comes with a handy toolbox for developers. 

> [Polymer App Toolbox](https://www.polymer-project.org/2.0/toolbox/) is a collection of components, tools and templates for building Progressive Web Apps with **Polymer**. The Toolbox elements and behaviors are available as hybrid versions, which can be used with both Polymer 1 and Polymer 2. Use the 2.0 branch of the elements when working with 2.0 Release.

Using this toolbox we can initialize a new **Polymer** application using a built in Starter Kit.

So, to get started, create our new application's directory (and change into the new directory).

```bash
mkdir polymer-2-app && cd "$_" 
```

> `$_` is a special parameter that holds the last argument of the previous command. Place quotes around `$_` make sure it works even if the argument contained spaces.

Use Polymer CLI to create the starter kit application.

```bash
polymer init polymer-2-starter-kit
```

Run the polyserve development server.

```bash
polymer serve
```

## Customising the Starter Kit

Dress it up with some styling

## Integrating with an API

swapi.co!

## Aside: Auth0 Authentication in Polymer 2

Add logins n' stuff

## Conclusion

Reflect on changes since 1.0, uptake of Web Components, common gripes with the framework

