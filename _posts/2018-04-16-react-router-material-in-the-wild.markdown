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
mkdir ~/Projects/auth0-react-material/ && cd "$_"
```

> **Note:** Here's a nifty bash technique, `"$_"` contains the last argument of the previous command, so you can `mkdir ~/Projects/auth0-react-material/` and `cd ~/Projects/auth0-react-material/` all in one command.

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
-   }
+   },
+   "scripts": {
+     "dev": "nodemon index.js"
+   }
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
// index.js
const express = require('express');
const app = express();

const videos = require('./controllers/videos');
app.use('/', videos);

const port = process.env.PORT || 3001;
app.listen(port, () => console.log(`Listening on port ${port}`));
```

## create react app (create-react-app)

Now we're going to create our react app to consume our backend express app.

```bash
create-react-app www-client
```

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-18 at 10.23.34.png)

and test that everything is working

```bash
cd www-client
yarn start
```

From here on out we'll be using the `www-client` directory as our current Working Directory. We'll move back to the express application directory when we need to.

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-18 at 10.49.02.png)

Now just to tidy up a bit and remove some boilerplate.

Create a components directory, where our componenets will live

```bash
mkdir src/components
```

and remove some files we're not going to need for a little bit

```bash
rm src/App.css src/App.test.js src/index.css src/logo.svg
```

move our `App.js` componenet into our components directory

```bash
mv src/App.js src/components/
```

and our directories will look something like this, which is a little tidier

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-18 at 11.04.52.png)

But our app won't run now. Let's fix it.

Edit our `src/index.js` file so it looks like below

```diff
// src/index.js
  import React from 'react';
  import ReactDOM from 'react-dom';
- import './index.css';
- import App from './App';
+ import App from './components/App';
  import registerServiceWorker from './registerServiceWorker';
...
```

and edit our `src/components/App.js` like this

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

Now everything should still work, if not a bit uglier. A good base for whats next.

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-18 at 11.13.31.png)

### setup routes with react router 4

```bash
yarn add react-router-dom
```

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-18 at 11.15.57.png)

edit `src/index.js` to use react router. it should now look something like this

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

`Main` is going to be the home of our react routing, where to choose which components to pick based on our route. `Header` is where we'll show our links where we'll interact with react router. `Home` will be where we put the initial content we want to load.

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

and now edit `src/components/App.js` to use our new components. it should look like the following code

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

nice and neat so far! let's see if it's working.

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-18 at 12.12.55.png)

now we're going to create another page, something for us to route to

```bash
touch src/components/Videos.js
```

and give it the following contents

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

and now we can setup some routing to allow us to switch between `Home` and `Videos`, edit `src/components/Main.js` to look like the following

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

another quick check, make sure we haven't broken anything

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-18 at 12.27.37.png)

But we don't have any links yet! so lets create a quick nav menu in our `Header` component. So edit `src/components/Header.js` with the code shown here

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

and check that our nav menu works with our router

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-18 at 12.50.45.gif)

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