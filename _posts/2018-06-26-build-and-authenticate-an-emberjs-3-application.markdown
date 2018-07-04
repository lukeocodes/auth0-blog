---
layout: post
title: "Build and Authenticate an EmberJS 3 Application"
description: "Learn how to build and authenticate an EmberJS 3 Single Page Application"
date: 2018-06-26 8:30
category: Technical Guide, Frontend, Ember
banner:
  text: "Auth0 makes it easy to add authentication to your EmberJS application."
author:
  name: "Sarah Jorgenson"
  url: "https://twitter.com/sarahjay57"
  mail: "sarahjorgenson5@gmail.com"
  avatar: "https://en.gravatar.com/avatar/8ffba6822a7500b6d75e24fad62d8ac0"
design:
  image: https://cdn.auth0.com/blog/ember-simple-auth/ember-logo-small.png
  bg_color: "#412b13"
tags:
- emberjs
- authentication
- spa
- javascript-framework
- ember
related:
- 2016-09-29-angular-2-authentication
- 2018-05-22-building-modern-applications-with-django-and-vuejs
- 2018-05-07-whats-new-in-angular6
---

**TL;DR:** Let’s dive into the world of [**EmberJS**](https://www.emberjs.com/), a framework built so that we all can be more productive as developers. Easy to jump in to and easy to get started with, EmberJS is a great framework to know and understand. In this tutorial, we will be building an application using EmberJS 3 and then add some **trusty authentication** to it. You can find the [GitHub repo here](https://github.com/SarahJay55/ember-js-auth) to get the code from this tutorial. 

## EmberJS

Which framework should I use? Which one would be best for me and my project? We get it, there are tons to choose from.  Let’s take some time and let’s learn more about the powerful framework we like to call EmberJS.  

We have the new EmberJS 3.0 that was released February 14, 2018. There were no functionality concepts added, instead the Ember team honed in on making the framework cleaner and more focused.  You will not have to deal with long-deprecated APIs anymore but you will see support for those legacy platforms. If you are already familiar with EmberJS 2.0, EmberJS 3.0 should be just fine.

{% include tweet_quote.html quote_text="EmberJS 3.0 did not add new functionality concepts, but instead concentrated on making the framework cleaner and more focused." %}

**Who Should Use It?**

Are you looking for an all-inclusive style framework? One that you do not have to be plugging in multiple libraries to make things work? Ember might be for you then. It may save lots of time so you are not looking around, trying to make things fit just right. Let EmberJS do that work for you, you just start building those beautiful apps!

## The Five Key Concepts

Who here has worked with Angular? AngularJS? React? Vue? Well then, this might look very familiar to you! EmberJS will be easy to understand and you’ll be making apps in no time! Do these things look familiar?

  1. Routes - Being URL based like many modern frameworks, this allows for easy navigation whether the user is visiting the base URL for the first time, or clicking a link on the page to be routed to another page.
  2. Models - Objects that equate the data that your application shows in the user's view.
  3. Templates - Using Handlebar Templating (learn more about [Handlebar Templates here](https://handlebarsjs.com/)), it contains the HTML for the application.
  4. Components - Consisting of a JavaScript file and Handlebar Templating, components capture the data into reusable information for the application.
  5. Controllers - Behaving like a specialized type of Component that is rendered by the router when entering a Route.

These are the five key concepts when using EmberJS. Ember also provides many other things, yet knowing and understanding these main five, you will be off to a good start.

{% include tweet_quote.html quote_text="EmberJS's core concepts include routes, models, templates, components, and controllers. These are shared by many SPA frameworks. Learn them well!" %}

### Packages used in EmberJS

Throughout the code, you will see imports referencing things like `@ember/utils`. These are Ember Packages. Ember packages have a number of methods, properties, events, or even functions that can be used within your project. There are many to chose from and you can find the reference to these packages in the [EmberJS Packages](https://www.emberjs.com/api/ember/3.1/modules/@ember%2Fapplication).

## Ember CLI

Now in EmberJS, whenever you want to generate a new file, all you need to do is type in 

`ember generate (what you want to do) and (what you want to call it)` 

For example, say I want to create a new route, I would type into my command line, 

```bash
ember generate route login
```

That would create a routing file named `login`. Within your `app` file, you will see in the route folder, a new file named `login`. Ember's CLI does a lot behind the scenes. As you generate new files, pay attention to the other files that Ember will automatically generate. 

## Let’s Build!

We are going to be building an application that uses authentication to jump into a mock bank account. Numbers won’t be real, don’t worry, we’ll use some JavaScript to generate a random number. We need some strong authentication to keep our bank account safe.  

What will our user see?

1. User sees a navbar with a login button
2. User logs in with their credentials via Auth0
3. User sees their name/username in the navbar and randomly generated bank account balance (if only we could keep the balances that randomly generate to 1,000,000).
4. Logout button and Dashboard button next to the name in the navbar

## EmberCLI and Setup

The ember-cli is an awesome tool that allows you to do a lot of app building within the command line. We will need the ember-cli global on our machine so open up your command line and let’s type in:

```bash
npm install -g ember-cli
```

Once that is done, let’s get an EmberJS app started! We will be making a login feature that takes us to a faux bank account. To get this going all you have to do is navigate to the folder you want this project in and type:

```bash
ember new ember-js-auth
```

Sit back and relax, Ember will be taking care of a lot of stuff right now. Once it has completed, let’s cd into that new directory by typing in:

```bash
cd ember-js-auth
```

and then in that run:

```bash
ember serve
```

to get the app started up in your browser. You can find it on http://localhost:4200 if it does not pop up right away. Once you see this home page, you are ready to go:

![Ember Welcome Page](https://i.imgur.com/y48FR0N.png)

Your EmberJS application is ready to use!

### Styling

For styling, we will be using [Bulma](https://bulma.io/) styling in today's app. Insert the bulma link tag into our index.html so we can use that styling throughout our application.

Open up your index.html file and insert the bulma link into the head tag as shown:

{% highlight html %}
{% raw %}
<!-- ember-js-auth/app/index.html -->
<!DOCTYPE html>
  <head>
    <meta charset="utf-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <title>EmberJsAuth</title>
    <meta name="description" content="">
    <meta name="viewport" content="width=device-width, initial-scale=1">

    {{content-for "head"}}
       
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/bulma/0.6.2/css/bulma.min.css">
    <link rel="stylesheet" href="{{rootURL}}assets/vendor.css">
    <link rel="stylesheet" href="{{rootURL}}assets/ember-js-auth.css">

    {{content-for "head-footer"}}
  </head>
{% endraw %}
{% endhighlight %}

### Authentication with Auth0

Today we will be using Auth0 for authentication. The authentication with Auth0 uses a central domain when the user wants to log in. No matter where the user is in the application, the authentication happens in one spot.

## Setting Up Auth0

Let's step away from our code for a second and go to [Auth0.com](https://auth0.com/) where we will log in. Once logged in, you will want to create a new single-page application so that we can have access to some very important information.  

![New Application Button](https://i.imgur.com/ByUiHbF.png)

![Create Application Page](https://i.imgur.com/6jxobLc.png)

We will be generating a couple of things that we will need in our application under Settings. We will not be needing them right this second but soon. Keep those two things close by! (aka, don't close the tab) :)

1. Domain - example.auth0.com
2. Client ID - a string of random numbers and letters
3. Callback URL - http://localhost:4200/callback

> Note! Now that you have your Auth0 Application, go to the Settings tab and add http://localhost:4200/callback to the Allowed Callback URLs and in the Allowed Logout URLs add http://localhost:4200 and hit the Save button. Then, leave the Settings tab open as you will need to copy some properties from it soon.

![Allowed Callback URLs](https://i.imgur.com/1y32iKn.png)

![Allowed Logout URLs](https://i.imgur.com/lxShywS.png)

## Auth0 Install

Now let's go back to our app and install our Auth0 library. 

> To learn more about the Auth0 library, you can find a lot of useful information [here](https://github.com/auth0/auth0.js). 

We will now go into the command line of the project and type: 

```bash
npm install --save auth0-js
```

What this is doing is it is giving you the correct Auth0 library and putting in your dependencies list in your `package.json` file. 

## Services in Ember

According to the [EmberJS docs](https://guides.emberjs.com/release/applications/services/) themselves, "an Ember.Service is an Ember object that lives for the duration of the application, and can be made available in different parts of your application." Let's let that one soak in for a bit, an application-wide state? The information that goes into this file, all files can see! This file loves to share. To go along with that it also keeps that information alive while the user is using the application. The data is saved and saved and saved until, boom, they log out and "reset" the service.

> Something an EmberJS Service would be good for would be things like geolocation, third-party APIs, or user authentication.  

In your command line type:

```bash
ember generate service auth
```

Here, in this newly created file, we will insert all of our logic for getting a session, logging in, logging out, and everything associated with authentication.

Open up the `app/services/auth.js` file and insert the following information:

```javascript
// ember-js-auth/app/services/auth.js
import Service from '@ember/service';
import { computed } from '@ember/object';
import config from 'ember-js-auth/config/environment';

export default Service.extend({
  
  /**
   * Configure our auth0 instance
   */
  auth0: computed(function () {
    return new auth0.WebAuth({
      // setting up the config file will be covered below
      domain: config.auth0.domain, // domain from auth0
      clientID: config.auth0.clientId, // clientId from auth0
      redirectUri: config.auth0.callbackUrl,
      audience: `https://${config.auth0.domain}/userinfo`,
      responseType: 'token',
      scope: 'openid profile' // adding profile because we want username, given_name, etc
    });
  }),

  /**
   * Send a user over to the hosted auth0 login page
   */
  login() {
    this.get('auth0').authorize();
  },

  /**
   * When a user lands back on our application
   * Parse the hash and store user info
   */
  handleAuthentication() {
    return new Promise((resolve) => {
      this.get('auth0').parseHash((err, authResult) => {
        if (err) return false;
        
        if (authResult && authResult.accessToken) {
          this.setUser(authResult.accessToken);
        }

        return resolve();
      });
    });
  },

  /**
   * Computed to tell if a user is logged in or not
   * @return boolean
   */
  isAuthenticated: computed(function() {    
    return this.get('checkLogin');
  }), 

  /**
   * Use the token to set our user
   */
  setUser(token) {
    // once we have a token, we are able to go get the users information
    this.get('auth0')
      .client
      .userInfo(token, (err, profile) => this.set('user', profile))
  },

  /**
   * Check if we are authenticated using the auth0 library's checkSession
   */
  checkLogin() {
    // check to see if a user is authenticated, we'll get a token back
    this.get('auth0')
      .checkSession({}, (err, authResult) => {
        // if we are wrong, stop everything now
        if (err) return err;
        this.setUser(authResult.accessToken);
      });
  }, 

  /**
   * Get rid of everything in sessionStorage that identifies this user
   */
  logout() {
    this.get('auth0').logout({
      clientID: config.auth0.clientId,
      returnTo: 'http://localhost:4200'
    });
  }
});
```

In the section that looks like this:

```javascript
  auth0: computed(function () {
    return new auth0.WebAuth({
      // setting up the config file will be covered below
      domain: config.auth0.domain, // domain from auth0
      clientID: config.auth0.clientId, // clientId from auth0
      redirectUri: 'http://localhost:4200/callback',
      audience: `https://${config.auth0.domain}/userinfo`,
      responseType: 'token',
      scope: 'openid profile' // adding profile because we want username, given_name, etc
    });
  }),
 ```

You will want to your own credentials from your Auth0 account. That tab you still have open, yeah, let's go back to that and grab that information. In a "secret" file, you will be inputting those values and then `.gitignore` that file.

What you can do is in the config folder (`/config`) within your project you can create an `auth0-variables.js` file that can contain your auth0 information. It can look something like this:

```javascript
module.exports = {
  clientId: 'id goes here',
  domain: 'domain goes here',
  callbackUrl: 'callback url goes here'
}
```

Then in the `config/environment.js` file, you can call for those variables. Like shown here: (towards the bottom of the file)

```javascript
//ember-js-auth/config/environment.js
const AUTH_CONFIG = require('./auth0-variables');

module.exports = function (environment) {
  var ENV = {
    modulePrefix: 'ember-js-auth',
    environment: environment,
    rootURL: '/',
    locationType: 'auto',
    EmberENV: {
      FEATURES: {
        // Here you can enable experimental features on an ember canary build
        // e.g. 'with-controller': true
        'ds-improved-ajax': true,
      },
      EXTEND_PROTOTYPES: {
        // Prevent Ember Data from overriding Date.parse.
        Date: false
      }
    },

    APP: {
      // Here you can pass flags/options to your application instance
      // when it is created
    }
  };

  if (environment === 'development') {
    // ENV.APP.LOG_RESOLVER = true;
    // ENV.APP.LOG_ACTIVE_GENERATION = true;
    // ENV.APP.LOG_TRANSITIONS = true;
    // ENV.APP.LOG_TRANSITIONS_INTERNAL = true;
    // ENV.APP.LOG_VIEW_LOOKUPS = true;
  }

  if (environment === 'test') {
    // Testem prefers this...
    ENV.locationType = 'none';

    // keep test console output quieter
    ENV.APP.LOG_ACTIVE_GENERATION = false;
    ENV.APP.LOG_VIEW_LOOKUPS = false;

    ENV.APP.rootElement = '#ember-testing';
  }

  if (environment === 'production') {

  }

  ENV.auth0 = {
    clientId: AUTH_CONFIG.clientId,
    domain: AUTH_CONFIG.domain,
    callbackUrl: AUTH_CONFIG.callbackUrl
  }

  return ENV;
};
```

You can declare these variable however you want, just make sure it is in a file that you `.gitignore` so that once you push it to GitHub, your secret keys will not be exposed to the world.

### Adding a Global Dependency

The [`auth0.js` library](https://github.com/auth0/auth0.js) needs to be added to a different file in the project. In your `ember-cli-build.js` you will add this line: 

```js
app.import('node_modules/auth0-js/build/auth0.js');
```

We will need to open our `ember-cli-build.js` file and add in the `app.import` line:

```javascript
//ember-js-auth/ember-cli-build.js
'use strict';

const EmberApp = require('ember-cli/lib/broccoli/ember-app');

module.exports = function(defaults) {
  let app = new EmberApp(defaults, {
    // Add options here
  });

  // This line is the line that needs to be added
  app.import('node_modules/auth0-js/build/auth0.js');

  return app.toTree();
};
```

> Do not be alarmed, I know this is not how React or many other frameworks do it. Why not import it in the top of the file? In Ember, some dependencies will need to be added to the build so that it can be used across the application. You can find the [Ember documentation for dependencies here](https://guides.emberjs.com/v3.1.0/addons-and-dependencies/managing-dependencies/).

To complete the global import, we will need to add the `auth0` logic as a global in our `eslintrc.js` file. Go to the `eslintrc.js` file and insert the couple of lines shown here in the file: 

```javascript
//ember-js-auth/eslintrc.js
module.exports = {
  root: true,
  parserOptions: {
    ecmaVersion: 2017,
    sourceType: 'module'
  },
  plugins: [
    'ember'
  ],
  extends: [
    'eslint:recommended',
    'plugin:ember/recommended'
  ],
  env: {
    browser: true
  },

  // These are the new lines we need to add. 
  globals: {
    'auth0': false
  },

  rules: {
  },
  overrides: [
    // node files
    {
      files: [
        'ember-cli-build.js',
        'testem.js',
        'config/**/*.js',
        'lib/*/index.js'
      ],
      parserOptions: {
        sourceType: 'script',
        ecmaVersion: 2015
      },
      env: {
        browser: false,
        node: true
      }
    }
  ]
};
```

## Each Route Needs to Be Declared

In our `app/router.js` file we need to declare what routes we are going to have. We have the standard '/' route and we will need to be able to tell the application what other routes we will be using. Go to your `app/router.js` file and ensure the information is as shown:

```javascript
//ember-js-auth/app/router.js
import EmberRouter from '@ember/routing/router';
import config from './config/environment';

const Router = EmberRouter.extend({
  location: config.locationType,
  rootURL: config.rootURL
});

Router.map(function() {
  // Ember will populate this information for you
  // When a route is created, it will automatically include it in this list
  this.route('dashboard');
  this.route('callback');
  this.route('home');
});

export default Router;
```

## The Application Route

We are going to need an all encompassing route. It is the route that is over the application as a whole. It is there to usually handle routing for the entire application but because we have declared routing elsewhere, this will not have that logic. However, we do need to keep it to ensure that the user stays logged in. This route will do so by utilizing `checkLogin`.

```bash
ember generate route application
```

It will send a message saying `Overwrite app/templates/application.hbs?`. That is something you will want to say yes to, so simply just type `y` and press enter when it asks again, `Yes, overwrite`.

In that newly generated file, you will see:

```javascript
//ember-js-auth/app/routes/application.js
import Route from '@ember/routing/route';
import { inject as service } from '@ember/service';

export default Route.extend({
  auth: service(),
  beforeModel() {
    this.auth.checkLogin();
  }
});
```

## Building a Functional Nav Bar

Our navbar will have our login button. Once the user has logged in, that login button will change to the user's name, dashboard button, and the logout button with a `Bank Home` button on the opposite side of the navbar. Let's look at our navbar code. We will need a couple of files generated in order to get it all working. Let's start with the component app-nav.

```bash
ember generate component app-nav
```

> Note! When naming components in Ember, you must have a `-` somewhere in the name. That is how Ember knows this file is a component. In the [Ember JS Docs](https://guides.emberjs.com/release/components/defining-a-component/) you will find that it prevents things like name duplication with HTML element names or helps Ember automatically know that it is a component. 

That command created the `component/app-nav.js` file and it also automatically created `templates/components/app-nav.hbs`. We will be using that file soon!

Within the file, let's add:

```javascript
//ember-js-auth/app/components/app-nav.js
import Component from '@ember/component';
import { inject as service } from '@ember/service';

export default Component.extend({
  router: service(),
  auth: service('auth'),
  actions: {
  
    /**
   * From service/auth, starting the login process
   */
    login() {
      this.get('auth').login();
    },

    goHome() {
      this.get('router').transitionTo('home');
    },

    goDashboard() {
      this.get('router').transitionTo('dashboard');
    },
    
    /**
   * From service/auth, removing the saved token from the session.
   */
    logout() {
      this
        .get('auth')
        .logout()  
    }
  }
});
```

We have nurmerous functions here that we will be using throughout our application. This will also help with the routing. Notice the `transitionTo` being called and holding a value? When that function is called, it will route the user to whichever route we have assigned it to. Now let’s dive into the other file that was generated, `templates/components/app-nav.hbs`. The code in there will look like this:

{% highlight html %}
{% raw %}
<!-- ember-js-auth/app/templates/components/app-nav.hbs -->
<nav class="navbar is-danger">
  <div class="navbar-brand">
    <a class="navbar-item" {{ action "goHome" }}>
      <p width="112" height="28">Bank Home</p>
    </a>
  </div>

  <div class="navbar-menu">
    <div class="navbar-start">
        
    </div>

    <div class="navbar-end">
      {{#if auth.user}}
        <a class="navbar-item">{{ auth.user.name }}</a>

        <a class="navbar-item" {{ action "goDashboard" }}>
          Dashboard
        </a>

        <button {{ action "logout" }} class="button is-primary">Log out</button>
      {{ else }}

      <p class="control">
        <a class="button is-primary" href="#" {{action "login"}}>
          Log In
        </a>
      </p>
      {{/if}}
    </div>
  </div>
</nav>
{% endraw %}
{% endhighlight %}

You will see that when the user first visits the page, the navbar displays a `Bank Home` title and a `Login` button. Once logged in, it changes to their name, `Dashboard` button, and `Logout` button, a clean toggle between them.

Now that we have the buttons ready for us, let's set up our authentication so those buttons know, "Hey, they are authenticated, let's let them pass to the dashboard!"

## Building Our All-Encompassing App

We will want to add another controller titled `application`. Imagine that the application controller is the parent component to the dashboard. It is wrapping itself around the entire application. While the user is in the dashboard, logged in, the application controller will ensure they keep that authenticated status.

```bash
ember generate controller application
```

In there the code should be:

```javascript
//ember-js-auth/app/controllers/application.js
import Controller from '@ember/controller';
import { inject as service } from '@ember/service';

export default Controller.extend({
  auth: service(),
  init() {
    this._super(...arguments);
  }
});
```

Next, to match up with the controller, the application template will have code that will show what the user needs to see.

Open up the `app/templates/application.hbs` file and add the following:

{% highlight html %}
{% raw %}
<!-- ember-js-auth/app/templates/application.hbs -->
{{app-nav user=user }}

<main>
  {{outlet}}
</main>
{% endraw %}
{% endhighlight %}

## The Home Route Setup

We have our navbar ready for us, but let's give those buttons a purpose. The button we will have on the left of the navbar will be our `Bank Home` button. This will take us to the home screen whether you are authenticated or not. We will need to add the `home` route, so on your command line type:

```bash
ember generate route home
```

This will create two files, the `routes/home` and the `templates/home`. Let's start first with the `routes/home`.

That file in particular will not change from the setup that Ember gave us, but it is necessary for the `templates` part of it. In that file you will see the following:

```javascript
//ember-js-auth/app/routes/home
import Route from '@ember/routing/route';

export default Route.extend({
});
```

Now onto the `templates/home` file. In there we will add in the following code: 

{% highlight html %}
{% raw %}
<!-- ember-js-auth/app/templates/home -->
{{outlet}}

<section class="hero is-info is-fullheight is-bold">
  <div class="hero-body">
    <div class="container has-text-centered">

      <h2 class="title">HELLO I AM HOME</h2>

    </div>
  </div>
</section>
{% endraw %}
{% endhighlight %}

We are telling the application that whenever the user is on the `home page`, to display just a simple header saying `HELLO I AM HOME` with some Bulma styling.

## Handling Authentication

We are going to help the application know what to do once the authentication process takes place. If we look at our `handleAuthentication` function in our auth services file, you'll see that we will take in the `accessToken` to then set the session. If these things are not passed through and we cannot properly handle the authentication, then this function being called will reject with an error. Here in our callback route, we will be using that function to allow for this to all take place.

Create the file:

```bash
ember generate route callback
```

The information inside that file should be:

> Note! The `beforeModel` stores information that we may need later on. If everything runs smoothly in this function, we can transition them to the `dashboard`.

```javascript
//ember-js-auth/app/routes/callback.js
import Route from '@ember/routing/route';
import { inject as service } from '@ember/service';
import { get } from '@ember/object';

export default Route.extend({
  auth: service('auth'),
  beforeModel() {
    // check if we are authenticated
    // parse the url hash that comes back from auth0
    // if authenticated on login, redirect to the dashboard
    get(this, 'auth')
      .handleAuthentication()
      .then(() => this.transitionTo('/dashboard'));
  },
});
```

## Adding an Authenticated Dashboard

The user, once authenticated, needs to be redirected to the dashboard. In our dashboard we will see the `login` button change to the user's name, the `logout` button, the `dashboard` button, and also a randomly-generated number that shows their faux bank account balance. We want that number secure, right?

Create a controller for the dashboard and also a template. 

```bash
ember generate controller dashboard
ember generate template dashboard
```

The controller will have the logic, the template will have the view of that logic for the user.

Your dashboard controller should look like so:

```javascript
//ember-js-auth/app/controllers/dashboard.js
import Controller from '@ember/controller';

export default Controller.extend({
  // bank: service(), // get fake data
  init() {
    this._super(...arguments);

    // banking data
    this.set('balance', this.bankBalance());
  },
  bankBalance() {
    // randomly generate bank account balance
    // were only doing this for demo purposes
    // normally you would get this from a service/api
    return "$" + Math.floor((Math.random() + 1) * 10000) + ".00";
  }
});
```

The template for the dashboard should be something like this:

{% highlight html %}
{% raw %}
<!-- ember-js-auth/app/templates/dashboard.hbs -->
<section class="hero is-medium is-primary is-bold is-fullheight has-text-centered">
  <div class="hero-body">
    <div class="container">
      <h1 class="title is-size-1">
        Your Bank Balance
      </h1>
      <h2 class="subtitle is-size-3">
        {{balance}}
      </h2>
    </div>
  </div>
</section>
{% endraw %}
{% endhighlight %}

Remember, the template is what the user is seeing. They will see the words "Your Bank Balance" and then the balance underneath it. You also see that we are using Bulma classes here to give it a simple styling. 

> Note! The stuff that happens on the dashboard can be whatever you want! We just want to make sure that the user is authenticated before they get to a protected page. In this example, it is a faux bank account balance with a randomly generated number.

We will need to also create a dashboard route. 

```bash
ember generate route dashboard
```

> Here you will get asked again, `Overwrite app/templates/dashboard.hbs?`. This time, say no, so type in `n` and press enter when asked, `No, skip`.

Inside the `routes/dashboard`, we will put the logic so that if they try and access the dashboard without being authenticated, it will send them back to the home screen with the login button. Can you imagine if the user was able to type in `yoururl.com/dashboard` and see the bank account balance without having to be authenticated first? In this `route/dashboard` file, the user is checked by the application. If the user is not authenticated, then they will transition to the base URL and see only the `Home Page`. This keeps things safe and sound.

The information in the `app/routes/dashboard.js` file will look like this:

```javascript
//ember-js-auth/app/routes/dashboard.js
import Route from '@ember/routing/route';
import { inject as service } from '@ember/service';

export default Route.extend({
  auth: service('auth'),
  beforeModel() {
    // this is where we check if a user is authenticated
    // if not authenticated, kick them to the home page
    if (!this.auth.isAuthenticated) {
      this.transitionTo('/');
    }
  }
});
```

Now the user is logged in and authenticated and will see a large, randomly generated number. You can of course change the size of that number so your user sees large or smaller numbers.

## The Banking Application

To see the application running, simply type into your command line:

```bash
ember serve
```

Now that it is running, let's use the application. The user will come to the site, and see this:

![Home page](https://i.imgur.com/OQMYdJH.png)

Once they click on `Log In`, they will then see the Auth0 login page.

<p align="center"><img src="https://i.imgur.com/1ErPfYG.png?1" alt="Auth0 sign in"></p>

If authentication goes through, they will be redirected to the dashboard showing them their profile name, a `Dashboard` button, a `Log Out` button, and a randomly generated bank account balance. Once they hit logout, they will be unauthenticated and be redirected back to the login screen.

![Dashboard Page](https://i.imgur.com/HVNwjbi.png)

## Conclusion 

This application tutorial was to get you started with authentication in EmberJS. You should now have a successfully built application. The magic that happens after the login can be whatever you want. It requires a lot of different files talking to each other in order for one thing to fire off. Once you start using it and building with it, you will start to see the flow clearly.

> Try visiting the site incognito and you will see there that you will not be able to visit the dashboard page without first being authenticated! Because remember, you cannot visit the dashboard view until you pass in your credentials. That page is a protected page. 

### Auth0

Using [Auth0](https://auth0.com) allows for worry-free authentication. You, as a developer, can rest assured that your users and your application are backed by a powerful authentication and identity solution.
