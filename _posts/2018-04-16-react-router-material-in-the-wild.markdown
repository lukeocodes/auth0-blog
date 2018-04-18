---
layout: post
title: "React, Express and Material UI: In the wild"
description: "A wild React application appears! Using React, React Router 4 and Material UI to build a real-world React application."
longdescription: "Learn how to build a React application using React Router 4 and Material UI. Building a robust backend API with Express, a functional frontend application with React and Material. We attempt to build a real-world example using Material to keep our user experience in-check, without sacrificing on security."
date: 2018-04-16 12:53
category: Technical Guide, Frontend, React
author:
  name: Luke Oliff
  url: https://twitter.com/mroliff
  avatar: https://avatars1.githubusercontent.com/u/956290?s=200
  mail: luke.oliff@auth0.com
design:
  bg_color: "#1A1A1A"
  image: https://cdn.auth0.com/blog/logos/react.png
tags:
- node
- express
- javascript
- react
- react-router-4
- react-router
- material
- material-ui
- routing
related:
- 2017-02-21-reactjs-authentication-tutorial
- 2017-10-26-whats-new-in-react16
- 2018-03-27-react-router-4-practical-tutorial
---

**TL;DR:** A brief synopsis that includes link to a [github repo](http://www.github.com/).

---

## What we're going to build

In this article, we're going to build a React app that makes use of React Router 4.

### React
#### React Router 4
#### React Components

### Material UI

### Node
#### Express

### Mongo DB
#### MLab
#### Mongoose

## a bit on security

Functionality (Material's UX) without sacrificing on security (tokens for our cool stuff inside an Express)

## pre-reqs

### Node & NPM

To get started we're going to need `node` and `npm` installed. Let's check if they're installed by running:

```bash
node --version
```

```bash
npm --version
```

If you don’t have `node` and `npm` installed, check out [nodejs.org](https://nodejs.org/) and install the correct version for your operating system. 

### yarn

```bash
npm install yarn -g
```

## create hello-world express api app

Create our new project directory to get started

```bash
mkdir ~/Projects/auth0-react-material 
cd ~/Projects/auth0-react-material
```

Initialize a yarn project in our new directory.

```bash
yarn init
```

Yarn prompts for some input. In my case I just accepted all the defaults, but you might want to do it differently. To follow this guide, I would leave `entry point` as `index.js`.

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-17 at 09.24.14.png)

Add `express` to our project

```bash
yarn add express
```

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-17 at 09.26.06.png)

and create our `entry point` file.

```bash
touch index.js
```

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-17 at 09.26.52.png)

now edit `index.js` and add the following *hello world* code.

```js
// index.js
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.json({
    hello: 'world',
    everything: 'seems ok'
  });
});

const port = process.env.PORT || 3001;
app.listen(port, () => console.log(`Listening on port ${port}`));
```

Before we run our development server, we're going to setup auto-reloading with *Nodemon*. When we change a file the development server will pick-up our changes automatically. I like to believe it's pronounced node-é-mon (like Pokémon).

```bash
yarn add nodemon --dev
```

make sure we add `--dev` as *Nodemon* should only be used for development.

When we install *Nodemon* it will create a binary file in `./node_modules/nodemon/bin/nodemon.js`. This is helpful, because `yarn` can detect and run binaries.

So all we need to do to run our dev server now is tell yarn to run nodemon.

```bash
yarn run nodemon
```

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-17 at 09.45.17.png)

You can also alias it in your `package.json` so you can customize it later.

```diff
// package.json
 {
   "name": "auth0-react-material",
   "version": "1.0.0",
   "main": "index.js",
   "license": "MIT",
   "dependencies": {
     "express": "^4.16.3"
   },
   "devDependencies": {
     "nodemon": "^1.17.3"
-  }
+  },
+  "scripts": {
+    "dev": "nodemon index.js"
+  }
 }
```

So now we can run

```bash
yarn run dev
```

Go and make sure everything is running okay

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-17 at 10.07.23.png)

Looks good!

### endpoint to return content to react app

edit `index.js` and add a new route, `/videos`.

```js
// index.js
...
app.get('/videos', (req, res) => {
  res.json({'videos': []});
});
...
```

#### fetch the content from remote

```bash
yarn add rss-parser
```

```js
// index.js
...
const parser = new (require('rss-parser'))();

const asyncVideosMiddleware = async (req, res, next) => {
  req.data = await parser.parseURL('https://www.youtube.com/feeds/videos.xml?channel_id=UCUlQ5VoIzE_kFbYjzUwHTKA');
  next();
};

app.get('/videos', asyncVideosMiddleware, (req, res) => {
  res.json(req.data);
});
...
```

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-17 at 11.06.26.png)

#### cache content with memory-cache

```bash
yarn add memory-cache
```

```js
// index.js
...
const cache = require('memory-cache');
...


const asyncVideosMiddleware = async (req, res, next) => {
  let videos = cache.get('videos');

  if (videos === null) {
    videos = await parser.parseURL('https://www.youtube.com/feeds/videos.xml?channel_id=UCUlQ5VoIzE_kFbYjzUwHTKA');
    cache.put('videos', videos, 1000 * 60 * 60); // 1 hour
  }

  req.data = videos;
  next();
};
...
```

Check it's still working. This time it comes from cache instead of making the request to the RSS feed and parsing the result.

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-17 at 11.06.26.png)

#### sensible file layout

```bash
mkdir controllers utils
```

`controllers` is where we're going to keep our routes and `utils` is where we're going to keep any utility logic, like contacting a 3rd party.

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-17 at 13.00.40.png)

```bash
touch controllers/videos.js utils/videos.js
```

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-17 at 13.01.25.png)

Now edit `utils/videos.js` where we'll move our async function for getting videos to.

```js
// utils/videos.js
const parser = new (require('rss-parser'))();
const cache = require('memory-cache');

const FEED_URL = 'https://www.youtube.com/feeds/videos.xml?channel_id=UCUlQ5VoIzE_kFbYjzUwHTKA';
const ONE_HOUR = 1000 * 60 * 60;

const videos = async (req, res, next) => {
  let videos = cache.get('videos');

  if (videos === null) {
    videos = await parser.parseURL(FEED_URL);
    cache.put('videos', videos, ONE_HOUR);
  }

  req.data = videos;
  next();
};

module.exports = videos;
```

Edit `controllers/videos.js`

```js
// controllers/videos.js
const router = require('express').Router();
const videos = require('../utils/videos');

router.get('/videos', videos, (req, res) => {
  res.json(req.data);
});

module.exports = router;
```

and edit `index.js` to look like this, much tidier.

```js
const express = require('express');
const app = express();

const videos = require('./controllers/videos');
app.use('/', videos);

const port = process.env.PORT || 3001;
app.listen(port, () => console.log(`Listening on port ${port}`));
```

## create react app (create-react-app)

### setup routes with react router 4
### layout using material ui
### fetch content from express app

## auth in react with auth0.js based on new guidelines

## user functionality endpoints in express
### 'mark as read', comment
#### mongoose for storage in mlab
#### sensible file layout
##### schemas
##### models

## deploys

## conclusion

## next steps (a react native guide)