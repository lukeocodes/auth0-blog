## Aside: Securing React Apps with Auth0

As you will learn in this section, you can easily secure your React applications with Auth0, a global leader in Identity-as-a-Service (IDaaS) that provides thousands of enterprise customers with modern identity solutions. Alongside with the classic [username and password authentication process](https://auth0.com/docs/connections/database), Auth0 allows you to add features like [Social Login](https://auth0.com/learn/social-login/), [Multifactor Authentication](https://auth0.com/docs/multifactor-authentication), [Passwordless Login](https://auth0.com/passwordless), and [much more](https://auth0.com/docs/getting-started/overview) with just a few clicks.

To follow along the instruction describe here, you will need an Auth0 account. If you don't have one yet, now is a good time to <a href="https://auth0.com/signup" data-amp-replace="CLIENT_ID" data-amp-addparams="anonId=CLIENT_ID(cid-scope-cookie-fallback-name)">sign up for a free Auth0 account</a>.

Also, if you want to follow this section in a clean environment, you can easily create a new React application with just one command:

```bash
npx create-react-app react-auth0
```

Then, you can move into your new React app (which was created inside a new directory called `react-auth0` by the `create-react-app` tool), and start working as explained in the following subsections.

### Setting Up an Auth0 Application

To represent your React application in your Auth0 account, you will need to create an [Auth0 Application](https://auth0.com/docs/applications). So, head to [the Applications section on your Auth0 dashboard](https://manage.auth0.com/#/applications) and proceed as follows:

1. click on the [_Create Application_](https://manage.auth0.com/#/applications/create) button;
2. then define a _Name_ to your new application (e.g., "React Demo");
3. then select _Single Page Web Applications_ as its type.
4. and hit the _Create_ button to end the process.

After creating your application, Auth0 will redirect you to its _Quick Start_ tab. From there, you will have to click on the _Settings_ tab to whitelist some URLs that Auth0 can call after the authentication process. This is a security measure implemented by Auth0 to avoid the leaking of sensitive data (like [ID Tokens](https://auth0.com/docs/tokens/id-token)).

So, when you arrive at the _Settings_ tab, search for the _Allowed Callback URLs_ field and add `http://localhost:3000/callback` into it. For this tutorial, this single URL will suffice.

That's it! From the Auth0 perspective, you are good to go and can start securing your React application.

### Dependencies and Setup

To secure your React application with Auth0, there are only three dependencies that you will need to install:

- [`auth0.js`](https://github.com/auth0/auth0.js): This is the default library to integrate web applications with Auth0.
- [`react-router`](https://github.com/ReactTraining/react-router): This is the de-facto library when it comes to routing management in React.
- [`react-router-dom`](https://github.com/ReactTraining/react-router/tree/master/packages/react-router-dom): This is the extension to the previous library to web applications.

To install these dependencies, move into your project root and issue the following command:

```bash
npm install --save auth0-js react-router react-router-dom
```

> **Note:** As you want the best security available, you are going to rely on the [Auth0 login page](https://auth0.com/docs/hosted-pages/login). This method consists of redirecting users to a login page hosted by Auth0 that is easily customizable right from [your Auth0 dashboard](https://manage.auth0.com/). If you want to learn why this is the best approach, check [the _Universal vs. Embedded Login_ article](https://auth0.com/docs/guides/login/universal-vs-embedded).

After installing all three libraries, you can create a service to handle the authentication process. You can call this service `Auth` and create it in the `src/Auth/` directory with the following code:

```js
// src/Auth/Auth.js
import auth0 from 'auth0-js';

export default class Auth {
  constructor() {
    this.auth0 = new auth0.WebAuth({
      // the following three lines MUST be updated
      domain: '<AUTH0_DOMAIN>',
      audience: 'https://<AUTH0_DOMAIN>/userinfo',
      clientID: '<AUTH0_CLIENT_ID>',
      redirectUri: 'http://localhost:3000/callback',
      responseType: 'token id_token',
      scope: 'openid profile'
    });

    this.getProfile = this.getProfile.bind(this);
    this.handleAuthentication = this.handleAuthentication.bind(this);
    this.isAuthenticated = this.isAuthenticated.bind(this);
    this.login = this.login.bind(this);
    this.logout = this.logout.bind(this);
    this.setSession = this.setSession.bind(this);
  }

  getProfile() {
    return this.profile;
  }

  handleAuthentication() {
    return new Promise((resolve, reject) => {
      this.auth0.parseHash((err, authResult) => {
        if (err) return reject(err);
        console.log(authResult);
        if (!authResult || !authResult.idToken) {
          return reject(err);
        }
        this.setSession(authResult);
        resolve();
      });
    })
  }

  isAuthenticated() {
    return new Date().getTime() < this.expiresAt;
  }

  login() {
    this.auth0.authorize();
  }

  logout() {
    // clear id token and expiration
    this.idToken = null;
    this.expiresAt = null;
  }

  setSession(authResult) {
    this.idToken = authResult.idToken;
    this.profile = authResult.idTokenPayload;
    // set the time that the id token will expire at
    this.expiresAt = authResult.expiresIn * 1000 + new Date().getTime();
  }
}
```

The `Auth` service that you just created contains functions to deal with different steps of the sign in/sign up process. The following list briefly summarizes these functions and what they do:

- `getProfile`: This function returns the profile of the logged-in user.
- `handleAuthentication`: This function looks for the result of the authentication process in the URL hash. Then, the function processes the result with the `parseHash` method from `auth0-js`.
- `isAuthenticated`: This function checks whether the expiry time for the user's ID token has passed.
- `login`: This function initiates the login process, redirecting users to the login page.
- `logout`: This function removes the user's tokens and expiry time.
- `setSession`: This function sets the user's ID token, profile, and expiry time.

Besides these functions, the class contains a field called `auth0` that is initialized with values extracted from your Auth0 application. It is important to keep in mind that you **have to** replace the `<AUTH0_DOMAIN>` and `<AUTH0_CLIENT_ID>` placeholders that you are passing to the `auth0` field.

> **Note:** For the `<AUTH0_DOMAIN>` placeholders, you will have to replace them with something similar to `your-subdomain.auth0.com`, where `your-subdomain` is the subdomain you chose while creating your Auth0 account (or your Auth0 tenant). For the `<AUTH0_CLIENT_ID>`, you will have to replace it with the random string copied from the _Client ID_ field of the Auth0 Application you created previously.

Since you are using the Auth0 login page, your users are taken away from the application. However, after they authenticate, users automatically return to the callback URL that you set up previously (i.e., `http://localhost:3000/callback`). This means that you need to create a component responsible for this route.

So, create a new file called `Callback.js` inside `src/Callback` (i.e., you will need to create the `Callback` directory) and insert the following code into it:

```jsx
// src/Callback/Callback.js
import React from 'react';
import { withRouter } from 'react-router';

function Callback(props) {
  props.auth.handleAuthentication().then(() => {
    props.history.push('/');
  });

  return (
    <div>
      Loading user profile.
    </div>
  );
}

export default withRouter(Callback);
```

This component, as you can see, is responsible for triggering the `handleAuthentication` process and, when the process ends, for pushing users to your home page. While this component processes the authentication result, it simply shows a message saying that it is _loading the user profile_.

After creating the `Auth` service and the `Callback` component, you can refactor your `App` component to integrate everything together:

```jsx
// src/App.js

import React from 'react';
import {withRouter} from 'react-router';
import {Route} from 'react-router-dom';
import Callback from './Callback/Callback';
import './App.css';

function HomePage(props) {
  const {authenticated} = props;

  const logout = () => {
    props.auth.logout();
    props.history.push('/');
  };

  if (authenticated) {
    const {name} = props.auth.getProfile();
    return (
      <div>
        <h1>Howdy! Glad to see you back, {name}.</h1>
        <button onClick={logout}>Log out</button>
      </div>
    );
  }

  return (
    <div>
      <h1>I don't know you. Please, log in.</h1>
      <button onClick={props.auth.login}>Log in</button>
    </div>
  );
}

function App(props) {
  const authenticated = props.auth.isAuthenticated();

  return (
    <div className="App">
      <Route exact path='/callback' render={() => (
        <Callback auth={props.auth}/>
      )}/>
      <Route exact path='/' render={() => (
        <HomePage
          authenticated={authenticated}
          auth={props.auth}
          history={props.history}
        />)
      }/>
    </div>
  );
}

export default withRouter(App);
```

In this case, you are actually defining two components inside the same file (just for the sake of simplicity). You are defining a `HomePage` component that shows a message with the name of the logged-in user (that is, when the user is logged in, of course), and a message telling unauthenticated users to log in.

Also, this file is making the `App` component responsible for deciding what component it must render. If the user is requesting the home page (i.e., the `/` route), the `HomePage` component is shown. If the user is requesting the callback page (i.e., `/callback`), then the `Callback` component is shown.

Note that you are using the `Auth` service in all your components (`App`, `HomePage`, and `Callback`) and also inside the `Auth` service. As such, you need to have a global instance for this service, and you have to include it in your `App` component.

So, to create this global `Auth` instance and to wrap things up, you will need to update your `index.js` file as shown here:

```js
// src/index.js

import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
import Auth from './Auth/Auth';
import './index.css';
import App from './App';
import registerServiceWorker from './registerServiceWorker';

const auth = new Auth();

ReactDOM.render(
  <BrowserRouter>
    <App auth={auth} />
  </BrowserRouter>,
  document.getElementById('root')
);
registerServiceWorker();
```

After that, you are done! You just finished securing your React application with Auth0. If you take your app for a spin now (`npm start`), you will be able to authenticate yourself with the help of Auth0, and you will be able to see your React app show your name (that is, if your identity provider does provide a name).

If you are interested in learning more, please, refer to [the official React Quick Start guide](https://auth0.com/docs/quickstart/spa/react/01-login) to see, step by step, how to properly secure a React application. Besides the steps shown in this section, the guide also shows:

- [How to manage profile information of authenticated users](https://auth0.com/docs/quickstart/spa/react/02-user-profile).
- [How to properly call an API](https://auth0.com/docs/quickstart/spa/react/03-calling-an-api).
- [How to control which routes users can see/interact with](https://auth0.com/docs/quickstart/spa/react/04-authorization).
- [How to deal with the expiry time of users' tokens](https://auth0.com/docs/quickstart/spa/react/05-token-renewal).
