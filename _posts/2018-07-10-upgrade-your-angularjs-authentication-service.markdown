---
layout: post
title: "Upgrade Your AngularJS Authentication Service"
description: "Learn about upgrading an application from AngularJS to the new Angular. As well as how to upgrade AngularJS authentication strategies to Angular."
date: 2018-07-10 08:30
category: Technical Guide, Angular
author:
  name: "Sam Julien"
  url: "https://www.upgradingangularjs.com/?ref=auth0"
  avatar: "https://cdn.auth0.com/blog/guest-authors/sam-julien.jpeg"
design:
  bg_color: "#012C6C"
  image: https://cdn.auth0.com/blog/logos/angular.png
tags:
- angular
- angularjs
- ngupgrade
- authentication
- auth0
- ngupgrade
related:
- 2018-04-24-new-features-for-developing-angularjs-applications-part-1
- 2017-06-28-real-world-angular-series-part-1
- 2018-03-13-using-python-flask-and-angular-to-build-modern-apps-part-1
---

**TL;DR:** In this article, we’re going to talk about two notoriously difficult subjects very near and dear to my heart: ngUpgrade and authentication. First, we’ll cover the foundations of upgrading a real application from AngularJS to Angular. Then, we’ll look at a practical example of upgrading AngularJS authentication strategies to Angular. We’ll look at a sample app that uses machine-to-machine authentication with Auth0.

{% include tweet_quote.html quote_text="Learn how to migrate from AngularJS to the new Angular framework." %}

> This article is based on a talk I gave for [the Auth0 online meet up](https://www.meetup.com/Auth0-Online-Meetup/). [You can watch the recording of that talk here](https://register.gotowebinar.com/register/7495371156540204033) and [access the slides here](https://www.upgradingangularjs.com/auth0).

## ngUpgrade Foundations

Back in late 2016, I was going through some very painful issues trying to upgrade my company’s apps from AngularJS to Angular. I was completely overwhelmed and totally lost. Rather than rage-flip my desk and go live in a cave, I decided to channel my frustrations into learning everything I could about the upgrade process and documenting it along the way for the community. That’s where my video course [Upgrading AngularJS](https://www.upgradingangularjs.com/?ref=auth0) came from my own blood, sweat, and tears of figuring this process out. I’m hoping to save other people the hundreds of hours that I put into it.

That’s also why I’m writing articles like this one — to help you save time. 

### ngUpgrade Background

Let’s start with a bit of background. AngularJS (1.x) entered a [long term stable schedule](https://blog.angular.io/stable-angularjs-and-long-term-support-7e077635ee9c) on July 1st, 2018 that will last until June 30, 2021. The last stable version is going to be version 1.7. So, where does that leave us? That leaves us with the new Angular (2+). You’ve now got this bridge period to determine if you need to upgrade and, if so, to complete that upgrade using the ngUpgrade library.

Most of the time, you should upgrade, but there are a handful of exceptions. The main exception is if your application is just going to lay dormant from now on. If you’re never going to do any more feature development on it, you don’t need to upgrade. The AngularJS packages and CDNs will stay around forever. You don’t need to worry about your legacy code evaporating into thin air and being unable to do anything about it.

That being said, if you are doing feature development, you should do yourself a favor and move over to Angular. Angular is much faster, has a lot better features, and has lots of architecture and data flow best practices already baked into the framework. Don’t worry, though. You don’t have to do it all in one shot — you can do it gradually and take the time that you need do it right.

Another question I get asked a lot is, “Should I just rewrite everything to React/Vue/whatever?” Most of the time, the answer is no, unless you’ve already got team members who have that expertise (or you just, for whatever reason, really want to move to a different framework). It’s very unlikely it’s going be easier to move to another framework. The ngUpgrade library, which is the `@angular/upgrade/static` package, is explicitly made to bridge the gap between AngularJS and Angular. There’s no such equivalent library for React or Vue.

## The ngUpgrade Process

Let’s get a high-level look at what the upgrade process looks like. There are two distinct phases. 

### Phase 1: Preparation

The first is the preparation phase. One of the nice things about AngularJS was that it was extremely flexible. You could use AngularJS in a lot of different situations, from simple data binding to complex single-page applications.

That flexibility had a downside, though. For a long time, there wasn’t a collection of best practices for AngularJS, until John Papa came along and codified everything in the [AngularJS style guide](https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md). That was really helpful, because that it was really confusing having so many possible ways to structure applications. Not all applications were able to immediately conform to the style guide, though. That means we have many possible starting points for ngUpgrade. There’s no “one size fits all” solution. For that reason, part of the ngUpgrade process is getting your AngularJS code conformed to best practices.

The preparation phase consists of four **building blocks** to the ngUpgrade process:

- Your **file structure**. There are two critical things here to get in line with the style guide. First, organize your files by feature, not by type. Second, have only one item per file.
- Your **dependencies**. We at one point were a divided community on how best to manage dependencies. Now, we’ve settled on npm (or yarn) as the best way to manage our packages and dependencies (no more Bower!). We also need to be using one of the latest versions of AngularJS (at least 1.5, but preferably the most recent). 
- Your **architecture**. AngularJS 1.5 introduced the component API, which enabled component architecture. Moving your application to component architecture is part of the preparation phase. AngularJS 1.5 also introduced things like life-cycle hooks and one-way data binding. As you upgrade, you’ll need to replace controllers with components and get rid of all of those instances of `$scope`. You don’t necessarily need to get everything in tip-top shape before you even start using ngUpgrade, but your end result will be component architecture, whether in AngularJS or Angular. The closer you are to components, the easier your upgrade will be.
- Your **build process**. The build process is the tooling that we want to do to get all of our application compiling and ready for the ngUpgrade library. Practically speaking, this means using Webpack for module bundling and Typescript for your code. This means replacing script tags or task runners like Gulp and Grunt with ES6 modules. Our goal is to make our tooling exactly in line with the Angular tooling so that at some point we can move to the Angular CLI.

Speaking of the CLI — if you can use it right away in your legacy application, by all means, do so. In certain situations, though, you can’t really get the CLI into an upgrade project right off the bat. You might have your code structure all over the place or a very customized build process. In these cases, you‘ll need to wait until you’ve at least cleaned up your code and architecture so that your application fits better with the CLI. The CLI is highly opinionated in how it wants to organize your code, and it wants to own your build process. This is fantastic in the long run — it solves the exact problem of the early days of AngularJS — but in the short term it can be frustrating if you’re working with a big application that uses old patterns. 

One last note on working through these four building blocks. It’s not necessarily a linear process. Not all of this needs to be completely perfect before you start moving on to ngUpgrade. Particularly, the architecture part can really be done slowly or at the same time as the upgrade process. It really depends on how big your application is and how much capacity you have for working through this technical debt while you’re also doing feature development and bug fixes. The only one that is really critical outside of being up to date with your dependencies is the _build process_ because we really do need a module bundler and TypeScript in order to use ngUpgrade and start moving our application over.

### Phase 2: Upgrading

Now let’s talk about the upgrading phase, which has three different parts. 

The first one is the **install and setup**, where you’ll install all of the packages for Angular and ngUpgrade and then bootstrap both frameworks to run side by side together. (We won’t cover that in detail here, but I wrote a thorough post over on Scotch.io to help you with that.)

Then the second part is the **migration process**. This is the loop of gradually moving pieces of your application over to Angular and using ngUpgrade to get your Angular and AngularJS code talking to each other. There are different approaches to this, but most people pick a route and start from the bottom or the top depending on how their application is structured. You’ll typically move from items that have the least number of dependencies to the most number of dependencies. For example, I usually start with the services on a route and then work my way up through the components.

This migration process can take as long as you need. I like to get the application all set up for ngUpgrade and then draw a line in the sand that all of my new features will be in Angular. Then, as I’m adding a new feature in Angular, inevitably I’m going to touch other pieces of the code base. While I’m doing that, I can just rewrite those pieces and work my way through it so that it’s a natural process throughout my development cycle.

Finally, leave your routing to the very end, since the routing is the brain of the application. Get everything rewritten to Angular, then swap out the old AngularJS router with the new Angular router.

Somewhere in here, you’re going to have to deploy this code to production if you want to keep bringing home a paycheck. You’re going to have to **set up your code for production**. This means that you need to take advantage of Angular’s static compilation process, called Ahead of Time compiling. Angular has two modes that it can run in. It can run in a Just in Time (JIT) compilation mode or it can run in Ahead of Time (AOT) compilation mode.

The compiler takes up a big chunk of the Angular library. When you’re in development and using JIT compilation, that compiler is being wrapped up in your bundle and going with you to the browser. 

![The compiler takes up a big chunk of Angular.](https://cdn.auth0.com/blog/ngupgrade/compiler-chunk.png)

When you use the AOT compilation process, everything gets compiled ahead of time and the compiler code itself doesn’t have to be put into your bundle.

There are a bunch of other reasons that you want to do this, like better security, but the bottom line is that AOT is smaller and faster than JIT, so it’s better for production. To read more about how to set up Webpack for AOT, you can [check out this article I wrote](https://medium.com/@UpgradingAJS/the-ultimate-guide-to-setting-up-aot-for-ngupgrade-without-jumping-out-a-window-998df2fdd196). I also have step-by-step videos on [this setup in my course](https://www.upgradingangularjs.com/?ref=auth0).

Now that we’ve got a handle on the different parts of the upgrade process, let’s dive into a practical example of Phase 2’s **migration process**.

## The Sample Application

Go ahead and [clone this updated fork of my course sample project](https://github.com/upgradingangularjs/ordersystem-evergreen) (don’t forget to run `npm install` or `yarn install` in both the `server` and the `public` folders). You can check out the `auth0` branch and the starting commit by running these commands:

```bash
git checkout auth0
git checkout c6b2055f831134b616452ad3319309817ab9d574
```

This basic order system application starts off as a hybrid application with AngularJS 1.6 and Angular 5 (don’t worry, nothing we cover in this tutorial is different in Angular 6). It’s not too fancy, but I’ve made it in a way that is extensible and uses some patterns that you’ve seen in the real world in AngularJS.

### Current Authentication Setup

The Express server is set up with Auth0 with a machine-to-machine API and client relationship. I used machine-to-machine because a user login system is an extra layer of complexity that, for this purpose, doesn’t actually matter that much. All we really care about is the ability to go get a token and add it to our outgoing requests to be able to get our data. Whether that’s specific to a user or to an application doesn’t matter. (I should also say that, while I’m using Auth0, the approach we take in this article applies to any sort of token system.) 

I’ve also got two routes using authentication: the customers' route and the products route. This is because the customers' route is using an Angular component and service, but the products route is still using AngularJS pieces. That’ll let us see both approaches and how to upgrade.

If you want to follow along, you’ll need to use Auth0 to set up your own machine-to-machine API and application, since this server is just running locally. You can do that from the Auth0 dashboard. First [create the API](https://auth0.com/docs/quickstart/backend/nodejs), then create the application as a machine-to-machine application.

![Machine to machine application.](https://cdn.auth0.com/blog/ngupgrade/machine-to-machine-auth0-api.png)

You’ll wire up this application to use the API you just made.

Once you’re done with the Auth0 setup, you can just edit `server/auth.js` to replace my API information with yours:

```js
const checkJwt = jwt({
  secret: jwksRsa.expressJwtSecret({
    cache: true,
    rateLimit: true,
    jwksRequestsPerMinute: 5,
    jwksUri: `https://samjulien.auth0.com/.well-known/jwks.json` // <-- replace
  }),

  // Validate the audience and the issuer.
  audience: 'ordersystem-api', // <-- replace
  issuer: `https://samjulien.auth0.com/`, // <-- replace
  algorithms: ['RS256']
});
```

You’ll also need to add a file called `authVariables.js` to the root of the project and export your application’s client id and client secret as variables, like this:

```js
export const CLIENT_ID = '[client id here]';
export const CLIENT_SECRET = '[client secret here]';
```

The AngularJS AuthService will use that file to go get the token from the API.

On the client side (the `public` folder), inside of our `src` folder, we have a file called `app.run.ajs.ts`. This file contains a function that calls the authentication service to check if we’re authenticated and go get the token if not:

```typescript
runAuth.$inject = ['authService'];
export function runAuth(authService) {  
  if (!authService.isAuthenticated()) authService.getToken();
}
```

This function gets added to our AngularJS module using `angular.module().run()` in `app.module.ajs.ts`.

To add our token to outgoing requests, we have an HTTP interceptor for the AngularJS `$http` provider (`auth.interceptor.ajs.ts`). It goes and gets the token from local storage and then it adds the authorization header with the bearer token before the request goes out.

The authentication service (`./shared/authService.ts`), which is also still in AngularJS, goes and gets the token from Auth0 (via the server) and then sets that token in local storage. 

To see that this is working, open a terminal and run `npm start` inside of the `server` folder. Then, open another terminal and run `npm run dev` inside of the `public` folder. Navigate to [`localhost:9000`](localhost:9000), open up Chrome developer tools, and click on the Customers tab. You can see that the token has been added as a header on the outgoing request:

![The Authorization header on the customer's request.](https://cdn.auth0.com/blog/ngupgrade/authorization-header.png)

You can do the same thing with the Products tab, which also uses authentication for the `/products` call to the Express server.

Of course, this is a very simple way of doing token authentication. We could make this more sophisticated in a number of ways, like adding error handling, but I want you to understand the basic concepts here.

### Adding an Angular Interceptor

There’s one problem in this setup. The `getCustomers` call of the CustomerService (in `./customers/customer.service.ts`) is actually cheating: 

```typescript
getCustomers(): Observable<Customer[]> {    
  return this.http.get<Customer[]>('/api/customers', {      
    headers: { Authorization: `Bearer ${localStorage.access_token}` }    
  });  
}
```

I’m just manually adding the header here, not using the interceptor. If we were to comment this out and let Webpack re-bundle, we’d no longer be able to get our customers data, because the token would no longer be attached to the request.

Why is this? AngularJS’s `$http` and Angular’s `HttpClient` don’t talk to each other. We have to bridge this gap somehow. To do this, we’ll write an interceptor for the HttpClient in Angular. 

To add an interceptor, let’s create a new file at the root of our source folder called `auth.interceptor.ts`. In that file, we’ll add this new class:

```typescript
import { Injectable } from '@angular/core';
import {
  HttpRequest,
  HttpHandler,
  HttpEvent,
  HttpInterceptor
} from '@angular/common/http';
import { Observable } from 'rxjs/Observable';

@Injectable()
export class AuthInterceptor implements HttpInterceptor {
  constructor() {}
  intercept(
    request: HttpRequest<any>,
    next: HttpHandler
  ): Observable<HttpEvent<any>> {
    request = request.clone({
      setHeaders: {
        Authorization: `Bearer ${localStorage.access_token}`
      }
    });
    return next.handle(request);
  }
}
```

We’re importing HttpRequest, HttpHandler, and a few other things from HttpClient, and we’re importing Observable from RxJS (note: this app was created using RxJS 5, so we’re using the old import style in this article). Then, we’re implementing the `HttpInterceptor` interface by having an `intercept` function. This function is pretty similar to the one in the AngularJS interceptor. We take the request, clone it, and then set the header with the access token from local storage.

To complete this step, we just need to add this as a provider to our Angular module (`app.module.ts`). We’ll need to import Angular’s `HTTP_INTERCEPTORS` and our new interceptor, and then add a special provider to the `providers` array:

```typescript
// add to the top of the file
import { HTTP_INTERCEPTORS } from '@angular/common/http';
import { AuthInterceptor } from './auth.interceptor';

// in Providers array
{
  provide: HTTP_INTERCEPTORS,
  useClass: AuthInterceptor,
  multi: true
}
```

Now you can just remove that config object from the `getCustomers` call, save everything, and let Webpack re-bundle. You should see that the data is loading correctly because the correct header is still being added to the request.

You can see [the finished code for this section here](https://github.com/upgradingangularjs/ordersystem-evergreen/commit/77283ef3d8e9ebe4b5ac883430313f3268543d03/).

One quick point of clarification: don’t get rid of the AngularJS interceptor until you’re done converting all of your services over to Angular. You’re not going to be able to use Angular’s HttpClient interceptor with your AngularJS services. 

### Taking Advantage of Angular’s APP_INITIALIZER

We’ve got our Angular services using an HttpClient interceptor. This is nice, but we do have a little bit of a problem here. There’s a delay in how the token retrieval gets set up. If you delete the token out of local storage and refresh while on the Customers route, you’ll see that the customers call is being made before the token resolves:

![We’re trying to get the customers data before the token resolves!](https://cdn.auth0.com/blog/ngupgrade/wrong-flow.png)

We’re trying to get the customers data before the token resolves! That's no good.

We could fix this in our AngularJS code by correcting the way the promises are resolving, but there’s a better way. This is a really practical example of working through ngUpgrade and realizing when you need to just rewrite and move on, and when you need to spend more time in your AngularJS code.

Most of the time, you want to start moving away from the AngularJS part and implementing things in Angular. We’re going to do this right now by replacing our AngularJS run block with something called `APP_INITIALIZER` in Angular. To do that, we need to do a couple of things. First, we’ll rewrite our authentication service to Angular. Then, we’ll downgrade and provide it to our AngularJS application so that our AngularJS authenticated routes will still work. Finally, we’ll use `APP_INITIALIZER` to call the AuthService when our application spins up.

#### Rewrite to an Angular Service

To get started on this part, checkout this commit:

```bash
git checkout abd3e34cbbaa8bbed004f56796d37e7ed28f73e2
```

In this commit, I’ve done a small refactor to the app run block and the authentication service. In the run function, I removed that bit that was checking for `isAuthenticated` and moved that responsibility to the authentication service.

Let’s rewrite this service to Angular. Since this service is already an ES6 class, there aren’t too many steps. First, let’s rename it to `auth.service.ts` to follow better conventions. Now, at the top of the file,add these two imports:

```typescript
import { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';
```

Add the `@Injectable()` decorator above the class.

Next, we’ll rename all of the references to `$http` with `http` (just do a find and replace to make your life easier). In the constructor, specify that the private instance of `http` is of the type `HttpClient`:

```typescript
constructor(private http: HttpClient) { }
```

You should now see that TypeScript is griping about all of the instances of `.then`. That’s because HttpClient returns observables instead of promises. One strategy that I’ve found very helpful in ngUpgrade is not trying to tackle observables right off the bat. I love observables, but when you’re in a real-world production application, it doesn’t always work to go immediately to observables. Thinking reactively is a brand new pattern for most folks, and sometimes you need to ease your way there. You can do this by converting observables to promises, making sure everything is working, and then refactoring the promises back to observables down the road. That’s what we’re going to do here, just for the sake of a practical example. 

Add these imports to the top of the file:

```typescript
import { Observable } from 'rxjs/Observable';
import 'rxjs/add/operator/toPromise';
```

(Again, this is the RxJS 5 import method, but feel free to use RxJS 6 and update these imports accordingly — import `Observable` from `rxjs` and `toPromise` from `rxjs/operators`.)

Now, you can just add `.toPromise()` to all of the calls prior to the `.then` and make TypeScript happy again. You can also get rid of `.data` in the return because HttpClient is good enough to just go ahead and return the data of the response body.

You should have this:

```typescript
mport { Injectable } from '@angular/core';
import { HttpClient } from '@angular/common/http';

import { Observable } from 'rxjs/Observable';
import 'rxjs/add/operator/toPromise';

import { CLIENT_ID, CLIENT_SECRET } from '../../../authVariables';

@Injectable()
export class AuthService {
  constructor(private http: HttpClient) {}

  getToken() {
    if (!this.hasToken() || !this.isAuthenticated()) {
      const url = 'https://samjulien.auth0.com/oauth/token';
      const body = `{
        "client_id":"${CLIENT_ID}",
        "client_secret":"${CLIENT_SECRET}",
        "audience":"ordersystem-api",
        "grant_type":"client_credentials"}`;
      const options = { headers: { 'content-type': 'application/json' } };
      return this.http
        .post(url, body, options)
        .toPromise()
        .then(response => {
          this.setSession(response);
        });
    }
  }
  
  // unchanged
  isAuthenticated() { ... }

  // unchanged
  hasToken() { ... }

  // unchanged
  setSession(authResult) { ... }
}
```

Believe it or not, that’s all we have to do to rewrite this service to Angular. Even the local storage API is the same. That wasn’t that bad, was it? Now we can add it to our Angular module. Just open up `app.module.ts`, import AuthService at the top, and add AuthService to the providers array.

```typescript
// Import below the CreateOrderComponent
import { AuthService } from './shared/auth.service';

// Updated providers array
providers: [
    CustomerService,
    OrderService,
    locationServiceProvider,
    productServiceProvider,
    addressServiceProvider,
    {
      provide: HTTP_INTERCEPTORS,
      useClass: AuthInterceptor,
      multi: true
    },
    AuthService
  ]
```

But how do we make sure our AngularJS code can still access that service? That’s where ngUpgrade comes into play. We’re going to use ngUpgrade’s `downgradeInjectable` function in our AngularJS module to make this Angular service available to our legacy code. 

Open up `app.module.ajs.ts`. First, fix the import up at the top since we renamed our file. Then, where we’re registering the service, we’re going to change it to a factory and use that downgrade function just like with the OrderService and CustomerService:

```typescript
// Fixed import
import { AuthService } from './shared/auth.service';

// ...other stuff in between
angular
  .module(MODULE_NAME, ['ngRoute'])
// ...other registrations
  .factory('authService', downgradeInjectable(AuthService));
```

Great. You should see the application still bundling and loading correctly, and everything should work the same way. Great work!

[Here’s the finished code so far](https://github.com/upgradingangularjs/ordersystem-evergreen/commit/e4464a42734213d02c95a743a1062224a6572b8d).

#### Getting the Token at Run Time

Now we can do the fun part: get rid of our app run function in AngularJS and replace it with the new Angular `APP_INITIALIZER`. Let’s go back over to the Angular module. We’re going to provide a function as a factory for Angular. You can just paste this above the `NgModule` decorator:

```typescript
export function get_token(authService: AuthService) {
  return () => authService.getToken();
}
```

This function injects the AuthService and calls its `getToken` function. Now we can use `APP_INITIALIZER` to provide a factory that calls this function at start-up. Add this new object after the interceptor and AuthService in the `providers` array:

```typescript
{
  provide: APP_INITIALIZER,
  useFactory: get_token,
  deps: [AuthService],
  multi: true
}
```

You’ll also need to add `APP_INITIALIZER` to the `import` property on the line where we import `NgModule`(from `core`).

Let’s try this out. Go comment out the registration of our `run` function in the AngularJS module (`app.module.ajs.ts`) and make sure everything compiles and bundles. Then, hop over to the browser. Clear out your application storage using Chrome developer tools to make sure we’re really starting from scratch, and then refresh the page. You should see that this works, no matter which route we’re on. Try it on the Customers tab to see it in Angular, and then Products tab to see it in AngularJS. You’ll see that the token gets called and loaded prior to when the interceptor uses it to make a call. Nice!

To finish this up, you can now remove the `run` function and its import from the AngularJS module, and delete the `app.run.ajs.ts` file. [You can see all of the finished code here](https://github.com/upgradingangularjs/ordersystem-evergreen/commit/e0fd2ec2ecac606ce59948ea86a84f5ad5cc8996).

{% include tweet_quote.html quote_text="Check out this article on upgrading AngularJS apps to the new Angular framework." %}

## What We’ve Covered

Let’s review. We started with an app run block, which called a service to get the token and then used an interceptor to attach the token to requests. All of this was happening in AngularJS, so this didn’t work in the customer service — the Angular HttpClient couldn’t use that interceptor. To fix that, we added our own Angular interceptor.

The interceptor helped, but it wasn’t quite enough, because there was a lag in our token retrieval. We needed to come up with a way that Angular could run our token at runtime. To do that, we first had to rewrite our authentication service to Angular. Then, we added our `APP_INITIALIZER` function and provider.

This demonstration proved a couple of things for us. First, AngularJS and Angular code don’t automatically talk to each other. They run in parallel, and we need to use ngUpgrade to bridge that gap. Second, do your best to rewrite and downgrade when you can. We could have tried to fix our token timing problem by working on AngularJS code — by fixing up the promises or making the routing more sophisticated. It turned out it was simpler to solve this problem by just rewriting to Angular. Don’t put a bunch of effort into updating your legacy code if you can help it. Try to just rewrite your AngularJS code one piece at a time.

If you’re stumped on any of this, I’ve got your back. [I’ve got 200+ detailed videos, quiz questions, and more for you in my comprehensive course Upgrading AngularJS](https://www.upgradingangularjs.com/?ref=auth0). I created it for everyday, normal developers and it’s the best ngUpgrade resource on the planet. Head on over and sign up for our email list to get yourself a free Upgrade Roadmap Checklist so you don’t lose track of your upgrade prep. And, while you’re there, check out our full demo.

See you next time!
