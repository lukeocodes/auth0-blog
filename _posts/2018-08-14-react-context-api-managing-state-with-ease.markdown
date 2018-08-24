---
layout: post
title: "React Context API: Managing State with Ease"
description: "Managing state with the new React Context API is very easy. Learn what the differences with Redux are and how to use it in this practical tutorial."
date: 2018-08-14 08:30
category: Technical Guide, Frontend, React
author:
  name: "Abdulazeez Adeshina"
  url: "https://twitter.com/kvng_zeez"
  mail: "laisibizness@gmail.com"
  avatar: "https://cdn.auth0.com/blog/guest-authors/abdulazeez.png"
design:
  bg_color: "#1A1A1A"
  image: https://cdn.auth0.com/blog/logos/react.png
tags:
- react
- context-api
- redux
- state
- state-management
- frontend
- javascript
related:
- 2017-11-28-redux-practical-tutorial
- 2018-03-06-time-slice-suspense-react16
- 2018-02-15-developing-web-apps-with-asp-dot-net-core-2-dot-0-and-react-part-1
---

**TL;DR:** The React Context API isn't a new thing on React's ecosystem. However, the React's `16.3.0` release [brought a lot of improvements to this API](https://auth0.com/blog/whats-new-in-react-16-3/). These improvements are so overwhelming that they greatly reduce the need for Redux and other advanced state management libraries. In this article, you will learn, through a practical tutorial, how the new React Context API replaces the need for Redux on small React applications.

{% include tweet_quote.html quote_text = "Learn how to migrate from Redux to the new React Context API in this practical tutorial." %}

## Quick Review on Redux

Before diving into the React Context API, we need to do a quick review on Redux, so we can compare both. [Redux is a JavaScript library that facilitates state management](https://redux.js.org). Redux is not tied to React itself. Developers from all around the world have been using Redux with popular JavaScript frontend frameworks such as _React_ and _Angular_.

To be clear, in this context, state management means handling changes that occur upon a particular event that occurs on a Single Page App (SPA). For example, events like the click of a button or an async message coming from the server can trigger changes to the app's state.

In Redux, particularly, there are a few things that you have to keep in mind:

1. The state of the entire app is stored in a single object (known as the source of truth).
2. To change the state, you need to dispatch `actions` that describes what needs to happen.
3. You cannot change properties of objects or make changes to existing arrays. In Redux, you must always return a new reference to a new object or a new array.

If you are not familiar with Redux and you want to learn more, please, check [this practical tutorial on Redux](https://auth0.com/blog/redux-practical-tutorial/).

## React Context API Introduction

The React Context API provides a way to pass data through the component tree without having to pass `props` down manually to every level. In React, data is often passed from a parent to its child component as a property.

Using the new React Context API depends on three main steps:

1. Passing the initial state to `React.createContext`. This function then returns an object with a `Provider` and a `Consumer`.
2. Using the `Provider` component at the top of the tree and making it accept a prop called `value`. This value can be anything!
3. Using the `Consumer` component anywhere below the Provider in the component tree to get a subset of the state.

As you can see, the concepts involved are actually not that different from Redux. The fact is, even Redux uses the React Context API underneath its public API. However, only recently the Context API reached a level of maturity high enough to be used in the wild.

## Creating a React App with Redux

As mentioned, the goal of this article is to show you how the new Context API can replace Redux for small apps. Therefore, you will start by creating a simple React app with Redux and, after that, you will learn how to remove this state management library so you can take advantage of the React Context API.

The sample application you will build is an app that handles a list of some popular foods and their origin. This app will also include a search functionality to enable users to filter the list based on some keyword.

In the end, you will have an app that looks like this:

### Project Requirements

As this article uses only React and some NPM libraries, you will need nothing else than Node.js and NPM installed in your development machine. If you don't have Node.js and NPM yet, check out the [official installation procedures](https://nodejs.org/en/download/) to install both.

After installing these dependencies, you will need to install the `create-react-app` tool. This tool helps developers getting started with React. So, to install it, open a terminal and run the following command:

```bash
npm i -g create-react-app
```

### Scaffolding the React App

With `create-react-app` installed, you will have to move to the directory where you want to put your project and execute the following command:

```bash
create-react-app redux-vs-context
```

After a few seconds, `create-react-app` will have finished creating your app. So, after that, you can move into the new directory created by this tool and install Redux:

```bash
# move into your project
cd redux-vs-context

# install Redux
npm i --save redux react-redux
```

> **Note:** `redux` is the main library and `react-redux` is a library that facilitates the interaction between React and Redux. In short, the latter acts as a proxy between React and Redux.

### Developing React Apps with Redux

Now that you have your React app structured and that you installed Redux, open your project in your preferred IDE. From there, you will create three files into the `src` directory:

- `foods.json`: This file will hold a static array of foods and their origin.
- `reducers.js`: This file will manage the state of the Redux version of your app.
- `actions.js`: This file that will hold the functions that will trigger changes in the state of the Redux version of your app.

So, to start, you can open the `foods.json` file and add the following content to it:

```json
[
  {
    "name": "Chinese Rice",
    "origin": "China",
    "continent": "Asia"
  },
  {
    "name": "Amala",
    "origin": "Nigeria",
    "continent": "Africa"
  },
  {
    "name": "Banku",
    "origin": "Ghana",
    "continent": "Africa"
  },
  {
    "name": "Pão de Queijo",
    "origin": "Brazil",
    "continent": "South America"
  },
  {
    "name": "Ewa Agoyin",
    "origin": "Nigeria",
    "continent": "Africa"
  }
]
```

As you can see, there is nothing special about this file. It is just an array of different food from different countries.

After defining the `foods.json` file, you can focus on defining your Redux store. To recap, the `store` is the place where you keep the single source of truth of the state of your app. So, open the `reducers.js` file and add the following code to it:

```js
import Food from './foods';

const initialState = {
  food: Food,
  searchTerm: '',
};

export default function reducer(state = initialState, action) {
  // switch between the action type
  switch (action.type) {
    case 'SEARCH_INPUT_CHANGED':
      const {searchTerm} = action.payload;
      return {
        ...state,
        searchTerm: searchTerm,
        food: searchTerm ? Food.filter(
          (food) => (food.name.toLowerCase().indexOf(searchTerm.toLowerCase()) > -1)
        ) : Food,
      };
    default:
      return state;
  }
}
```

In the code above, you can see that the `reducer` function receives two parameters: `state` and `action`. When you start your React application, this function will get the `initialState` defined right before it and, when you dispatch instances of an action, this function will get the current state (not the `initialState` anymore). Then, based on the contents of these actions, the `reducer` function will generate a new state for your app.

Next, you have to define what these actions are. Actually, to keep things simple, you will define a single action that will be triggered when users input a search term in your app. So, open the `actions.js` file and insert the following code into it:

```js
function searchTermChanged(searchTerm) {
  return {
    type: 'SEARCH_INPUT_CHANGED',
    payload: {searchTerm},
  };
}

export default {
  searchTermChanged,
};
```

With this `action` creator in place, the next thing you need to do is to wrap your `App` component into the `Provider` component that is available on `react-redux`. This provider is responsible for making your single source of truth (i.e., the `store`) to your React app.

To use this provider, first, you will create your app's `store` using the `initialState` defined in the `reducers.js` file. Then, you will pass this `store` to your `App` with the help of `Provider`. To accomplish these tasks, you will have to open the `index.js` file and replace its contents with:

```js
import React from 'react';
import ReactDOM from 'react-dom';
import {Provider} from 'react-redux';
import {createStore} from 'redux';
import reducers from './reducers';
import App from './App';

// Creating the store using the reducers info.
// That's because reducers are the building blocks of a Redux Store.
const store = createStore(reducers);

ReactDOM.render(
  <Provider store={store}>
    <App/>
  </Provider>,
  document.getElementById('root')
);
```

That's it! You just finished configuring Redux in your React app. Now, you have to implement the UI (User Interface), so your users can use the features implemented in this section.

### Building the React Interface

Now that you have the core of your application ready to go, you can focus on building your user interface. For that, open your `App.js` file and replace its contents with this:

{% highlight html %}
{% raw %}
import React from 'react';
import {connect} from 'react-redux';
import actions from './actions';
import './App.css';

function App({food, searchTerm, searchTermChanged}) {
  return (
    <div>
      <div className="search">
        <input
          type="text"
          name="search"
          placeholder="Search"
          value={searchTerm}
          onChange={e => searchTermChanged(e.target.value)}
        />
      </div>
      <table>
        <thead>
        <tr>
          <th>Name</th>
          <th>Origin</th>
          <th>Continent</th>
        </tr>
        </thead>
        <tbody>
        {food.map(theFood => (
          <tr key={theFood.name}>
            <td>{theFood.name}</td>
            <td>{theFood.origin}</td>
            <td>{theFood.continent}</td>
          </tr>
        ))}
        </tbody>
      </table>
    </div>
  );
}

export default connect(store => store, actions)(App);
{% endraw %}
{% endhighlight %}

For those not used to Redux, the only thing that they might not be familiar with is the `connect` function used to encapsulate the `App` component. This function is actually a [High Order Component (HOC)](https://reactjs.org/docs/higher-order-components.html) that acts as the glue between your app and Redux.

If you run your app now, you will be able to use it in your browser:

```bash
npm run start
```

However, as you can see, the app right now is ugly. So, to make it look a little bit better, you can open the `App.css` file and replace its contents with: 

```css
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 15px;
  line-height: 25px;
}

th {
  background-color: #eee;
}

td, th {
  text-align: center;
}

td:first-child {
  text-align: left;
}

input {
  min-width: 300px;
  border: 1px solid #999;
  border-radius: 2px;
  line-height: 25px;
}
```

![React app implemented with Redux](https://cdn.auth0.com/blog/react-context-api/react-app-with-redux.png)

Done! You now have a basic React and Redux app and can start learning about how to migrate to the Context API.

## Implementing React Apps with React Context API

In this section, you will learn how to migrate the Redux version of your app to the React Context API.

Fortunately, as you will see, you won't really need to do a lot of refactoring to switch between Redux and the Context API.

For starter, you will have to remove every trace of Redux from your app. For that, go to your terminal and remove both the `redux` and `react-redux` libraries:

```bash
npm rm redux react-redux
```

After that, you can remove the `import` statements that reference these libraries. So, open the `App.js` file and remove these lines:

```js
import {connect} from 'react-redux';
import actions from './actions';
```

Then, still in this file, replace the last line (the one that starts with `export default`) with this:

```js
export default App;
```

With these changes in place, you can rewrite your app with the Context API.

### Migrating from Redux to React Context API

To convert the previous app from a Redux powered app to using the Context API, you will need a context to store the app's data (this context will replace the Redux Store). Also, you will need a `Context.Provider` component which will have a `state`, a `props`, and a normal React component lifecycle.

Therefore, you will need to create a `providers.js` file in the `src` directory and add the following code to it:

{% highlight html %}
{% raw %}
import React from 'react';
import Food from './foods';

const DEFAULT_STATE = { allFood: Food, searchTerm: '' };

export const ThemeContext = React.createContext(DEFAULT_STATE);

export default class Provider extends React.Component {
  state = DEFAULT_STATE;
  searchTermChanged = searchTerm => {
    this.setState({searchTerm});
  };

  render() {
    return (
      <ThemeContext.Provider value={{
        ...this.state,
        searchTermChanged: this.searchTermChanged,
      }}> {this.props.children} </ThemeContext.Provider>);
  }
}
{% endraw %}
{% endhighlight %}

The `Provider` class defined in the code above is responsible for encapsulating other components inside the `ThemeContext.Provider`. By doing that, you enable these components to have access to your app's state and to the `searchTermChanged` function that provides a way to change this state.

To consume these values later in the component tree, you will need to initiate a `ThemeContext.Consumer` component. This component will need a render function that will receive the above value `props` as arguments to use at will.

So, next, you need to create a filed called `consumer.js` in the `src` directory and write the following code into it:

{% highlight html %}
{% raw %}
import React from 'react';
import {ThemeContext} from './providers';

export default class Consumer extends React.Component {
  render() {
    const {children} = this.props;

    return (
      <ThemeContext.Consumer>
        {({allFood, searchTerm, searchTermChanged}) => {
          const food = searchTerm
            ? allFood.filter(
              food =>
                food.name.toLowerCase().indexOf(searchTerm.toLowerCase()) > -1
            )
            : allFood;

          return React.Children.map(children, child =>
            React.cloneElement(child, {
              food,
              searchTerm,
              searchTermChanged,
            })
          );
        }}
      </ThemeContext.Consumer>
    );
  }
}
{% endraw %}
{% endhighlight %}

Now, to finalize the migration, you will open your `index.js` file, and inside the `render()` function, wrap the `App` component with the `Consumer` component. Also, you will wrap the `Consumer` inside the `Provider` component. Exactly as shown here:

```js
import React from 'react';
import ReactDOM from 'react-dom';
import Provider from './providers';
import Consumer from './consumer';
import App from './App';

ReactDOM.render(
  <Provider>
    <Consumer>
      <App />
    </Consumer>
  </Provider>,
  document.getElementById('root')
);
```

Done! You just finished migrating from Redux to the React Context API. If you run your app now, you will see that the whole thing is working just like before. The difference now is that your app is not using Redux more.

{% include tweet_quote.html quote_text = "The new React Context API s a great alternative to Redux in small React applications." %}

{% include asides/react.markdown %}

## Conclusion

Redux is an **advanced** state management library that should be used when building large scale React apps. The Context API, on the other hand, can be used in small-scale React apps where byte-sized changes are made. By using the Context API, you do not have to write a lot of code such as `reducers`, `actions`, etc. to work out the logic exhibited by state changes. 
