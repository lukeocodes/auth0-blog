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
mkdir auth0-react-material/ && cd "$_"
```

> **Note:** Here's a nifty bash technique, `"$_"` contains the last argument of the previous command, so you can `mkdir auth0-react-material/` and `cd auth0-react-material/` all in one command.

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

// ...

app.get('/videos', (req, res) => {
  res.json({'videos': []});
});

// ...

```

#### fetch the content from remote

```bash
yarn add rss-parser
```

```js
// index.js

// ...

const parser = new (require('rss-parser'))();

const asyncVideosMiddleware = async (req, res, next) => {
  req.data = await parser.parseURL('https://www.youtube.com/feeds/videos.xml?channel_id=UCUlQ5VoIzE_kFbYjzUwHTKA');
  next();
};

app.get('/videos', asyncVideosMiddleware, (req, res) => {
  res.json(req.data);
});

// ...

```

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-17 at 11.06.26.png)

#### cache content with memory-cache

```bash
yarn add memory-cache
```

```js
// index.js

// ...

const cache = require('memory-cache');

// ...

const asyncVideosMiddleware = async (req, res, next) => {
  let videos = cache.get('videos');

  if (videos === null) {
    videos = await parser.parseURL('https://www.youtube.com/feeds/videos.xml?channel_id=UCUlQ5VoIzE_kFbYjzUwHTKA');
    cache.put('videos', videos, 1000 * 60 * 60); // 1 hour
  }

  req.data = videos;
  next();
};

// ...

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

// ...

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

`material-ui` needs the `Roboto` font, for which we'll need the `webfontloader` library. Let's install those and set them up.

```bash
yarn add material-ui webfontloader
```

and now use it to load `Roboto` by editing `src/index.js` and adding the following code

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

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-18 at 13.30.35.png)

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

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-18 at 14.34.48.png)

Now to tackle our `Header` component, we'll make use of the [Material UI `AppBar` component](http://www.material-ui.com/#/components/app-bar).

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

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-18 at 19.59.34.png)

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

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-18 at 20.26.05.gif)

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
}

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
import {Card, CardMedia} from 'material-ui/Card';

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

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-24 at 12.36.50.png)

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

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-24 at 12.49.05.png)

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

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-24 at 12.33.42.png)

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

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-24 at 13.01.15.png)

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
    this.props.history.push('/');
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
    this.props.history.push('/');
  }

  deauthorized() {
    this.props.history.push('/');
  }
```

The `authorized` callback function updates the `App` component state. So we've given our `Auth` class the ability to nudge our `React` app once authentication has taken place. Both `authorized` and `deauthorized` are then responsible for pushing us back to the `Home` route once done.

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
        <Route path="/callback" render={(props) => <Callback auth={auth} {...props} />}/>
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

  handleLogin = () => this.props.auth.login();

  handleLogout = () => this.props.auth.logout();

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

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-24 at 15.00.37.gif)

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

![screenshot](/Users/olaf/Desktop/Screen Shot 2018-04-24 at 16.03.37.png)

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
const issuer = `https://blog-posts.eu.auth0.com/`;
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

## user functionality endpoints in express
### 'mark as read', comment
#### mongoose for storage in mlab
#### sensible file layout
##### schemas
##### models

## conclusion

## next steps (a react native guide)