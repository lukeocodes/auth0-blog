---
layout: post
title: "Vue.js and AWS Lambda: Developing Production-Ready Apps (Part 1)"
description: "In this series, you will learn how to develop production-ready applications with Vue.js and AWS Lambda."
longdescription: "In this series, you will learn how to develop production-ready applications with Vue.js and AWS Lambda. Alongside with these technologies, you will use Express to define the endpoints that you will deploy on AWS Lambda and you will use MongoDB to persist data."
date: 2018-06-13 08:30
category: Technical Guide, Frontend, VueJS
author:
  name: "Bruno Krebs"
  url: "https://twitter.com/brunoskrebs"
  mail: "bruno.krebs@gmail.com"
  avatar: "https://cdn.auth0.com/blog/profile-picture/bruno-krebs.png"
design:
  bg_color: "#213040"
  image: https://cdn.auth0.com/blog/vue-js-and-lambda-developing-production-ready-apps/logo.png
tags:
- vue.js
- aws-lambda
- aws
- lambda
- spa
- auth0
related:
- 2018-07-03-vue-js-and-lambda-developing-production-ready-apps-part-2
- 2017-04-18-vuejs2-authentication-tutorial
---

**TL;DR:** In this series, you will use modern technologies like Vue.js, AWS Lambda, Express, MongoDB, and Auth0 to create a production-ready application that acts like a micro-blog engine. The first part of the series (this one) will focus on the setup of the Vue.js client that users will interact with and on the definition of the Express backend app.

[The second part will show you how to prepare your app for showtime](https://auth0.com/blog/vue-js-and-lambda-developing-production-ready-apps-part-2/). There, you will start by signing up to AWS and to MongoLabs (where you will deploy the production MongoDB instance), then you will focus on refactoring both your frontend and backend apps to support different environments (like development and production).

[You can find the final code developed in this part in this GitHub repository](https://github.com/auth0-blog/vue-js-lambda-part-1).

## Stack Overview

Before starting with the hands-on exercises, let's take a brief overview of each piece that will compose your micro-blog engine.

### Vue.js

[As stated by the official guide, Vue.js is a progressive framework for building user interfaces](https://vuejs.org/v2/guide/). One of its focuses is on enabling developers to incrementally adopt the framework. That is, instead of demanding that developers use it to structure the whole application, Vue.js allows them to use it on specific parts to enhance the user experience on legacy apps. With that said, the guide also states that Vue.js is "perfectly capable of powering sophisticated Single-Page Applications (SPAs) when used in combination with modern tooling (like Webpack) and supporting libraries".

In this series, you will have the opportunity to see this great framework in action to create a SPA from the ground up.

### AWS Lambda

[AWS Lambda](https://aws.amazon.com/lambda/) is a serverless computer platform, provided by Amazon, that allows developers to run their code without having to spend too much time thinking about the servers needed to run it. Although the main idea of using a serverless platform is to facilitate the deployment process and the scalability of applications, AWS Lambda is not easy for newcomers. In fact, AWS Lambda on its own is not enough to run Rest APIs like the one you will need for your micro-blog engine. Besides this AWS service, you will also need to use [AWS API Gateway](https://aws.amazon.com/api-gateway/) to define how external services (or, in this case, a Vue.js client) can communicate with your serverless backend app. This last piece is exactly what makes AWS Lambda not straightforward.

So, to avoid wasting your time with the intricacies of AWS API Gateway and AWS Lambda, you will take advantage of an open-source tool called [Claudia.js](https://claudiajs.com/). The goal of this tool is to enable you to deploy your Node.js projects to AWS Lambda and API Gateway easily.

### Express

Express is the most popular framework for developing web applications on the Node.js landscape. By using it, you will have access to tons of (and great) documentation, a huge community, and a lot of middleware that will help you achieve your goals. In this series, for example, you will use at least four popular middleware to define your backend endpoints: `bodyParser`, `cors`, `helmet`, and `morgan`.

If you haven't heard about them, you will find a brief explanation of what they are capable of doing and you will see that using them is pretty straightforward. You will also note that defining endpoints and communicating with databases (in this case, MongoDB) can't be easier.

### MongoDB

MongoDB is a popular NoSQL database that treats data as documents. That is, instead of the classic approach of defining data as tables and rows that relate to each other, MongoDB allows developers to persist and query complex data structures. As you will see, using MongoDB to persist JSON data sent by clients and processed by Node.js (or Express in this case) is really simple.

### Auth0

Handling identity on modern applications is not easy. For starter, if the developers choose to homegrown their own solution they will have to create everything from the sign-up page, passing through the recovering password feature, till the handling of sessions and access tokens. Not to say that if they want to integrate with social networks like Facebook or Google, or if they want to allow users from companies that rely on Active Directory or SAML, they will face a scenario that is way more complex.

To avoid all these challenges and to enable you to focus on what matters the most to your application (its special features) you will take advantage of Auth0. With Auth0, a global leader in Identity-as-a-Service (IDaaS), you only have to write a few lines of code to get [solid identity management solution](https://auth0.com/user-management), [single sign-on](https://auth0.com/docs/sso/single-sign-on), support for [social identity providers (like Facebook, GitHub, Twitter, etc.)](https://auth0.com/docs/identityproviders), and support for [enterprise identity providers (Active Directory, LDAP, SAML, custom, etc.)](https://auth0.com/enterprise).

## What You Will Build

Throughout this series, you will use the stack described above (mainly Vue.js, Lambda, Express, and MongoDB) to create a micro-blog that contains a single public channel of micro-posts. That is, visitors (unauthenticated users) will be able to see all micro-posts and registered users will be able to express their minds publicly.

Although simple, this application will enable you to learn how to create secure, modern, and production-ready applications with Vue.js and AWS Lambda.

Now, regarding this specific part of the series, you will achieve the following objectives:

1. You will bootstrap your Vue.js application with `vue-cli`.
2. You will create your backend app with Express.
3. You will initialise a MongoDB instance and use it in your Express app.
4. You will enable identity management in both your Vue.js application and your Express backend with the help of Auth0.

So, without further ado, it's time to start developing!

## Vue.js, Express, and Mongo: Hands-On!

So, now that you know the stack that you will use and that you know what you will build, it's time to start creating your app. To keep things organised, you will create a directory to keep both your frontend and backend source code:

```bash
# create the root directory
mkdir vuejs-lambda

# move into it
cd vuejs-lambda
```

Then, as a responsible developer, you will make this directory a Git repository so you can keep your code safe and sound:

```bash
# initialize Git in the directory
git init
```

For now, there is nothing to save in your Git repository but, if you want to save some keystrokes in the future, you can define a Git alias to add and commit files:

```bash
git config --global alias.cm '!git add -A && git commit -m'
```

With this alias in place, you can just issue `git cm 'some message'` when you want to save your work.

### Bootstrapping Vue.js

To create your Vue.js application based on best practices, you will take advantage of [the `vue-cli` tool](https://github.com/vuejs/vue-cli/tree/master). So, before using this command line interface, you will have to install it on your machine:

```bash
npm install -g vue-cli
```

Then, in the root directory of your project, you will run the following command:

```bash
vue init webpack client
```

This command will make `vue-cli` ask a bunch of questions. You can answer them as follows:

- `Project name:` vuejs-micro-blog-engine
- `Project description:` A Vue.js Micro-Blog Engine
- `Author:` Your Name
- `Vue build:` Runtime + Compiler: recommended for most users
- `Install vue-router?` Yes
- `Use ESLint to lint your code?` Yes
- `Pick an ESLint preset:` Standard
- `Set up unit tests:` No
- `Setup e2e tests with Nightwatch?` No
- `Run npm install?` Yes, use NPM

> **Note:** If you are an advanced user, you can tweak the answers at will. For example, you can choose another [ESLint preset like Airbnb](https://www.npmjs.com/package/eslint-config-airbnb) or [choose to use Yarn](https://yarnpkg.com/en/) instead of NPM. However, keep in mind that if you change answers, the coding style shown here might not be the same as your project expects or that you will have to change a few of the commands shown.

After answering the questions appropriately, you can issue the following commands to check if your frontend Vue.js client is really working:

```bash
# move dir into your new client
cd client

# run the development server
npm run dev
```

The last command will run the development server on [`http://localhost:8080`](http://localhost:8080). So, if you open a browser and head to this address, you will see the following screen:

![Vue.js up and running.](https://cdn.auth0.com/blog/vuejs-lambda/vuejs-up-and-running.png)

Good, you can now stop your server by hitting `Ctrl + C` (or `⌘ + C` on a Mac) and save your work:

```bash
git cm 'bootstrapping the Vue.js client'
```

### Creating an Express Project

Now that you have your Vue.js frontend application skeleton defined, you can focus on scaffolding your backend application. For that, you will create a new directory called `backend` and install some dependencies:

```bash
# move back to the project root
cd ..

# create the backend dir
mkdir backend

# move into the backend directory
cd backend

# initialise NPM
npm init -y

# install dependencies
npm i express body-parser cors mongodb morgan helmet
```

So, as you can see, the commands above initialised NPM in the `backend` directory and installed six NPM packages:

- [`express`](https://github.com/expressjs/express): the most popular Node.js web application framework;
- [`body-parser`](https://github.com/expressjs/body-parser): an Express middleware that parses request bodies so you can access JSON objects sent by clients;
- [`cors`](https://github.com/expressjs/cors): an Express middleware to make your endpoint accept cross-origin requests;
- [`mongodb`](https://github.com/mongodb/node-mongodb-native): the MongoDB native driver for Node.js;
- [`morgan`](https://github.com/expressjs/morgan): an HTTP request logger middleware for Node.js web apps;
- and [`helmet`](https://github.com/helmetjs/helmet): an Express middleware that helps to secure your apps with various HTTP headers.

> **Note:** If you want more information, you can check the official documentation of each package on the links above.

After installing all dependencies, you can create a new directory to hold the JavaScript source code and define the `index.js` file inside it:

```bash
# define a directory to hold the javascript code
mkdir src

# create the index.js file
touch src/index.js
```

Then, in this new file, you can add the following code:

```javascript
//import dependencies
const express = require('express');
const bodyParser = require('body-parser');
const cors = require('cors');
const helmet = require('helmet');
const morgan = require('morgan');

// define the Express app
const app = express();

// enhance your app security with Helmet
app.use(helmet());

// use bodyParser to parse application/json content-type
app.use(bodyParser.json());

// enable all CORS requests
app.use(cors());

// log HTTP requests
app.use(morgan('combined'));

// start the server
app.listen(8081, () => {
  console.log('listening on port 8081');
});
```

As you can see, the code above is quite simple. From top to bottom, it:

1. starts by importing most of the packages that you installed before (the only one missing is `mongodb` because you will use it elsewhere);
2. then, it defines a new Express web application;
3. after that, it configures `helmet`, `bodyParser`, `cors`, and `morgan` middleware;
4. and, finally, it triggers the server on port `8081`.

Right now, there is no reason to start your Express application. Why? Because there are no endpoints defined on it and because you have no MongoDB instance to communicate with.

In the next section, you will initialise a MongoDB instance and then create a few endpoints to communicate with it. However, before proceeding into it, you will be better off creating two new files (in the project root) to help you in your development process.

The first one is a `.gitignore` file to keep you from committing useless files into your Git repository. So, create this file in the project root directory and [copy the contents of this file into it](https://raw.githubusercontent.com/auth0-blog/vue-js-lambda-part-1/master/.gitignore). Or, if you prefer, you can also create and populate this file quite easily with the following commands:

```bash
# move to the project root
cd ..

# create and populate .gitignore
curl https://raw.githubusercontent.com/auth0-blog/vue-js-lambda-part-1/master/.gitignore >> .gitignore
```

After that, you can create the second file. This one, called `.editorconfig` will help you keep your indentation style consistent. Again, [you can copy and paste the contents from the internet](https://raw.githubusercontent.com/auth0-blog/vue-js-lambda-part-1/master/.editorconfig), or you can use the following command:

```bash
curl https://raw.githubusercontent.com/auth0-blog/vue-js-lambda-part-1/master/.editorconfig >> .editorconfig
```

Great! Now, you are ready to save your progress and move to the next section:

```bash
git cm 'Scaffolding the Express web application.'
```

### Preparing a MongoDB Instance

After defining the basic structure of both your backend and frontend applications, you will need to initialise a MongoDB instance to persist your users' data. There are many options to create a new MongoDB instance. For example:

1. You can install MongoDB on your development machine, but this would make the process of upgrading to newer versions harder.
2. You can use a MongoDB service like [MLab](https://mlab.com/), but this is a little extreme and slow for the development machine.
3. You can use Docker to initialise a container with a MongoDB instance.

Although the last alternative requires you to have Docker installed in your development machine, this is the best option for the development phase because it allows you to have multiple MongoDB instances at the same time in the same machine. So, if you don't have Docker already installed locally, head to [the Docker Community download page](https://www.docker.com/community-edition) and follow the instructions for your OS.

> **Note:** In the next article, you will create an MLab account to use a reliable and globally available instance of MongoDB. Don't worry, as any other service used in this series, MLab provides a free tier that is more than enough for this series.

After installing it, you can trigger a new MongoDB instance with the following command:

```bash
docker run --name mongo \
    -p 27017:27017 \
    -d mongo
```

Yup, that's it. It's easy like that to initialise a new MongoDB instance in a Docker container. For more information about it, you can check the instructions on [the official Docker image for MongoDB](https://hub.docker.com/_/mongo/).

## Integrating Vue.js, Express, and MongoDB

You now have all the basic building blocks in place and ready to be used. So, it's time to tie them up to see the stack in action.

### Consuming MongoDB Collections with Express

For starters, you will create a new file called `routes.js` in the `backend/src` directory and add the following code to it:

```js
const express = require('express');
const MongoClient = require('mongodb').MongoClient;

const router = express.Router();

// retrieve latest micro-posts
router.get('/', async (req, res) => {
  const collection = await loadMicroPostsCollection();
  res.send(
    await collection.find({}).toArray()
  );
});

// insert a new micro-post
router.post('/', async (req, res) => {
  const collection = await loadMicroPostsCollection();
  await collection.insertOne({
    text: req.body.text,
    createdAt: new Date(),
  });
  res.status(200).send();
});

async function loadMicroPostsCollection() {
  const client = await MongoClient.connect('mongodb://localhost:27017/');
  return client.db('micro-blog').collection('micro-posts');
}

module.exports = router;
```

As you can see, the code necessary to integrate your Express application with a MongoDB instance is extremely simple (for this basic app of course). In less than 30 lines, you defined two routes for your app: one for sending micro-posts to users and one to persist new micro-posts in the database.

Now, to use your new routes, you will have to update the `index.js` file (the one that resides in the `backend/src` directory) as follows:

```js
// ... other require statements
const routes = require('./routes');

// express app definition and middleware config

app.use('/micro-posts', routes);

app.listen(8081, () => {
  console.log('listening on port 8081');
});
```

Again, pretty straightforward, isn't it? You just had to `require` the new file and make your Express app aware of the routes defined on it through the `use` function.

With these changes in place, you can test your backend with the following commands (or with any HTTP client for that matter):

```bash
# move cursor to the backend directory
cd backend

# start up your Express app
node src

# fetches the micro-posts (for now, an empty array)
curl 0:8081/micro-posts

# add the first micro-post
curl -X POST -H 'Content-Type: application/json' -d '{
  "text": "I love coding"
}' 0:8081/micro-posts

# fetches the micro-posts again
curl 0:8081/micro-posts
```

As you now have your Express app integrated with a MongoDB instance, it's time to save your progress:

```bash
git cm 'integrating Express and Mongo'
```

### Consuming Express Endpoints with Vue.js

Now, you can focus on upgrading your Vue.js app to communicate with these two new endpoints. So, first, you will need a new service that will interface this communication. To define this service, create a new file called `MicroPostsService.js` inside the `./client/src/` directory and add the following code to it:

```javascript
import axios from 'axios'

const url = 'http://localhost:8081/micro-posts/'

class MicroPostsService {
  static getMicroPosts () {
    return new Promise(async (resolve, reject) => {
      try {
        const serverResponse = await axios.get(url)
        const unparsedData = serverResponse.data
        resolve(unparsedData.map(microPost => ({
          ...microPost,
          createdAt: new Date(microPost.createdAt)
        })))
      } catch (error) {
        reject(error)
      }
    })
  }

  static insertMicroPost (text) {
    return axios.post(url, {
      text
    })
  }
}

export default MicroPostsService
```

> **Note:** Whenever you get an answer back from the `GET` endpoint, you iterate over the micro-posts returned to transform the stringified version of the `createdAt` property into real JavaScript `Date` objects. You are doing it so you can manipulate this property more easily.

As you can see, this service depends on [Axios](https://github.com/axios/axios), a promise based HTTP client for JavaScript applications. So, to install Axios, issue the following command from the `./client` directory:

```bash
# using npm to install axios
npm i axios
```

After installing this library, you can wrap your head around the `HelloWorld` component (this is the first component your users will see when they access your application). In this component, you will use the newly created service to show micro-posts to all users. So, open the `HelloWorld.vue` file and replace the contents of the `<script>` tag with the following:

```js
import MicroPostService from '../MicroPostsService'

export default {
  name: 'HelloWorld',
  data () {
    return {
      microPosts: [],
      error: ''
    }
  },
  async created () {
    try {
      this.microPosts = await MicroPostService.getMicroPosts()
    } catch (error) {
      this.error = error.message
    }
  }
}
```

In the new version of this code, you are using the `created` lifecycle hook to fetch micro-posts from the backend (through the `MicroPostService.getMicroPosts` function). Then, when the request is fulfilled, you are populating the `microPosts` property so you can render it on the screen. You are also checking for any error (with `try ... catch`) and populating the `error` property if anything goes wrong.

Now, to use these two properties (`microPosts` and `error`), you can replace the contents of the `<template>` tag with the following:

{% highlight html %}
{% raw %}
<div class="container">
  <h1>Latest Micro-Posts</h1>
  <p class="error" v-if="error">{{ error }}</p>
  <div class="micro-posts-container">
    <div class="micro-post"
         v-for="(microPost, index) in microPosts"
         v-bind:item="microPost"
         v-bind:index="index"
         v-bind:key="microPost.id">
      <div class="created-at">
        {{ `${microPost.createdAt.getDate()}/${microPost.createdAt.getMonth() + 1}/${microPost.createdAt.getFullYear()}` }}
      </div>
      <p class="text">{{ microPost.text }}</p>
      <p class="author">- Unknown</p>
    </div>
  </div>
</div>
{% endraw %}
{% endhighlight %}

This will make the component render the `error` if anything goes wrong (i.e. you are relying on a conditional rendering: `v-if="error"`) or render the `microPosts` returned by the backend. For each micro-post (`v-for="(microPost, index) in microPosts"`), you are telling the component to render the date that it was created (inside the `div.created-at` element), the `text` inputted by the user (inside the `p.text` element), and the author's name (unknown for now).

Lastly, to make your application look a little bit better, you can replace the contents of the `<style>` tag with the following:

```css
div.container {
  max-width: 800px;
  margin: 0 auto;
}

p.error {
  border: 1px solid #ff5b5f;
  background-color: #ffc5c1;
  padding: 10px;
  margin-bottom: 15px;
}

div.micro-post {
  position: relative;
  border: 1px solid #5bd658;
  background-color: #bcffb8;
  padding: 10px;
  margin-bottom: 15px;
}

div.created-at {
  position: absolute;
  top: 0;
  left: 0;
  padding: 5px 15px 5px 15px;
  background-color: darkgreen;
  color: white;
  font-size: 13px;
}

p.text {
  font-size: 22px;
  font-weight: 700;
  margin-bottom: 0;
}

p.author {
  font-style: italic;
  margin-top: 5px;
}
```

Now, to check if everything is working as expected, you can run the following commands:

```bash
# move to the backend directory
cd ../backend/

# run the backend in the background
node src &

# move back to the Vue.js client directory
cd ../client/

# start the development server
npm run dev
```

After running the development server, head to [`http://localhost:8080`](http://localhost:8080) to check the new version of your application:

![Vue.js application integrated with Express and MongoDB](https://cdn.auth0.com/blog/vuejs-lambda/vuejs-integrated-with-express-and-mongo.png)

As a reminder, if you want to create new micro-posts, you will have to use some HTTP client like `curl` for now:

```bash
curl -X POST -H 'Content-Type: application/json' -d '{
  "text": "I want pizza"
}' 0:8081/micro-posts
```

Ok, time to save your progress:

```bash
git cm 'Integrating Vue.js, Express, and MongoDB.'
```

## Handling Authentication with Auth0

Awesome, you have all the main building blocks of your app (Vue.js, Express, and MongoDB) integrated and communicating properly. Now, it's time to focus on adding a modern identity management tool to your app so you can identify who is accessing it and to let users publish their own micro-posts.

So, before getting into the details on how to integrate Auth0 in your Vue.js app and in your Express backend, you will need to create a new Auth0 account. If you don't have one already, now it's a good time to <a href="https://auth0.com/signup" data-amp-replace="CLIENT_ID" data-amp-addparams="anonId=CLIENT_ID(cid-scope-cookie-fallback-name)">sign up for a free Auth0 account</a>.

{% include tweet_quote.html quote_text="Check out how to add modern identity management to Vue.js apps." %}

### Integrating Auth0 and Your Vue.js App

After signing up for your free Auth0 account, you will need to create a representation of your Vue.js app on it. So, [head to the Applications page inside the Auth0 management dashboard](https://manage.auth0.com/#/applications) and click on _Create Application_. Clicking on it will bring a small form that will ask you two things:

1. The name of your application: you can enter something like "Vue.js Micro-Blog".
2. The type of your application: here you will have to choose _Single Page Web Applications_.

![Creating a Vue.js application on Auth0](https://cdn.auth0.com/blog/vuejs-lambda/create-vuejs-app-on-auth0.png)

After filling in the form, you can click on the _Create_ button. This will redirect you to a page where you can see tabs like _Quick Start_, _Settings_, and _Connections_. To proceed, click on the _Settings_ tab.

In this new page, you will see a form where you can tweak your application configuration. For now, you are only interested in adding values to two fields:

- `Allowed Callback URLs`: Here you will need to add `http://localhost:8080/callback` so that Auth0 know it can redirect users to this URL after the authentication process.
- `Allowed Logout URLs`: The same idea but for the logout process. So, add `http://localhost:8080/` in this field.

After inserting these values into these fields, hit the _Save Changes_ button at the bottom of the page.

You can now move back to your code, but don't close the _Settings_ page just yet, you will need to copy some info from it soon.

Back in your code, the first thing you will do is to install a package called [`auth0-web`](https://github.com/auth0-blog/auth0-web) in your Vue.js application:

```bash
# make sure you are in the client directory
cd ./client/

# install auth0-web
npm i auth0-web@1.7.0
```

With this package in place, you will create a new component (which you will call `Callback`) to handle the authentication response from Auth0. So, create a new file called `Callback.vue` inside the `./client/src/components/` directory and define its `<script>` section as follows:

{% highlight html %}
{% raw %}
<script>
import * as Auth0 from 'auth0-web'

export default {
  name: 'Callback',
  created () {
    Auth0.handleAuthCallback(() => {
      this.$router.push('/')
    })
  }
}
</script>
{% endraw %}
{% endhighlight %}

This is configuring the component to parse the hash (through the `Auth0.handleAuthCallback` function) returned by Auth0 to fetch the `access_token` and the `id_token` of the users that are authenticating. After handling the authentication response, this component will forward your users to the `HelloWorld` component again (`this.$router.push('/')`).

Before refactoring the `HelloWorld` component to make it aware of the authentication status, you can add a nice message and a nice style to your `Callback` component. To do this, add the following code after the `<script>...</script>` tag:

{% highlight html %}
{% raw %}
<template>
<div>
  <p>Loading your profile...</p>
</div>
</template>

<style scoped>
div > p {
  margin: 0 auto;
  text-align: center;
  font-size: 22px;
}
</style>
{% endraw %}
{% endhighlight %}

Then, after creating the `Callback` component, you will add buttons to your app so users can log in and log out. But first, you have to configure the `auth0-web` package with your Auth0 details. So, open the `App.vue` file (you can find it in the `./client/src/` directory) and you will replace the `<script>...</script>` tag with the following:

{% highlight html %}
{% raw %}
<script>
import * as Auth0 from 'auth0-web'

export default {
  name: 'App',
  created () {
    Auth0.configure({
      domain: '<YOUR-AUTH0-DOMAIN>',
      clientID: '<AN-AUTH0-CLIENT-ID>',
      audience: '<AN-AUTH0-AUDIENCE>',
      redirectUri: 'http://localhost:8080/#/callback',
      responseType: 'token id_token',
      scope: 'openid profile'
    })
  }
}
</script>
{% endraw %}
{% endhighlight %}

As you can imagine, you will have to replace the `<YOUR-AUTH0-DOMAIN>`, `<AN-AUTH0-CLIENT-ID>`, and `<AN-AUTH0-AUDIENCE>` placeholders with details from your Auth0 account. So, back in [the Auth0 management dashboard](https://manage.auth0.com/#/applications) (hopefully, you have left it open), you can copy the value from the _Domain_ field (e.g. `bk-tmp.auth0.com`) and use it to replace `<YOUR-AUTH0-DOMAIN>` and copy the value from the _Client ID_ field (e.g. `KsX...GPy`) and use it to replace `<AN-AUTH0-CLIENT-ID>`.

After that, you will have only one placeholder left to replace: `<AN-AUTH0-AUDIENCE>`. Although this placeholder refers to a value that you will define in the next section (while creating an Auth0 API for your endpoints), you can go ahead and replace this placeholder with the following value: `https://micro-blog-app`. Don't worry about this value being an URL that you do not own, Auth0 will never call this URL, it is just a value to represent your backend.

After configuring the `auth0-web` package in the `App` component, you can open the `HelloWorld` component (its file resides in the `./client/src/components/` directory) and add refactor the `<template></template>` section as follows:

{% highlight html %}
{% raw %}
<template>
<div class="container">
  <h1>Latest Micro-Posts</h1>
  <div class="users">
    <button v-if="!profile" v-on:click="signIn">
      Sign In
    </button>
    <button v-if="profile" v-on:click="signOut">
      Sign Out
    </button>
    <p v-if="profile">
      Hello there, {{ profile.name }}. Why don't you
      <router-link :to="{ name: 'ShareThoughts' }">
        share your thoughts?
      </router-link>
    </p>
  </div>
  <!-- ... p.error and div.micro-posts-container stay untouched ... -->
</div>
</template>
{% endraw %}
{% endhighlight %}

The new version of the template adds buttons to allow users to sign in and sign out and an area that shows their names alongside with a link to `ShareThoughts`. You will create a new component to handle this route in a bit but, before that, you still have to refactor the `<script>` area of this component to support the new features:

{% highlight html %}
{% raw %}
<script>
import * as Auth0 from 'auth0-web'
import MicroPostService from '../MicroPostsService'

export default {
  name: 'HelloWorld',
  data () {
    return {
      microPosts: [],
      error: '',
      profile: null
    }
  },
  async created () {
    try {
      this.microPosts = await MicroPostService.getMicroPosts()
      Auth0.subscribe(() => {
        this.profile = Auth0.getProfile()
      })
    } catch (error) {
      this.error = error.message
    }
  },
  methods: {
    signIn: Auth0.signIn,
    signOut () {
      Auth0.signOut({
        clientID: '<AN-AUTH0-CLIENT-ID>',
        returnTo: 'http://localhost:8080/'
      })
    }
  }
}
</script>
{% endraw %}
{% endhighlight %}

> **Note:** You must replace `<AN-AUTH0-CLIENT-ID>` with the _Client ID_ property of the Auth0 Application that you created before.

As you can see, now you have a new property called `profile` on the component and you have defined two methods to handle the two buttons added to the template: `signIn` and `signOut`. These methods are just wrappers around the methods provided by the `auth0-web` package.

Also, you made this component subscribe as a listener (by using the `Auth0.subscribe` function) to the authentication status. As such, when authenticated users open your app, your component will load their profile so it can show their name. Nice, right?

The last missing piece to have a Vue.js application properly integrated with Auth0 is to register the `Callback` component as the responsible for the `/callback` route. To achieve this, open the `index.js` file that resides in the `./client/src/router/` directory and replace its contents with this:

```javascript
import Vue from 'vue'
import Router from 'vue-router'
import HelloWorld from '@/components/HelloWorld'
import Callback from '@/components/Callback'

Vue.use(Router)

export default new Router({
  mode: 'history',
  routes: [
    {
      path: '/',
      name: 'HelloWorld',
      component: HelloWorld
    },
    {
      path: '/callback',
      name: 'Callback',
      component: Callback
    }
  ]
})
```

> **Note** The `mode: 'history'` property is needed because, without it, your Vue.js app won't be able to properly handle the hash present in the callback URL.

If you test your application now, you will see that it is capable of authenticating users through Auth0 and of showing users' name.

![Vue.js app integrated with Auth0](https://cdn.auth0.com/blog/vuejs-lambda/vuejs-app-integrated-with-auth0.png)

However, you still haven't created the route where users will be able to express their mind. You can focus on this task now, but not before saving your progress:

```bash
git cm 'integrating the Vue.js client with Auth0'
```

As you will see, after integrating Auth0 into your app, you will be able to easily add secured areas in your Vue.js app.

For that, the first thing you will do is to create a new file called `ShareThoughts.vue` inside the `./client/src/components/` directory. In this file, you will add the following `<script>` tag:

{% highlight html %}
{% raw %}
<script>
import MicroPostsService from '../MicroPostsService'

export default {
  name: 'ShareThoughts',
  data () {
    return {
      text: ''
    }
  },
  methods: {
    async shareThouths () {
      await MicroPostsService.insertMicroPost(this.text)
      this.$router.push('/')
    }
  }
}
</script>
{% endraw %}
{% endhighlight %}

As you can see, the idea of this component is to let authenticated users to share their thoughts through the `shareThouths` method. This method simply delegates the communication process with the backend to the `insertMicroPost` function of the `MicroPostsService` that you defined before. After this service finishes inserting the new micro-post in your backend, the `ShareThoughts` component forwards users to the public page where they can see all micro-posts (including what they just shared).

To make this new component available in your app, you need to register it under your routes. So, open the `./client/src/router/index.js` file add update it as follows:

```js
// ... other import statements ...
import ShareThoughts from '@/components/ShareThoughts'
import * as Auth0 from 'auth0-web'

Vue.use(Router)

export default new Router({
  mode: 'history',
  routes: [
    // ... other routes ...
    {
      path: '/share-your-thoughts',
      name: 'ShareThoughts',
      component: ShareThoughts,
      beforeEnter: (to, from, next) => {
        if (Auth0.isAuthenticated()) {
          return next()
        }
        this.$router.push('/')
      }
    }
  ]
})
```

If you run both your frontend app (by running `npm run dev` from the `client` directory) and your backend app (by running `node src` from the `backend` directory), you will be able to authenticate yourself and navigate to `http://localhost:8080/share-your-thoughts` to share some thoughts.

![Vue.js route secured with Auth0](https://cdn.auth0.com/blog/vuejs-lambda/vuejs-secured-area.png)

**Note:** If you don't authenticate yourself before trying to access this route, you will get redirected to the main route. This happens because you defined a guard condition on the `beforeEnter` hook that calls `this.$router.push('/')` for unauthenticated users. 

Ok, then. From the frontend perspective, you have enough features. So, now it's time to focus on refactoring your backend application to integrate it with Auth0. But, as always, not before saving your progress:

```bash
git cm 'adding a secured route to Vue.js'
```

### Securing Your Express App with Auth0

As you have finished integrating your frontend application with Auth0, the only missing part now is adding Auth0 to your backend. For starters, you will need three to install new packages in your backend project:

```bash
# make sure you are in the backend directory
cd ./backend

# install dependencies
npm i express-jwt jwks-rsa auth0
```

Together, these dependencies will allow you to validate `access_tokens` sent by clients and will allow your backend app to get the profile of your users (like name and picture). If you need more info about these packages, you can check the following resources: [`express-jwt`](https://github.com/auth0/express-jwt), [`jwks-rsa`](https://github.com/auth0/node-jwks-rsa), and [`auth0`](http://auth0.github.io/node-auth0/).

Now, to use these packages, you will have to update only a single file: `./backend/src/routes.js`. Inside this file, update the code as follows:

```js
// ... other import statements ...
const auth0 = require('auth0');
const jwt = require('express-jwt');
const jwksRsa = require('jwks-rsa');

// ... router definition and router.get('/', ...) ...

// this is a middleware to validate access_tokens
const checkJwt = jwt({
  secret: jwksRsa.expressJwtSecret({
    cache: true,
    rateLimit: true,
    jwksRequestsPerMinute: 5,
    jwksUri: `https://<YOUR-AUTH0-DOMAIN>/.well-known/jwks.json`
  }),

  // Validate the audience and the issuer.
  audience: '<AN_AUTH0_AUDIENCE>',
  issuer: `https://<YOUR-AUTH0-DOMAIN>/`,
  algorithms: ['RS256']
});

// insert a new micro-post with user details
router.post('/', checkJwt, async (req, res) => {
  const collection = await loadMicroPostsCollection();

  const token = req.headers.authorization
    .replace('bearer ', '')
    .replace('Bearer ', '');

  const authClient = new auth0.AuthenticationClient({
    domain: '<YOUR-AUTH0-DOMAIN>',
    clientId: 'KsXpF1d1L0vYdwiBG10VNOjw1lJmBGPy',
  });

  authClient.getProfile(token, async (err, userInfo) => {
    if (err) {
      return res.status(500).send(err);
    }

    await collection.insertOne({
      text: req.body.text,
      createdAt: new Date(),
      author: {
        sub: userInfo.sub,
        name: userInfo.name,
        picture: userInfo.picture,
      },
    });

    res.status(200).send();
  });
});

// ... loadMicroPostsCollection and module.exports ...
```

As you can see, this new version simply defines a middleware called `checkJwt` to validate any `access_token` sent on request headers. Then, it uses the new middleware to secure the function responsible for handling `POST` HTTP requests (`router.post('/', checkJwt, async (req, res) => { ... });`). After that, inside this function, you are fetching the `access_token` from `req.headers.authorization` so you can use it to get profile details about the user calling your API (for this, you are using `auth0.AuthenticationClient` and `authClient.getProfile`). Lastly, when your backend finishes fetching users' profiles, it uses them to add more info into micro-posts saved in your MongoDB instance.

> **Note:** You have to replace the three occurrences of `<YOUR-AUTH0-DOMAIN>` with your own Auth0 domain and `<AN_AUTH0_AUDIENCE>` with the audience of an Auth0 API. You will create this API now.

To create an [Auth0 API](https://auth0.com/docs/api/info) to represent your backend, [head to the APIs page in your Auth0 management dashboard](https://manage.auth0.com/#/apis) and hit the _Create API_ button. This will bring up a form where you will set a name for your API (e.g. _Micro-Blog API_), an identifier which is also known as audience (in this case you can use `https://micro-blog-app`), and a signing algorithm (you can leave it as `RS256`). Then, after filling in this form, you can click on the _Create_ button and that's it.

![Creating a representation of your Express API on Auth0](https://cdn.auth0.com/blog/vuejs-lambda/create-express-api-on-auth0.png)

Back to the code, you will need to perform two changes in your Vue.js app. First, you will need to update the `MicroPostsService.js` file (which you can find in the `./client/src/` directory) so it sends users' `access_tokens` when submitting new micro-posts:

```js
// ... import axios ...
import * as Auth0 from 'auth0-web'

// ... const url ...

class MicroPostsService {
  // ... static getMicroPosts () ...

  static insertMicroPost (text) {
    return axios.post(url, {
      text
    }, {
      headers: { 'Authorization': `Bearer ${Auth0.getAccessToken()}` }
    })
  }
}

// ... export default MicroPostsService ...
```

Then, you can update the `<template>` tag inside the `HelloWorld.vue` file as follows:

{% highlight html %}
{% raw %}
<template>
<div class="container">
  <!-- ... leave h1, div.users, and p.error untouched ... -->
  <div class="micro-posts-container">
    <div class="micro-post" ...>
      <!-- and simply replace p.author with this: -->
      <p class="author">- {{ microPost.author.name || 'Unknown' }}</p>
    </div>
  </div>
</div>
</template>
{% endraw %}
{% endhighlight %}

Now, for any new micro-post submitted by an authorized user, your Vue.js app with show their names. To see this in action, issue the following commands:

```bash
# go to the backend directory
cd ./backend

# leave Express running in the background
node src &

# go to the client directory
cd ../client

# run the local development server
npm run dev
```

Then, head to [`http://localhost:8080`](http://localhost:8080). Now, after authenticating yourself, you can share a new thought and your name will appear on the micro-post:

![Vue.js and Express apps integrated with Auth0](https://cdn.auth0.com/blog/vuejs-lambda/vuejs-and-express-apps-integrated-with-auth0.png)

That's it, you have completed the first version of your micro-blog engine and you are now ready to deploy it to production on AWS. But this task will be left to the second part of the series.

Time to save your progress!

```bash
git cm 'vue.js and express fully integrated with auth0'
```

{% include tweet_quote.html quote_text="Developing Vue.js apps and integrating it with an Express backend is quite easy." %}

## Conclusion and Next Steps

In the first part of this series, you have created a Vue.js application to work as the user interface of a micro-blog engine. You have also created an Express API to persists micro-posts in a MongoDB instance. Besides that, you have installed and configured Auth0 on both your frontend and backend applications to take advantage of a modern identity management system. With this, you have finished developing the first version of your micro-blog engine and you are ready to move it to production.

So, [in the next part of this series](https://auth0.com/blog/vue-js-and-lambda-developing-production-ready-apps-part-2/), you will prepare your source code to deploy your backend API to AWS Lambda and your frontend Vue.js app to an AWS S3 bucket. Then, you will use [Claudia.js, a tool that facilitates AWS Lambda management](https://claudiajs.com/), to make your backend code live and will use the AWS CLI (Command Line Interface) tool to push your Vue.js app to AWS S3.

I hope you enjoy the process. Stay tuned!
