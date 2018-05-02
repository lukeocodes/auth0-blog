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

In this article, we're going to be building two individual applications. Firstly, there will be an API, built on [Express](https://expressjs.com/) that is responsible for serving us our data. Secondly, our React application which will access our API. Our Express application is going to be built to verify tokens originally issued to our React application and our React application is going to be built to keep those tokens safe in memory, not persisting them to local storage. This is intended to demonstrate how a SPA application can be used for authentication, securely.

### React

For a while now, [React](https://reactjs.org) has been a developer favourite, developed by Facebook, with some amazing features. Working with the DOM can be frustrating, but by offering developers a small set of tools to learn, React makes interacting with the browser DOM accessible to a large set of developers familiar with JavaScript but unfamiliar with the DOM API. 

React gives us an ideal, or “virtual”, representation of our application, in our virtual DOM, with that simple set of tools to refine and manipulate it. Then, through a process of “[reconciliation](https://reactjs.org/docs/reconciliation.html)”, it syncs with the real DOM.

The simplified virtual DOM API that React provides us allows us to tell React what we want the application to look like and React makes sure the DOM matches. This abstracts out the attribute manipulation, event handling, and manual DOM updating that you would otherwise have to build yourself.

In the world of React, virtual DOM shouldn't be confused with Shadow DOM. Virtual DOM effectively describes React elements, whereby the Shadow DOM is a browser technology designed primarily for scoping variables and CSS in web components.

Newcomers can learn more about React, and how it works [here](https://reactforbeginners.com/).

#### React Router 4

React Router 4 is a complete ground up re-imagining of React Router, introducing breaking changes. But that's not all bad. We have a [practical run down of React Router 4](https://auth0.com/blog/react-router-4-practical-tutorial/) to cover all the bases, but I for one love the new direction React Router has taken on their path to 5.0.

React Router is a set of navigational components that offers declarative routing in your React application. That means you can control your application through the URL. It has powerful features such as lazy code loading, dynamic route matching, and location transition handling built in.

You can learn more about [React Router on reacttraining.com](https://reacttraining.com/react-router/)

#### React Components

React is one of the nicest implementations to enable componentization that I've seen in a UI library. [React components](https://reactjs.org/docs/components-and-props.html) allow you to split your code into independent, reusable pieces, and think about each piece in isolation. 

Components are like JavaScript functions that return React elements, describing what should appear on the screen. They accept arbitrary properties (`props`) and can have their own "state". When a Component's state or properties change, React re-renders the React elements, keeping the UI in sync.

A simple React component can be defined in basic JavaScript.

```js
function Auth0(props) {
  return <p>Auth0 welcomes {props.name} to React.</p>;
}

const Auth0 = (props) => <p>Auth0 welcomes {props.name} to React.</p>;
```

You can also use a JavaScript class, new with ECMAScript 2015 (ES6), to define a component.

```js
import React, { Component } from 'react';

class Auth0 extends Component {
  render() {
    return <p>Auth0 welcomes {props.name} to React.</p>;
  }
}
```

You can learn more about getting started, and [bootstrapping a React project](https://auth0.com/blog/bootstrapping-a-react-project/) here.

### Material UI

[Material UI](http://www.material-ui.com/#/) is a library of React components that provide you a simple to use implementation of Google's [Material Design guidelines](https://material.io/guidelines/), that can be configured to represent your own brand.

Google's [Material Design guidelines](https://material.io/guidelines/) seeks to set out the language needed to describe classic principles of good design, with the innovation and possibilities of modern technology. Design guidelines have been core to what print-based media businesses have done for years, setting out typography, grids, space, scale, color, and use of imagery so that creativity can evolve within margins that do not allow designers to stray too from the brand.

Material UI's current version is not quite a frontend framework as it lacks certain components a lot of developers look for. The core team had a vision to enable developers to utilize Material Design guidelines without being opinionated about layouts, for which there are other libraries available. But Material UI provides a strong foundation to produce brand design guidelines for frontend developers to work from.

In the [next version of Material UI](https://material-ui-next.com/), we'll see a more mature framework, complete with Grid system and other features developers have been yearning for.

### Node, NPM and Yarn

[Node](https://nodejs.org/en/) (or Node.js) is a free, open source server environment designed to work on pretty much any platform which uses JavaScript server-side. The speed and power of Node comes from how it detatches the request and response. In a traditional web-server the request is received, waits to read the code, renders a response and returns it to the client. Node does it slightly differently in that the request is received and then it goes back to listening for the next request. Once the response is ready it returns it to the client. This non-blocking asynchronous prcoessing of requests makes it very memory efficient.

[NPM](https://www.npmjs.com/) is the worlds largest software registry, with around 3 billion downloads per week and 600,000 packages registered. Most of these packages can be used on the frontend (on your browser) AND the backend (on your node server), enabling JavaScript developers, previously banished to the frontend, to build software on the backend. NPM can be used in your project for dependency management, creating a lock file that can deploy with your application, so no nasty version suprises occur on deployment.

[Yarn](https://yarnpkg.com/en/) is dependency management solution, also developed by Facebook. This project uses `yarn` but the demo app ships with a lock file for both yarn and NPM. 

#### Express

[Express](https://expressjs.com/) (or Express.js) is a super lightweight and flexible web application framework that provides a robust set of features for web and mobile applications.

We're using Express to build an API and Express comes with a whole bunch of HTTP utility methods and middleware, making creation of a robust API quick and easy.

### MongoDB

[MongoDB](https://www.mongodb.com) is an open-source NoSQL database that uses JSON documents for storage. Like other NoSQL databases, MongoDB supports dynamic schema design, allowing the documents in a collection to have different fields and structures from each other. 

MongoDB is supremely fast at writes (or inserts/updates) because it handles writes asynchronously. It does not queue requests, handle a transaction or wait for a response. It also doesn't concern itself with relationships or joins.

#### mLab

[mLab](https://mlab.com) is a cloud database service featuring automated provisioning and scaling of MongoDB databases with a handy web interface, backups and recovery. mLab's Database-as-a-Service platform is available on AWS, Azure, and Google. One of the features that I find most appealing is the ability to setup a sandbox database in a suitable region for free on any of the three platforms mentioned. It also comes with the advantage that you can signup and get a free sandbox database without the need for credit card information, which cam be very appealing for those seeking to prototype applications quickly.

We talk through signing up and creating a MongoDB database with mLab in this article.

#### Mongoose

[Mongoose](http://mongoosejs.com/) (or Mongoose.js) describes itself as elegant MongoDB object modeling for Node.js. It provides a neat API to describe object schemas, connect to a MongoDB service, query and save data with a familiar asynchronous operation, returning promises from methods such as `save` when we write data out to the database.

We're going to connect our Express application to mLab MongoDB database using Mongoose, but you can read more about using [Mongoose in Express](https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs/mongoose) here.

## Security First

There has been [plenty said](https://www.rdegges.com/2018/please-stop-using-local-storage/) about Single Page Applications and [security](https://www.owasp.org/index.php/HTML5_Security_Cheat_Sheet#Local_Storage) recently and it's forced a lot of very good developers to ask whether they're doing the responsible thing in teaching people to use local storage for access tokens.

So in this article we'll be ensuring we only store non-sensitive information in local storage, because local storage still has a place and it's extremely useful.

## Pre-requisites

### Node & NPM

To get started we're going to need `node` and `npm` installed. Let's check if they're installed by running:

```bash
node --version
```

```bash
npm --version
```

If you don’t have `node` and `npm` installed, check out [nodejs.org](https://nodejs.org/) and install the correct version for your operating system. 

### Yarn

Out of personal preference and nothing more, we'll create this project with Yarn. To do so, we'll need Yarn installed. The demo app ships with the ability to get up and running with Yarn and NPM.

```bash
npm install yarn -g
```

### Create-react-app

This command will allow us to create react apps with no build configuration. The basic application it gives us will provide us a great foundation to work from. 

```bash
npm install create-react-app -g
```

## Our basic Express API

First we need to create our new project directory. Remember where we put it!

```bash
mkdir auth0-react-material/ && cd "$_"
```

> **Note:** Here's a nifty bash technique, `"$_"` contains the last argument of the previous command, so you can `mkdir auth0-react-material/` and `cd auth0-react-material/` all in one command.

Initialize a yarn project in our new directory.

```bash
yarn init
```

Yarn prompts for some input. In my case I just accepted all the defaults, but you might want to do it differently. However, to follow this guide, I would leave `entry point` as `index.js`. Otherwise we'll all get confused!

![Initialize a project with Yarn](/Users/olaf/Desktop/upload react--yarn-init.png)

Now we can add `express` to our project.

```bash
yarn add express
```

![Add Express to a project](/Users/olaf/Desktop/upload react--add-express.png)

Next, create our `entry point` file.

```bash
touch index.js
```

![Create a project entry point file](/Users/olaf/Desktop/upload react--create-entry-point.png)

Now edit `index.js` and add the following *hello world* code.

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

Make sure we add `--dev` as *Nodemon* should only be used for development.

When we install *Nodemon* it will create a binary file in `./node_modules/nodemon/bin/nodemon.js`. This is helpful, because `yarn` can detect and run binaries.

So all we need to do to run our dev server now is tell yarn to run nodemon.

```bash
yarn run nodemon
```

![Run Nodemon with Yarn](/Users/olaf/Desktop/upload react--yarn-run-nodemon.png)

You can also alias it in your `package.json` so you can customize it later without changing the command you run.

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
-   }
+   },
+   "scripts": {
+     "dev": "nodemon index.js"
+   }
  }
```

So now we can run it like this.

```bash
yarn run dev
```

Go and make sure everything is running okay

![screenshot](/Users/olaf/Desktop/upload react--yarn-run-dev-everything-seems-okay.png)

Looks good!

### Create the API video endpoint

Now edit `index.js` and add a new route, `/videos`. This route is going to evolve throughout the article, but in essense it always receive a HTTP `GET` request and return JSON with a HTTP `200 OK` response.

```js
// index.js
// ...

app.get('/videos', (req, res) => {
  res.json({'videos': []});
});

// ...
```

#### Fetch content from a remote source

The data we're going to form out application around is videos posted to the [Auth0 YouTube channel](https://www.youtube.com/auth0). YouTube helpfully provides us with a [public RSS feed for videos available on our channel](https://www.youtube.com/feeds/videos.xml?channel_id=UCUlQ5VoIzE_kFbYjzUwHTKA). We're going to parse this RSS feed and use the data in our application.

To do this, install [`rss-parser`](https://www.npmjs.com/package/rss-parser). There are other feed parsers available, such as [`feedparser`](https://www.npmjs.com/package/feedparser), that offer a richer set of features. But `rss-parser` offers us a neat integration with a small footprint, perfect for our application.

```bash
yarn add rss-parser
```

Now we can use it in our app. Edit `index.js` and add the following code.

```js
// index.js
// ...

const parser = new (require('rss-parser'))();

// The public video feed for Auth0's YouTube channel
const URL = 'https://www.youtube.com/feeds/videos.xml?channel_id=UCUlQ5VoIzE_kFbYjzUwHTKA';

const asyncVideosMiddleware = async (req, res, next) => {
  req.data = await parser.parseURL(URL);
  next();
};

app.get('/videos', asyncVideosMiddleware, (req, res) => {
  res.json(req.data);
});

// ...
```

Essentially we're applying our `videos` data, fetched from our channel feed, to `req.data` and returning that as JSON. The `asyncVideosMiddleware` is exactly as it says, asynchronous middleware designed to run before we process the rest of our HTTP request.

![RSS feed videos parsed and returned as JSON](/Users/olaf/Desktop/upload react--rss-feed-videos-parsed-and-returned-as-json.png)

#### Cache the remote content

It wouldn't be acceptable for us to request our data from a remote source for every request made to our API. The responsible thing to do, even if YouTube can probably handle it, is to cache our data for a sensible amount of time. To do this, we're going to use [`memory-cache`](https://www.npmjs.com/package/memory-cache), which is a simple in-memory cache for NodeJS. The cache doesn't persist anywhere.

So add `memory-cache` to our project.

```bash
yarn add memory-cache
```

Now edit `index.js` and add it to our `asyncVideosMiddleware` so it's not trying to fetch it from the remote source on every request to our API.

```js
// index.js
// ...
const cache = require('memory-cache');

// ...

const asyncVideosMiddleware = async (req, res, next) => {
  let videos = cache.get('videos');

  if (videos === null) {
    const response = await parser.parseURL(URL);
    videos = response.items;
    cache.put('videos', videos, 1000 * 60 * 60);
  }

  req.data = videos;
  next();
};

// ...
```

> `1000 * 60 * 60` - or an hour in English!

Check it's still working. This time it comes from cache instead of making the request to the RSS feed and parsing the result.

#### Sensible code structure

Sticking all our middleware and routing in our `index.js` is going to get messy, especially with what we have planned! So we're going to create some directories to move some of our logic into tidy homes.

```bash
mkdir controllers utils
```

`controllers` is where we're going to keep our routes, and `utils` is where we're going to keep any middleware.

![The structure of Express directories](/Users/olaf/Desktop/upload react--structure-of-express-directories.png)

Now create two new files;

- a `videos` controller, to manage our video routes and,
- a `videos` util, where our async remote or cached videos will come from.

```bash
touch controllers/videos.js utils/videos.js
```

![Express router and middleware files](/Users/olaf/Desktop/upload react--structure-of-express-files.png)

Edit `utils/videos.js` where we'll move our async function for getting videos there.

```js
// utils/videos.js
const parser = new (require('rss-parser'))();
const cache = require('memory-cache');

// The public video feed for Auth0's YouTube channel
const URL = 'https://www.youtube.com/feeds/videos.xml?channel_id=UCUlQ5VoIzE_kFbYjzUwHTKA';

const videos = async (req, res, next) => {
  let videos = cache.get('videos');

  if (videos === null) {
    const response = await parser.parseURL(URL);
    videos = response.items;
    cache.put('videos', videos, 1000 * 60 * 60);
  }

  req.data = videos;
  next();
};

module.exports = videos;
```

Edit `controllers/videos.js` to place our `/videos` route there.

```js
// controllers/videos.js
const router = require('express').Router();
const videos = require('../utils/videos');

router.get('/videos', videos, (req, res) => {
  res.json(req.data);
});

module.exports = router;
```

Edit `index.js` to look like this, much tidier.

```js
// index.js
const express = require('express');
const app = express();

const videos = require('./controllers/videos');
app.use('/', videos);

const port = process.env.PORT || 3001;
app.listen(port, () => console.log(`Listening on port ${port}`));
```

> ***Note:*** When we require routing files and `use` them like this, we have the opportunity to apply a prefix to an entire routing file. `app.use('/some/prefix', someRouting)`.

## Our basic React application

We're going to create our React application, which will consume our backend Express API. Using `create-react-app` will create our new `www-client` directory.

```bash
create-react-app www-client
```

Test our new React application by changing into our `www-client` directory and running `yarn start`.

```bash
cd www-client
yarn start
```

From here on out we'll be using the `www-client` directory as our current Working Directory. We'll move back to the express application directory when we need to.

![Test the new React application](/Users/olaf/Desktop/upload react--create-react-app-first-run.png)

Now just to tidy up a bit and remove some boilerplate.

Create a components directory, where our React components will live.

```bash
mkdir src/components
```

Remove some files we're not going to need.

```bash
rm src/App.css src/App.test.js src/index.css src/logo.svg
```

Move our `App.js` component into our `components` directory.

```bash
mv src/App.js src/components/
```

Everything is much tidier now, but our app won't run now. Let's fix it. Edit our `src/index.js` file like this.

```diff
// src/index.js
  import React from 'react';
  import ReactDOM from 'react-dom';
- import './index.css';
- import App from './App';
+ import App from './components/App';
  import registerServiceWorker from './registerServiceWorker';

// ...
```

Edit our `src/components/App.js` to remove the `css` and `svg` use.

```diff
// src/components/App.js
  import React, { Component } from 'react';
- import logo from './logo.svg';
- import './App.css';

  class App extends Component {
    render() {
      return (
        <div className="App">
          <header className="App-header">
-           <img src={logo} className="App-logo" alt="logo" />
            <h1 className="App-title">Welcome to React</h1>
          </header>
          <p className="App-intro">
            To get started, edit <code>src/App.js</code> and save to reload.
          </p>
        </div>
      );
    }
  }
```

Now everything should still work, if not a bit uglier. But it's a good foundation for whats coming.

![Uglier basic React application](/Users/olaf/Desktop/upload react--uglier-basic-react-application.png)

### Setup routing with React Router 4

```bash
yarn add react-router-dom
```

Edit `src/index.js` with the following code.

```js
// src/index.js
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
import App from './components/App';
import registerServiceWorker from './registerServiceWorker';

ReactDOM.render((
  <BrowserRouter>
    <App />
  </BrowserRouter>
), document.getElementById('root'));
registerServiceWorker();
```

Create some new empty components `Main.js`, `Header.js` and `Home.js`. 

- `Main` is going to be the home of our routing in React, where we choose component based on our URL.
- `Header` is where we'll show our links where we'll interact with react router with our navigation components.
- `Home` will be where we put the initial content we want to load.

```bash
touch src/components/{Main.js,Header.js,Home.js}
```

> **Note:** *Another* nifty bash technique is the ability to run commands multiple times by providing it an array of arguments. This is called [Brace Expansion](http://wiki.bash-hackers.org/syntax/expansion/brace). It is the equivalent of `touch src/components/Main.js`, `touch src/components/Header.js` and `touch src/components/Home.js` all in one command.

For the moment just set them up as basic components by editing `src/components/Main.js`, `src/components/Header.js` and `src/components/Home.js` as shown below.

```js
// src/components/Header.js
import React from 'react';

const Header = () => (
  <header>
    <h1>Welcome to React</h1>
  </header>
);

export default Header;
```

```js
// src/components/Home.js
import React from 'react';

const Home = () => (
  <p>
    Hello, react world!
  </p>
);

export default Home;
```

```js
// src/components/Main.js
import React from 'react';
import Home from './Home';

const Main = () => (
  <div>
    <Home/>
  </div>
);

export default Main;
```

Now edit `src/components/App.js` to use some of our new components. It should look like the following.

```js
// src/components/App.js
import React from 'react';
import Header from './Header';
import Main from './Main';

const App = () => (
  <div>
    <Header />
    <Main />
  </div>
);

export default App;
```

Nice and neat so far! Let's see if it's working.

![App, Main and Header React components](/Users/olaf/Desktop/upload react--app-main-header-components-working.png)

Now we're going to create another page, something for us to route to.

```bash
touch src/components/Videos.js
```

Give it this code.

```js
// src/components/Videos.js
import React from 'react';

const Videos = () => (
  <p>
    Some video content here. Or something.
  </p>
);

export default Videos;
```

Now we can setup some routing to allow us to switch between `Home` and `Videos`, edit `src/components/Main.js` to look like this.

```js
// src/components/Main.js
import React from 'react';
import { Switch, Route } from 'react-router-dom';
import Home from './Home';
import Videos from './Videos';

const Main = () => (
  <div>
    <Switch>
      <Route exact path='/' component={Home}/>
      <Route path='/videos' component={Videos}/>
    </Switch>
  </div>
);

export default Main;
```

Now check we can load our `video` route.

![React Router 4 loading the Video component](/Users/olaf/Desktop/upload react--react-router-loading-video-component.png)

But we don't have any links yet! so lets create a quick navigation menu in our `Header` component. So edit `src/components/Header.js` with the code shown here

```js
// src/components/Header.js
import React from 'react';
import { Link } from 'react-router-dom';

const Header = () => (
  <header>
    <h1>Welcome to React</h1>
    <nav>
      <ul>
        <li><Link to='/'>Home</Link></li>
        <li><Link to='/videos'>Videos</Link></li>
      </ul>
    </nav>
  </header>
);

export default Header;
```

Check that our nav menu works with our router by being able to navigate between our `Home` and `Video` pages.

![React Router navigating between the Home and Video routes](/Users/olaf/Desktop/upload react--react-router-navigating-between-home-and-video-routes.gif)

### Layout with Material UI

`material-ui` needs the `Roboto` font, for which we'll need the `webfontloader` library. Let's install those and set them up.

```bash
yarn add material-ui webfontloader
```

Now use `webfontloader` it to load `Roboto` by editing `src/index.js` and adding the following code.

```js
// src/index.js

// ...

import WebFont from 'webfontloader';

WebFont.load({
  google: {
    families: ['Roboto:300,400,500', 'sans-serif']
  }
});

// ...
```

While we're here, we can add Material UI's default theme provider component to our app. So our `src/index.js` will end up looking like this.

```js
// src/index.js
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
import App from './components/App';
import registerServiceWorker from './registerServiceWorker';
import WebFont from 'webfontloader';
import MuiThemeProvider from 'material-ui/styles/MuiThemeProvider';

WebFont.load({
  google: {
    families: ['Roboto:300,400,500', 'sans-serif']
  }
});

ReactDOM.render((
  <MuiThemeProvider>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </MuiThemeProvider>
), document.getElementById('root'));
registerServiceWorker();
``` 

Despite these changes, you'll see nothing has changed.

![Material UI and Roboto setup but nothing has changed](/Users/olaf/Desktop/upload react--material-ui-and-roboto-but-nothing-has-changed.png)

We'll start off simple, we'll make our content look a bit prettier but putting it inside a [Material UI `Paper` component](http://www.material-ui.com/#/components/paper). So edit `src/components/Home.js`, import the `Paper` component and place our text inside a `Paper` tag, like so;

```js
import React from 'react';
import Paper from 'material-ui/Paper';

const Home = () => (
  <Paper
    style={{
      padding: '1em'
    }}>
    Hello, react world!
  </Paper>
);

export default Home;
```

We can check that Material UI is working just fine. Notice how the styles from the base component are applied to Material UI's components, only.

![screenshot](/Users/olaf/Desktop/upload react--content-inside-paper-component.png)

To tackle our `Header` component, we'll make use of the [Material UI `AppBar` component](http://www.material-ui.com/#/components/app-bar).

Edit `src/components/Header.js` and follow the changes below.

We'll change our `Header` component from a stateless [Functional Component to a Class Component](https://reactjs.org/docs/components-and-props.html). This is because our component now needs a state and some functions of it's own.

```diff
- import React from 'react';
+ import React, { Component } from 'react';
  import { Link } from 'react-router-dom';
  
- const Header = () => (
+ class Header extends Component {
+   render() {
+     return (
        <header>
          <h1>Welcome to React</h1>
          <nav>
            <ul>
              <li><Link to='/'>Home</Link></li>
              <li><Link to='/videos'>Videos</Link></li>
            </ul>
          </nav>
        </header>
      );
+   }
+ }
  
  export default Header;
```

Next we'll replace our `<h1>` with an `AppBar` component.

```diff

// ...

+ import AppBar from 'material-ui/AppBar';


// ...

-         <h1>Welcome to React</h1>
+         <AppBar
+           title="Welcome to React"
+         />

// ...

```

It should be looking something like this now.

![screenshot](/Users/olaf/Desktop/upload react--Screen Shot 2018-04-18 at 19.59.34.png)

how cool would it be to move that nav into a `Drawer` component that slides in from the side, we can do this with the [Material UI `Drawer` component](http://www.material-ui.com/#/components/drawer).

This time we're going to add a constructor to set our initial state and two functions to handle toggling and closing our `Drawer`.

```diff

// ...

+ import Drawer from 'material-ui/Drawer';
+ import MenuItem from 'material-ui/MenuItem';


// ...

  class Header extends Component {
+   constructor(props) {
+     super(props);
+     this.state = {open: false};
+   }
+
+   handleToggle = () => this.setState({open: !this.state.open});
+
+   handleClose = () => this.setState({open: false});
+
    render() {

// ...

-         <nav>
-           <ul>
-             <li><Link to='/'>Home</Link></li>
-             <li><Link to='/videos'>Videos</Link></li>
-           </ul>
-         </nav>
+         <Drawer
+           docked={false}
+           open={this.state.open}
+           onRequestChange={(open) => this.setState({open})}
+         >
+           <MenuItem
+             containerElement={<Link to='/' />}
+             primaryText="Home"
+             onClick={this.handleClose}
+           />
+           <MenuItem
+             containerElement={<Link to='/videos' />}
+             primaryText="Videos"
+             onClick={this.handleClose}
+           />
+         </Drawer>

// ...

    }

// ...

```

So now our links have disappeared, we can't click them and we can't see the menu. We just need to tell our `AppBar` to open the `Drawer` when we click on the menu button.

To do this, we need to tell the left most icon on the `AppBar` that it's a button with a function!

```diff

// ...

+ import IconButton from 'material-ui/IconButton';
+ import MenuIcon from 'material-ui/svg-icons/navigation/menu';


// ...

          <AppBar
            title="Welcome to React"
+           iconElementLeft={<IconButton
+             label="Open Menu"
+             onClick={this.handleToggle}
+           ><MenuIcon /></IconButton>}
          />

// ...

```

Once done the file should end up looking like this

```js
import React, { Component } from 'react';
import { Link } from 'react-router-dom';
import AppBar from 'material-ui/AppBar';
import Drawer from 'material-ui/Drawer';
import MenuItem from 'material-ui/MenuItem';
import IconButton from 'material-ui/IconButton';
import MenuIcon from 'material-ui/svg-icons/navigation/menu';

class Header extends Component {
  constructor(props) {
    super(props);
    this.state = {open: false};
  }

  handleToggle = () => this.setState({open: !this.state.open});

  handleClose = () => this.setState({open: false});

  render() {
    return (
      <header>
        <AppBar
          title="Welcome to React"
          iconElementLeft={<IconButton
            label="Open Drawer"
            onClick={this.handleToggle}
          ><MenuIcon /></IconButton>}
        />
        <Drawer
          docked={false}
          open={this.state.open}
          onRequestChange={(open) => this.setState({open})}
        >
          <MenuItem
            containerElement={<Link to='/' />}
            primaryText="Home"
            onClick={this.handleClose}
          />
          <MenuItem
            containerElement={<Link to='/videos' />}
            primaryText="Videos"
            onClick={this.handleClose}
          />
        </Drawer>
      </header>
    );
  }
}

export default Header;
```

And if we run it, we should be able to navigate like this!

![screenshot](/Users/olaf/Desktop/upload react--Screen Shot 2018-04-18 at 20.26.05.gif)

### fetch content from express app

first thing is first, we need to enable CORS for our express app.

Back in our express apps root directory, let's install `cors`.

```bash
cd ..
yarn add cors
```

To configure it, edit our `index.js` and to look like the following

```js
const express = require('express');
const cors = require('cors');
const app = express();

const corsOptions = {
  origin: process.env.CORS_ORIGIN || '*',
};

app.use(cors(corsOptions));

const videos = require('./controllers/videos');
app.use('/', videos);

const port = process.env.PORT || 3001;
app.listen(port, () => console.log(`Listening on port ${port}`));
```

notice origin is set to '*' if the environment variable isn't set `origin: process.env.CORS_ORIGIN || '*'` so that it's not restricted during the development process. It will be insecure if you deploy to production without your origin set

back to our client react application

```bash
cd www-client
```

now we'll need to add a library for embedding youtube videos

```bash
yarn add react-youtube
```

create a new component for our video

```bash
touch src/components/Video.js
```

give it the following code 

```js
import React from 'react';
import YouTube from 'react-youtube';
import { Card, CardMedia } from 'material-ui/Card';

const Video = ({video}) => (
  <Card
    style={{
      width: '400px',
      padding: '10px',
      marginRight: '1em',
      marginBottom: '1em',
      textAlign: 'center',
      display: 'inline-block',
    }}>
    <CardMedia
    >
      <YouTube
        opts={{
          width: '380',
          height: '220',
        }}
        videoId={(video.id.split(":").pop())}
      />
    </CardMedia>
  </Card>
);

export default Video;
```

lets use this component to display our videos, edit `src/components/Videos.js` to look like this

```js
import React, { Component } from 'react';
import Video from './Video';

const API = 'http://localhost:3001';

class Videos extends Component {
  constructor(props) {
    super(props);

    this.state = {
      videos: [],
    };
  }

  componentDidMount() {
    const config = { headers: {} };

    fetch(`${API}/videos`, config)
      .then(response => response.json())
      .then(data => this.setState({ videos: data }));
  }

  render() {
    const { videos } = this.state;

    return (
      <div>
        {videos.map(video =>
          <Video
            key={video.id}
            video={video}
            {...this.props}
          />
        )}
      </div>
    );
  }
}

export default Videos;
```

you'll see this is another component that has changed from a [Functional Component to a Class Component](https://reactjs.org/docs/components-and-props.html) so that we can manage it's state

breaking down this component you'll see that we are using `constructor` to set a default state and `componentDidMount` which is a lifecycle method that is invoked immediately after a component is mounted.

here we make an request to our API. response is returned as an asychronous `Promise`. here we'll update the state of the component (when `videos` are returned from the API, we update the component to know about them). when a components state changes, `render` is called again. This means that we can put a pretty loader on our component and it will update once our data is returned. Remember, our data is being cached in memory as well.

one quick change to `src/components/Main.js` to widen the margin between the headers `AppBar` and the main content.

```diff
-   <div>
+   <div style={{
+     marginTop: '1em'
+   }}>
```

Our app should look something like this

![screenshot](/Users/olaf/Desktop/upload react--Screen Shot 2018-04-24 at 12.36.50.png)

## auth in react with auth0.js based on new guidelines

We're going to use [Auth0](https://auth0.com) to identify our users.

We're also going to be following the [Auth0 React Quickstart](https://auth0.com/docs/quickstart/spa/react) to get set up with Auth0 authentication on our new application. If you're confident with React you could skip straight to the quickstart or get the code you need from the [Auth0 React samples repository](https://github.com/auth0-samples/auth0-react-samples/tree/embedded-login).

The [Auth0 login page](https://auth0.com/docs/hosted-pages/login) is the easiest way to set up authentication in your application.

### Sign Up for Auth0

You'll need an [Auth0](https://auth0.com) account to manage authentication. You can <a href="https://auth0.com/signup" data-amp-replace="CLIENT_ID" data-amp-addparams="anonId=CLIENT_ID(cid-scope-cookie-fallback-name)">sign up for a free Auth0 account here</a>. Next, set up an Auth0 Application so Auth0 can interface with your app.

### Set up an Application

1. Go to your [**Auth0 Dashboard**](https://manage.auth0.com/#/) and click the "[create a new application](https://manage.auth0.com/#/applications/create)" button. 
2. Name your new app, select "Single Page Web Applications", and click the "Create" button. 
3. In the **Settings** for your new Auth0 app, add `http://localhost:3000/callback` to the **Allowed Callback URLs**.
4. Click the "Save Changes" button.
5. If you'd like, you can [set up some social connections](https://manage.auth0.com/#/connections/social). You can then enable them for your app in the **Application** options under the **Connections** tab. The example shown in the screenshot above utilizes username/password database, Facebook, Google, and Twitter.

> **Note:** Under the **OAuth** tab of **Advanced Settings** (at the bottom of the **Settings** section) you should see that the **JsonWebToken Signature Algorithm** is set to `RS256`. This is the default for new applications. If it is set to `HS256`, please change it to `RS256`. You can [read more about RS256 vs. HS256 JWT signing algorithms here](https://community.auth0.com/questions/6942/jwt-signing-algorithms-rs256-vs-hs256).

### Install auth0.js

You need the auth0.js library to integrate Auth0 into your application.

Install auth0.js using npm.

```bash
yarn add auth0-js
```

### Create the Auth class

We'll need a new class to manage and coordinate user authentication. 

#### Basic class

Create a new file `src/auth/Auth.js` and inside it put the following code:

```js
// src/auth/Auth.js
import auth0 from 'auth0-js';

const AUTH0_DOMAIN = '<your-domain>.auth0.com';
const AUTH0_CLIENT_ID = '<your-client-id>';

export default class Auth {
  auth0 = new auth0.WebAuth({
    domain: AUTH0_DOMAIN,
    clientID: AUTH0_CLIENT_ID,
    redirectUri: 'http://localhost:3000/callback',
    audience: `https://${AUTH0_DOMAIN}/api/v2/`,
    responseType: 'token id_token',
    scope: 'openid profile email'
  });

  login() {
    this.auth0.authorize();
  }
}
```

Edit `src/auth/Auth.js` and replace `<your-domain>` and `<your-client-id>` with your Auth0 domain prefix and your client ID, found on your [application dashboard](https://manage.auth0.com/#/applications).

![screenshot](/Users/olaf/Desktop/upload react--Screen Shot 2018-04-24 at 12.49.05.png)

#### Test our Auth class

Quickly, we'll test that our new Auth class is configured properly. To quickly do this we'll add the following code to our `src/components/App.js` file. Don't worry about making a mess, this file is about to change, dramatically.

```js
// src/components/App.js

// ...

import Auth from '../auth/Auth';

const auth = new Auth();
auth.login();

const App = () => (
  
// ...

```

When you visit [localhost:3000](http://localhost:3000/) now, we'll be redirected to our login page.

![screenshot](/Users/olaf/Desktop/upload react--Screen Shot 2018-04-24 at 12.33.42.png)

If we get our login box, we know our library is configured correctly. **Logging in won't work,** we haven't built our callback yet.

#### Finish the Auth class

We need a few more methods in the `Auth` class to help us manage authentication in the app. So add the following code to `src/auth/Auth.js`:

```js
// src/auth/Auth.js
  
// ...

export default class Auth {
  
  // ...

  constructor() {
    this.login = this.login.bind(this);
    this.logout = this.logout.bind(this);
    this.handleAuthentication = this.handleAuthentication.bind(this);
    this.isAuthenticated = this.isAuthenticated.bind(this);

    this.accessToken = undefined;
    this.userProfile = undefined;

    this.authorizedCallback = () => {};
    this.deauthorizedCallback = () => {};
  }

  handleAuthentication() {
    this.auth0.parseHash((err, authResult) => {
      if (authResult && authResult.accessToken && authResult.idToken) {
        this.getUserInfo(authResult);
      } else if (err) {
        console.error(err);
      }
    });
  }

  getUserInfo(authResult) {
    this.auth0.client.userInfo(authResult.accessToken, (err, profile) => {
      if (!err) {
        this.setSession(authResult, profile);
      } else {
        console.error(err);
      }
    });
  }

  setSession(authResult, userProfile) {
    let expiresAt = JSON.stringify((authResult.expiresIn * 1000) + new Date().getTime());
    localStorage.setItem('expires_at', expiresAt);

    this.accessToken = authResult.accessToken;
    this.userProfile = userProfile;

    this.authorizedCallback(this.isAuthenticated());
  }

  logout() {
    localStorage.removeItem('expires_at');

    this.accessToken = undefined;
    this.userProfile = undefined;

    this.deauthorizedCallback(this.isAuthenticated());
  }

  isAuthenticated() {
    let expiresAt = JSON.parse(localStorage.getItem('expires_at'));
    return (Date.now() < expiresAt) && this.accessToken !== undefined;
  }
}
```

#### The callback

Create a new file `src/components/Callback.js` and add this code.

```js
// src/components/Callback.js

import React, { Component } from 'react';
import Paper from 'material-ui/Paper';
import CircularProgress from 'material-ui/CircularProgress';

class Callback extends Component {
  render() {
    return (
      <Paper
        style={{
          padding: '3em'
        }}>
        <CircularProgress
          size={80}
          thickness={5}
          style={{
            left: '50%',
            marginLeft: '-40px'
          }}
        />
      </Paper>
    );
  }
}

export default Callback;
```

Besides the pretty spinning `CircularProgress`, the function of this class will be to identify an incoming accessToken supplied in `window.location.hash` and handle the authentication.

We quickly setup some routing to it by editing `src/components/Main.js` and adding a route for `Callback`

```js
// src/components/Main.js

// ...

import Home from './Home';
import Videos from './Videos';
import Callback from './Callback';

// ...

      <Switch>
        <Route exact path='/' component={Home}/>
        <Route path='/videos' component={Videos}/>
        <Route path='/callback' component={Callback}/>
      </Switch>

// ...
```

When we visit it directly without a hash, you can see it doesn't do very much else!

![screenshot](/Users/olaf/Desktop/upload react--Screen Shot 2018-04-24 at 13.01.15.png)

Now for the magic bit, edit the `src/components/Callback.js` and add the `componentDidMount` method to the component class like this.

```js
// src/components/Callback.js

// ...

class Callback extends Component {
  componentDidMount() {
    if (/access_token|id_token|error/.test(window.location.hash)) {
      this.props.auth.handleAuthentication();
    }
  }
    
  // ...
}
// ...
```

Edit `src/components/App.js` for our big change. We'll remove our test code and change the component from a stateless to a stateful component, so it's aware of our authentiation situation.

```js
// src/components/App.js
import React, { Component } from 'react';
import { withRouter } from 'react-router-dom';
import Header from './Header';
import Main from './Main';
import Auth from '../auth/Auth';

class App extends Component {
  authorized(authenticated) {
    this.setState({ authenticated });

    if (localStorage.getItem('redirectTo')) {
      this.props.history.push(localStorage.getItem('redirectTo'));
    } else {
      this.props.history.push('/');
    }
  }

  deauthorized() {
    this.props.history.push('/');
  }

  constructor() {
    super();
    this.auth = new Auth();
    this.auth.authorizedCallback = this.authorized.bind(this);
    this.auth.deauthorizedCallback = this.deauthorized.bind(this);

    this.state = { authenticated: false };
  }

  render() {
    return (
      <div>
        <Header
          isAuthenticated={this.state.authenticated}
          auth={this.auth}
        />
        <Main
          auth={this.auth}
        />
      </div>
    );
  }
}

export default withRouter(App);
```

You'll see we've now got a `constructor()` that creates our `new Auth()` instance like our test code did. Then we apply two callback functions to the class so our app knows what to do after login/logout.

```js
  authorized(authenticated) {
    this.setState({ authenticated });

    if (localStorage.getItem('redirectTo')) {
      this.props.history.push(localStorage.getItem('redirectTo'));
    } else {
      this.props.history.push('/');
    }
  }

  deauthorized() {
    this.props.history.push('/');
  }
```

The `authorized` callback function updates the `App` component state. So we've given our `Auth` class the ability to nudge our `React` app once authentication has taken place. The `authorized` function will also look to see if we have a localStorage item for a `redirectTo` path, so after we've authorized we can push the user to a predetermined route, like the page they were on when they tried to login. The `deauthorized` function is response for pushing us back to the `Home` route once we've logged out.

The last little change is adding the `withRouter` [higher-order component](https://reactjs.org/docs/higher-order-components.html). Concretely, a higher-order component is a function that takes a component and returns a new component. Whereas a component transforms props into UI, a higher-order component transforms a component into another component.

```js
// src/components/App.js

// ...

export default withRouter(App);
```

Edit our `Main` component with our final routing changes, which shares the Auth class instance with our main components. Edit `src/components/Main.js` and replace it's contents with the following code.

```js
// src/components/Main.js

import React from 'react';
import { Switch, Route } from 'react-router-dom';
import Home from './Home';
import Videos from './Videos';
import Callback from './Callback';

const Main = ({auth}) => {
  return (
    <div style={{
      marginTop: '1em'
    }}>
      <Switch>
        <Route exact path='/' render={(props) => <Home auth={auth} {...props} />}/>
        <Route path='/videos' render={(props) => <Videos auth={auth} {...props} />}/>
        <Route path='/callback' render={(props) => <Callback auth={auth} {...props} />}/>
      </Switch>
    </div>
  );
};

export default Main;
```

You might notice that we've changed from `Route component={}` to [`Route render={}`](https://reacttraining.com/react-router/web/api/Route/render-func) props. This allows us to use an inline function to pass props through to our routed components.

Now our application is aware of our authentication state, we can modify our `Header` to show our log in/log out button.

Replace the contents of `src/components/Header.js` with this code.

```js
// src/components/Header.js
import React, { Component } from 'react';
import { Link } from 'react-router-dom';
import AppBar from 'material-ui/AppBar';
import Drawer from 'material-ui/Drawer';
import MenuItem from 'material-ui/MenuItem';
import IconButton from 'material-ui/IconButton';
import MenuIcon from 'material-ui/svg-icons/navigation/menu';
import FlatButton from 'material-ui/FlatButton';

class Header extends Component {
  constructor(props) {
    super(props);
    this.state = {open: false};
  }

  handleToggle = () => this.setState({open: !this.state.open});

  handleClose = () => this.setState({open: false});

  handleLogin = () => {
    localStorage.setItem('redirectTo', window.location.pathname);

    this.props.auth.login();
  };

  handleLogout = () => {
    this.props.auth.logout();
  };

  render() {
    const { isAuthenticated } = this.props.auth;

    const button = !isAuthenticated() ? (
      <FlatButton label="Log in" onClick={this.handleLogin.bind(this)} />
    ) : (
      <FlatButton label="Log out" onClick={this.handleLogout.bind(this)} />
    );

    return (
      <header>
        <AppBar
          title="Welcome to React"
          iconElementLeft={<IconButton
            label="Open Drawer"
            onClick={this.handleToggle}
          ><MenuIcon /></IconButton>}
          iconElementRight={button}
        />
        <Drawer
          docked={false}
          open={this.state.open}
          onRequestChange={(open) => this.setState({open})}
        >
          <MenuItem
            containerElement={<Link to='/' />}
            primaryText="Home"
            onClick={this.handleClose}
          />
          <MenuItem
            containerElement={<Link to='/videos' />}
            primaryText="Videos"
            onClick={this.handleClose}
          />
        </Drawer>
      </header>
    );
  }
}

export default Header;
```

The big changes are are we're adding the `iconElementRight` prop, which contains a button, to the `AppBar`. The state of the button is determined by the authentication state.

If the user is logged in, you'll see `Log out` and visa versa.

Now we can test it!

![screenshot](/Users/olaf/Desktop/upload react--Screen Shot 2018-04-24 at 15.00.37.gif)

The last thing to do is add our avatar to our `Log out` button. Because why not! Edit `src/components/Header.js`. This is assuming they'll always have a `picture` in their OpenID profile, otherwise you could use the [Material UI `Avatar` component](http://www.material-ui.com/#/components/avatar) to do letters or icons.

```js
// src/components/Header.js
// ...
import Avatar from 'material-ui/Avatar';

// ...

  render() {
    // ...

    let avatar = '';

    if (userProfile) {
      avatar = <Avatar src={userProfile.picture} size={30} />
    }

    // ...

    return (
      // ...

      <FlatButton label="Log out" onClick={this.handleLogout.bind(this)} icon={avatar} />

      // ...
    );
  }
}

// ...
```

And test!

![screenshot](/Users/olaf/Desktop/upload react--Screen Shot 2018-04-24 at 16.03.37.png)

## Verifying an access token in Express

So one of the things we want to do is verify our user on our Express application, without having to re-authenticate them. In our React application, we have our access token stored in memory. We can pass that through with our requests to express and verify them there.

If we're authenticated on our React application, we want to pass through our authToken to Express. So edit `src/components/Videos.js` where we make our request to Express and add this code.

```js
// src/components/Videos.js
// ...

class Videos extends Component {
  // ...

  componentDidMount() {
    // ...

    if (this.props.auth.isAuthenticated()) {
      config.headers.Authorization = `Bearer ${this.props.auth.accessToken}`;
    }

    // ...
  }

  // ...
}

// ...
```

Simply, if `isAuthenticated()` is `true`, we add our token to the headers of our `fetch`.

Now we need to verify that in our Express application. For that we're going to need the `express-jwt` and `jwks-rsa` packages. 

- `express-jwt` is an Express middleware that validates Json Web Tokens and once verified sets `req.user` on the [Express `req` object](https://expressjs.com/en/api.html#req).
- `jwks-rsa` is Auth0's very own library to retrieve RSA signing keys from a [JWKS (JSON Web Key Set)](https://auth0.com/docs/jwks) endpoint.

Combined, these two packages will allow us to verify our token on our backend even if it was for our frontend application.

Change to our backend application's directory.

```bash
cd ..
```

Install `express-jwt` and `jwks-rsa`.

```bash
yarn add express-jwt jwks-rsa
```

Now while we're here, we need to create our own middleware to verify our token. So create a new file `utils/auth.js` and add the following to the file.

```js
// utils/auth.js
const jwt = require('express-jwt');
const jwksRsa = require('jwks-rsa');

const AUTH0_DOMAIN = '<your-domain>.auth0.com';
const issuer = `https://${AUTH0_DOMAIN}`;
const config = {
  secret: jwksRsa.expressJwtSecret({ jwksUri: `${issuer}.well-known/jwks.json` }),
  audience: `${issuer}api/v2/`,
  issuer: issuer,
  algorithms: [ 'RS256' ]
};

const auth = {
  required: (req, res, next) => {
    return jwt(config)(req, res, next);
  },
  optional: (req, res, next) => {
    return jwt({...config, credentialsRequired: false})(req, res, next);
  }
};

module.exports = auth;
```

Now we have a means to protect our endpoints with auth that is both required or optional. Applying authentication to an endpoint is as easy as this;

```js
const auth = require('utils/auth.js');

app.get('/protected_endpoint', auth.required, (req, res) => {
  res.json(req.data);
});
```

But if you want an endpoint to be available whether they're authenticated or not, you can do this;

```js
const auth = require('utils/auth.js');

app.get('/protected_endpoint', auth.optional, (req, res) => {
  res.json(req.data);
});
```

A couple of steps ago, we applied a condition to our client app's `Videos` component when `componentDidMount` fires, that if a user is authenticated it will apply the `accessToken` to any requests it makes. So lets edit our `/videos` endpoint so it knows to verify our `accessToken`.

Edit `controllers/videos.js` with this code, whicvh allows `/videos` to be accessed with optional authentication.

```js
// ...
const auth = require('../utils/auth');

router.get('/videos', auth.optional, videos, (req, res) => {
  // ...
});

// ...
```

Now our `/videos` route (and our `videos` middleware) are both aware when our user is authenticated, as the validated user is stored in `req.user`.

## Save our favourite videos

Now we're logged in, we might want to favourite the videos we see, so we can find them easier later. To do this, we're going to need to store our favourites in a database.

The database we're going to use is [MongoDB](https://www.mongodb.com/), and [Mongoose](http://mongoosejs.com/) is a great object modeling tool designed to work in an asynchronous environment.

### Setting up a MongoDB database

We're going to use mLab's free cloud-hosted "sandbox" database. This database is not considered suitable for production websites because it lacks redundancy and its support, availability, and size are limited; however, this database is great for development and prototyping.

[Create a free account](https://mlab.com/signup/) with mLab. The bonus over a free AWS or Google Cloud instance is that you can get up and running without providing any payment details.

![screenshot](/Users/olaf/Desktop/upload react--Screen Shot 2018-04-30 at 16.00.54.png)

After logging in, you'll be taken to your [home screen](https://mlab.com/home).

* Click **Create New** in the MongoDB Deployments section of the home screen.

  ![screenshot](/Users/olaf/Desktop/upload react--Screen Shot 2018-04-30 at 16.02.04.png)

* This opens the Cloud Provider Selection screen.

  ![screenshot](/Users/olaf/Desktop/upload react--Screen Shot 2018-04-30 at 16.02.31.png)

  * Select any provider from the Cloud Provider section. Their availability regions differ.

  * Select the 'SANDBOX' plan from the Plan Type section, it's free.

  * Now click **Continue**.

* This opens the Select Region screen.

  ![screenshot](/Users/olaf/Desktop/upload react--Screen Shot 2018-04-30 at 16.02.40.png)

  * Select the region closest to you and click **Continue**.

* This opens the Final Details screen.

  ![screenshot](/Users/olaf/Desktop/upload react--Screen Shot 2018-04-30 at 16.03.18.png)

  * Enter the name of your new database and click **Continue**.

* This opens the Order Confirmation screen.

  ![screenshot](/Users/olaf/Desktop/upload react--Screen Shot 2018-04-30 at 16.03.25.png)

  * Confirm the details and click **Submit Order**.

* You'll be returned to the home screen.

  * Open the database you just created. Note the URL shown (or where to find it), as you'll need it later.

  ![screenshot](/Users/olaf/Desktop/upload react--Screen Shot 2018-04-30 at 16.04.30.png)

  * Click on the **Users** tab.

  * Click the Add database user button.

  ![screenshot](/Users/olaf/Desktop/upload react--Screen Shot 2018-04-30 at 16.04.39.png)

* This opens an Add new database user form.

  * Complete form and click **Create**.

  ![screenshot](/Users/olaf/Desktop/upload react--Screen Shot 2018-04-30 at 16.05.41.png)

* You'll be returned to the home screen.

  * Click on the **Collections** tab.

  * Click the Add collection button.
  
  ![screenshot](/Users/olaf/Desktop/upload react--Screen Shot 2018-05-01 at 13.56.57.png)

* This opens an Add new collection form.

  * Complete form and click **Create**.
  
  ![screenshot](/Users/olaf/Desktop/upload react--Screen Shot 2018-05-01 at 13.57.14.png)

You now have the URL of a database you can use for development along with a username and password to access it, and a collection to store your favourite videos in. The URL should be something along the lines of `mongodb://<db-user>:<db-password>@<user-domain>.mlab.com:<port>/<db-name>`.

### Connect to our database

Now we have our database setup and a URL to connect to, we need to connect from our app. To do this, we need to edit our `index.js` for the first time in a while! But first we need to install `mongoose`.

```bash
yarn add mongoose
```

Now edit `index.js` and add the following code.

```js
// ...

const mongoose = require('mongoose');
mongoose.connect('mongodb://<db-user>:<db-password>@<user-domain>.mlab.com:<port>/<db-name>');
mongoose.Promise = global.Promise;
mongoose.connection.on('error', console.error.bind(console, 'MongoDB connection error:'));

const corsOptions = {
  // ...
};

// ...
```

As long as we get no errors when we try our app now, it would appear we have successfully connected to a MongoDB database instance.

### Define a `video`

We're going to be storing the metadata for a video, along with a `favourite` tag and the `user`, so we need to define the schema and model.

So create a new directory from root called `schemas` and in it create a new file `schemas/video.js`.

```bash
mkdir schemas
touch schemas/video.js
```

In our new file, add this code.

```js
// schemas/video.js
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

const VideoSchema = new Schema({
  title: String,
  link: String,
  pubDate: Date,
  author: String,
  id: String,
  user: String,
  favourite: Boolean
}, { collection : 'videos' });

module.exports = mongoose.model( 'VideoModel', VideoSchema );
```

This file defines our data structure for our video model, which provides us well formed objects as well as validation further down the line. Notice how it's also defining our collection name, here: `{ collection : 'videos' }`. If you've named your collection anything but `videos` on mLab, this will need to reflect that.

### Favouriting a video

To control our favourite videos, we're going to need two new endpoints. These endpoints will receive a HTTP `POST` request with a JSON payload of the video we're going to favourite. This means we need to parse the body, for which we'll need `body-parser`, a npm package that will parse the JSON and apply it to `req.body` as an object for us.

So let's install `body-parser`.

```bash
yarn add body-parser
```

Now we can add our new endpoints. Edit `controllers/videos.js` and add this code.

```js
// controllers/videos.js
// ...
const bodyParser = require('body-parser');

// ...

const toggleFavourite = async (req, favourite) => {
  const VideoModel = require('../schemas/video');
  let video = await VideoModel.findOne({ id: req.body.id, user: req.user.sub }).then(obj => { return obj });

  if (!video) {
    video = new VideoModel(req.body);
  }

  video.favourite = favourite;
  video.save(err => {if (err) console.log(err)});

  return video;
};

router.post('/videos/favourite', auth.required, bodyParser.json(), async (req, res) => {
  const body = await toggleFavourite(req, true);

  res.json(body);
});

router.post('/videos/unfavourite', auth.required, bodyParser.json(), async (req, res) => {
  const body = await toggleFavourite(req, false);

  res.json(body);
});

// ...
```

Here we're adding the `/favourite` and `/unfavourite` endpoints, and both do ***VERY*** similar things. They receive the `video` to action and set the appropriate `favourite` flag on the object before saving it to our MongoDB database. So we've abstracted this into a function expression that can do what we want for both endpoints and it returns the modified video.

### Getting our favourites

Our API is now ready to add and remove our favourites, but we need to be able to return them. Edit `utils/videos.js` and add this code.

```js
// utils/videos.js
// ...

const videos = async (req, res, next) => {
  // ...

  if (req.user !== undefined) {
    const VideoModel = require('../schemas/video');
    const favouriteVideos = await VideoModel.find({ favourite: true, user: req.user.sub }).then(data => { return data; });

    videos = videos.map((video) => {
      return favouriteVideos.find(obj => video.id === obj.id) || video;
    });
  }

  // ...
};

// ...
```

This is going to try and find any favourited videos stored in the database for our user. Any videos in our list that we find in the database are going to be updated with the details from the database. This is a neat little way to preserve the work we've already build, while adding our new functionality.

### The favourite button

To let our users toggle a favourite video (and to highlight our favourites) we're going to add a button to our `Video` component.

Change back to our React app.

```bash
cd www-client
```

Edit `src/components/Video.js` and replace it's contents with the following code.

```js
import React, { Component } from 'react';
import YouTube from 'react-youtube';
import { Card, CardMedia, CardActions } from 'material-ui/Card';
import FlatButton from 'material-ui/FlatButton';
import ActionGrade from 'material-ui/svg-icons/action/grade';

const API = 'http://localhost:3001';

class Video extends Component {
  constructor(props) {
    super(props);

    this.state = {
      video: this.props.video,
    };
  }

  handleFavourite = (video) => {
    const { auth } = this.props;
    if (this.props.auth.isAuthenticated()) {
      const config = {
        headers: {
          'Authorization': `Bearer ${auth.accessToken}`,
          'Accept': 'application/json',
          'Content-Type': 'application/json'
        },
        method: 'post',
        body: JSON.stringify({...video, user: auth.userProfile.sub})
      };

      let route = `${API}/videos/favourite`;

      if (video.favourite) {
        route = `${API}/videos/unfavourite`;
      }

      fetch(route, config)
        .then(response => {
          if (!response.ok) {
            throw Error(response.statusText);
          }

          return response.json();
        })
        .then(data => this.setState({ video: data}));
    }
  };

  render() {
    const { video } = this.state;
    const { isAuthenticated } = this.props.auth;
    const actions = isAuthenticated() ? (
      <CardActions>
        <FlatButton
          label='Favourite'
          onClick={this.handleFavourite.bind(this, video)}
          fullWidth={true}
          secondary={video.favourite}
          labelPosition='before'
          icon={<ActionGrade />}
        />
      </CardActions>
    ) : ( '' );

    return (
      <Card
        style={{
          width: '400px',
          padding: '10px',
          marginRight: '1em',
          marginBottom: '1em',
          textAlign: 'center',
          display: 'inline-block',
        }}>
        <CardMedia
        >
          <YouTube
            opts={{
              width: '380',
              height: '220',
            }}
            videoId={(video.id.split(":").pop())}
          />
        </CardMedia>
        {actions}
      </Card>
    );
  }
}

export default Video;
```

One of the first things you'll notice is that we've also changed this component from a stateless [Functional Component to a Class Component](https://reactjs.org/docs/components-and-props.html) and made `video` part of the component's `state`. When the component's `state` is change, `render()` is called again. This means we can `React` to state changes, like the callback to an action–leading onto our next change.

We've created a function expression `handleFavourite` that receives a `video` and subsequently makes a `favourite` or `unfavourite` request to our API based on the context of what was given to it. I.e. it will favourite a video and unfavourite a favourite video. This function will also update the `state` of the `Video` component with the response from the API.

The state is used to decide whether the `FlatButton` component is highlighted or not, indicating our video has been favourited. Clicking this button will call the `handleFavourite` function, therefore acting as a toggle. Having a click about, we should end up with something similar to the screenshot below.

![screenshot](/Users/olaf/Desktop/upload react--Screen Shot 2018-05-01 at 15.23.02.png)

## Conclusion

Using Auth0, we've built robust authentication into our [React](https://reactjs.org/) application, verifying our users access in the backend so our API remains secure. 

While concentrating on security, [Material UI](http://www.material-ui.com/) and [React Router 4](https://reacttraining.com/react-router/) have allowed us to deliver a neat user experience and nicely structured code.

[Express](https://expressjs.com/) has given us the opportunity to produce a tidy API with a minimal footprint and it's [middleware](https://expressjs.com/en/guide/using-middleware.html) has allowed us to nicely separate concerns.

In an attempt to keep the complexity of our React application small, I've avoided Redux or complicating the build process too much. In the real world, Flux implementations like [Redux can be a rabbit hole of complexity](https://hackernoon.com/avoiding-accidental-complexity-when-structuring-your-app-state-6e6d22ad5e2a), but supremely powerful.

[Material UI](http://www.material-ui.com/) isn't a finished product yet. Initially they wanted to keep it as a 'component' library, to provide React components, as an implementation of the Material Design guidelines. This mean't they avoided some more basic desires of a UI framework, like Grids and layouts. However, it seems that [Material UI v1 is coming](https://material-ui-next.com/) and it appears it's a mature, fully fledged framework complete will all bells and whistles. I am very much looking forward to V1's full release and an opportunity to reproduce our React client application in the new version.

In our next article, we'll be producing a React Native application, extending our API's functionality right on to our handsets. 