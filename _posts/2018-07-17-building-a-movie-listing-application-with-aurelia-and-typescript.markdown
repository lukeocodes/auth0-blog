---
layout: post
title: "Building a Movie Listing Application with Aurelia and TypeScript"
description: "Learn how to build a movie watch list demo application using TypeScript, the Aurelia framework, and user authentication with Auth0."
date: 2018-07-17 08:30
category: Technical Guide, Frontend, Aurelia
author:
  name: Steve Hobbs
  url: https://stevescodingblog.co.uk
  mail: elkdanger@gmail.com
  avatar: https://en.gravatar.com/userimage/3841188/bc8fc1f1ebb326d59bab456cac894bdf.jpeg
design:
  bg_color: "#6E4D9A"
  image: https://cdn.auth0.com/blog/logos/aurelia.png
tags:
- aurelia
- spa
- typescript
- auth0
- movie-list
- frontend
related:
- 2018-03-29-vuejs-kanban-board-the-development-process
- 2018-02-06-developing-games-with-react-redux-and-svg-part-1
---

**TL;DR:** In this article, we're going to use the [Aurelia JavaScript framework](https://aurelia.io/) to build a TypeScript application that lets you search for movies or pick from a list of current popular movies, and add them to a 'watch later' list. We're also going to use Auth0 to require authentication for movie list management.

If you would like to follow along, the source code for this article is [available on GitHub.com](https://github.com/elkdanger/movielist). There is also an [online demo application](https://movies.elkdanger.co.uk) if you'd like to play around with what we're going to build here.

{% include tweet_quote.html quote_text="Learn how to use Aurelia to build a movie listing application with ease." %}

---

If, like me, you're always picking up on new movies that you need to watch, but never have the time for — or forget what they were — then this app is for you! Today we're going to build a movie—tracker app that lets you keep a record of those movies that you want to watch without having to remember what they were. Here's a breakdown of the things we'll achieve in this article:

* We'll use the [Aurelia JavaScript framework](https://aurelia.io/) and its [CLI](https://aurelia.io/docs/build-systems/aurelia-cli/) to generate a new Aurelia application that uses [TypeScript](https://www.typescriptlang.org/) and [Webpack](https://webpack.js.org/)
* Our app will have three screens — the list of movies we've added (our "watch list"), a list of currently trending movies, and a search results page
* We'll connect to the [The Movie DB](https://www.themoviedb.org/) to get our movie information
* We'll protect the feature to add a movie to your watch list behind an Auth0 login, and also add the ability to log in and out of your application

To illustrate, here's what this will look like once it's done:

![User's watchlist demo app screenshot](https://cdn.auth0.com/blog/aurelia-movies/watch-list.png)

![Popular movies demo app screenshot](https://cdn.auth0.com/blog/aurelia-movies/popular-movies-screenshot.png)

## A brief intro to Aurelia

Aurelia is a modular JavaScript framework designed to remove the pain of building and configuring your application, allowing you to focus on your important application features and business logic. It includes everything you need out—of—the—box to build modern JavaScript applications in the browser, on the desktop or mobile, with support for many popular JavaScript variants including ES5, ES2016 and TypeScript.

## Prerequisites

The only thing you need to follow along with this article is Node 4.x or above, which is the minimum requirement for the Aurelia CLI.

## Setting up the application

Let's begin by creating our application using the Aurelia CLI. This tool will help us to set up a boilerplate application using sensible defaults (chosen by us), giving us a large head start in getting our application up and running. We won't have to worry about the detail of how it is configured — we'll be able to just start writing our app.

To start, we need to install the CLI tool. Assuming at this point you have installed Node version 4.x or above, you can install it by opening up your terminal window and using NPM to install the tool locally:

```bash
npm install -g aurelia-cli
```

Next, still inside the terminal, navigate to a place on your computer where you want the project to live. You can then invoke the Aurelia CLI to generate the beginnings of your app:

```bash
au new
```

The CLI tool will then guide you through the creation of your application. The first question asks you for the name of your application. I called mine 'movielist', but you can call it whatever you want.

The next question, however, asks you for a choice of default setup — choose '2. Default TypeScript' here, as that's what we'll be using in this article. This will provide you with a sensible configuration for a TypeScript application that is built using Webpack.

Next you'll just be asked to confirm your choices, so go ahead and choose "1. Yes" to create the project.

For brevity, the project configuration that has been generated for me looks like this:

```bash
Project Configuration

    Name: movielist
    Platform: Web
    Bundler: Webpack
    Loader: None
    Transpiler: TypeScript
    Markup Processor: Minimal Minification
    CSS Processor: None
    Unit Test Runner: Jest
    Unit Test Runner: Karma
    Integration Test Runner: None
    Editor: Visual Studio Code
```

Finally, you'll be asked if you want to install the project dependencies. This simply performs an `npm install` operation on the project directory and installs all the packages it needs, so select 'yes' for this option.

Once Aurelia has finished installing all of your dependencies, you should be left with a fully working application. Just to check that it's working, move into your project directory from the terminal (`cd movielist`) and then run the project using `au run --watch`. After a few seconds, Webpack should report that it has compiled successfully and you will able to view your newly created project by visiting [http://localhost:8080](http://localhost:8080 ) in a browser window.

Hopefully, you will see the words 'Hello world!' printed out in a heading tag. If not, go back over the steps in this section and ensure that nothing has been missed out.

> **Note:** I would like to point out at this point that while you can run your project by simply using `au run` (without the `--watch` flag) there is [an open Webpack bug](https://github.com/aurelia/cli/issues/852) at the time of writing which prevents this from working properly. So for the purposes of this article, always run your project using `au run --watch` to make sure that it compiles and starts properly!

## A quick tour around the project

At this point, you can open the project in your favorite editor. I'm using [Visual Studio Code](https://code.visualstudio.com/); while it's generally pretty awesome as an editor, it's particularly great at working with TypeScript and is a perfect fit for this project.

Looking at the file system, we're mainly going to be concerned with the `src` folder, as it's where all of our components, service classes and templates are going to live and will contain the bulk of our code. We are going to dip our toes into a a couple of other places along the way though, primarily:

* We're going to add a custom TypeScript definition file for [The Movie DB library](https://npmjs.org/package/moviedb-promise) we'll be using, which will help us out a lot with using certain types. These live in the `custom_typings` folder
* We're going to add a [Bootstrap 4 skin](https://bootswatch.com/darkly/) into the `index.ejs` file, which is essentially the wrapping HTML for our entire application
* We'll be adding some environment variables into the files in the `aurelia_project/environments` directory, allowing us to customize values for the different environments you might want to deploy to

At it's core though, it has all the files you'd expect for a Node/Webpack project; there is a `package.json` file to manage dependencies, and a `webpack.config.js` file to configure your build. The build configuration is one of the more advanced ones you'll come across — we only need to make a very minor change to it as part of this tutorial — but it can be changed if you so wish.

One thing I would like you to do is remove the `test` folder. While I'm an avid fan of unit testing, it's not something we'll cover as part of this tutorial. Therefore, if you don't remove this folder, you'll find that as you progress through the article, you will hit more and more issues with your unit tests failing as they run automatically whenever Webpack is invoked.

## The Movie Database API

Before we get started on the meat of the application build, let's have a quick look at the API that is going to drive the whole project.

The Movie DB is a huge database of information about movies and TV shows where you can retrieve names, overviews, ratings, cast and poster images. You can also get collections of popular movies and TV shows, some of which we'll be using here.

To get started, you will need to create a free account and apply for an API key. The application is instant, but you do need to give them some basic information about why you want the key and what you're going to use it for. You can do this by going to your account settings page once you've created your account, and going to the 'API' tab. There, [you can click on the _request a Developer API key_ option](https://www.themoviedb.org/settings/api/request) and accept the terms of use. You will find out that the form is quite extensive but don't worry, the validation process occurs instantly.

We won't quite need it yet, but keep a mental note of where to get this key from later when we start putting in the classes we need in order to communicate with the API.

## Start building the demo app

Let's get writing our app. The first thing we're going to do is configure the main application window with some default styles and get the header into place.

From this point, make sure you've got the project running using `au run --watch` and that you are able to view the application in the brower at [http://localhost:8080](http://localhost:8080).

Looking in the `src` folder, you'll see the `app.ts` and `app.html` files. This is essentially the entry point to our application and is where the rest of our application will grow from. To start with, let's create our first component — the header.

To create components, we can make use of the Aurelia CLI tool again by asking it to create the component for us. To invoke the component generator, run this from the command line:

```bash
au generate component Header
```

You will be asked which subfolder you want the component to be created in. You can just press 'enter' if you want the component to be created inside the `src` folder, but I'm going to create components such as this inside a subfolder called 'components', just to keep them nice and organized.

Now if you look inside the `src/components` folder, you'll find `header.html` and `header.ts`. These are the files that make up our header component. Every component is created with some default 'Hello world' content, but we're going to strip that out and replace it with our own markup.

In this case we're going to replace it with some basic navigation markup and come back to it later as we implement all the other features. Replace the contents of `header.html` with the following to create a basic Bootstrap navigation bar:

{% highlight html %}
{% raw %}
<template>

  <nav class="navbar navbar-expand navbar-dark bg-primary justify-content-between">
    <span class="navbar-brand mb-0 h1" href="#">${appTitle}</span>
    <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent"
      aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarSupportedContent">
    </div>    

  </nav>

</template>
{% endraw %}
{% endhighlight %}

So far, this is mostly just Bootstrap's standard markup for a responsive navigation bar. Notice the `${appTitle}` directive in there? We're going to bind that from the 'controller' of this template, which can be found in the `header.ts` file. Replace its contents with the following:

```js
export class Header {    
  appTitle = "Movielist";
  
  constructor() {
  }
}
```

You can see from this example how easy the binding can be between variables that exist on the component class, and displaying them on a template. Not only that, but _changes_ to that variable will also be picked up and surfaced to the template in real time.

Continuing on, open `app.html` in the root of the `src` folder and replace its contents with the following, which will display the Header component on the page:

{% highlight html %}
{% raw %}
<template>
  <require from="./app.css"></require>
  <require from="./components/header"></require>

  <header></header>
</template>
{% endraw %}
{% endhighlight %}

Notice that it brings in `app.css`, which just has some minor styling in it to put some distance in between the header and the content. Create the file `app.css` in the same directory as your app template and give it the following content:

```css
.container {
  margin-top: 2rem;
}
```

Next, let's bring in those Bootswatch styles we were talking about. Open `index.ejs` in the root of the project and add in the stylesheet inside the `head` tag so that it looks like this:

{% highlight html %}
{% raw %}
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title><%- htmlWebpackPlugin.options.metadata.title %></title>
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <base href="<%- htmlWebpackPlugin.options.metadata.baseUrl %>">
    
    <!-- Add styles for Bootswatch here -->
    <link rel="stylesheet" href="https://bootswatch.com/4/darkly/bootstrap.min.css">
    <!-- end Bootswatch -->

    <!-- imported CSS are concatenated and added automatically -->
  </head>
  <body aurelia-app="main">
    <% if (htmlWebpackPlugin.options.metadata.server) { %>
    <!-- Webpack Dev Server reload -->
    <script src="/webpack-dev-server.js"></script>
    <% } %>

    <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js" integrity="sha384-q8i/X+965DzO0rT7abK41JStQIAqVgRVzpbzo5smXKp4YfRvH+8abtTE1Pi6jizo" crossorigin="anonymous"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.3/umd/popper.min.js" integrity="sha384-ZMP7rVo3mIykV+2+9J3UJ46jBk0WLaUAdn689aCwoqbBJiSnjAK/l8WvCWPIPm49" crossorigin="anonymous"></script>
    <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.1.1/js/bootstrap.min.js" integrity="sha384-smHYKdLADwkXOn1EmN1qk/HfnUcbVRZyYmZ4qpPea6sjB/pTJ0euyQp0Mk8ck+5T" crossorigin="anonymous"></script>

  </body>
</html>

{% endraw %}
{% endhighlight %}

Notice that we've also included script tags for Bootstrap and jQuery, so that the responsive navigation bar works correctly across smaller devices.

By the way, for my application I've chosen to use the [Darkly theme](https://bootswatch.com/darkly/), but you can use any of the themes that you like, as all of the markup should be compatible.

At this point, you should be able to see your application with a very basic header showing the application title and some basic coloring coming from your chosen Bootswatch styles. Notice, however, that the HTML title of your application is not quite right — by default it's set to 'Aurelia Navigation Skeleton'. We can change that to something a little more appropriate by opening `webpack.config.js` and altering line 16 to whatever title we like. I've simply called mine 'Movielist':

{% highlight js %}
//...
const when = (condition, config, negativeConfig) =>
  condition ? ensureArray(config) : ensureArray(negativeConfig);

// primary config:
const title = 'Movielist';
const outDir = path.resolve(__dirname, project.platform.output);
const srcDir = path.resolve(__dirname, 'src');
const nodeModulesDir = p
// ...
{% endhighlight %}

## Showing some movies

One of the first things we can do is show some of the current movies that are trending from the API. This will involve a few different pieces:

* Creating a class that we can use to access the API
* Creating a component to show a single movie
* Creating a component to show a list of our movie components

Let's begin with the API class. This will simply be a class that will allow us easy access to The Movie DB API, and will wrap a node library called `moviedb-promise`. Create a new folder called `api` inside the `src` folder, and create a new file in there called `index.ts`.

We also need a couple of supporting elements before we go ahead and create the API class itself.

First up, we need to modify the environment configuration with our MovieDB API key that we created earlier. Open `aurelia_project/environments/dev.ts` and add in your API key, like so:

```js
export default {
  debug: true,
  testing: true,
  apiKey: '<your API key here>'
};
```

> **Note:** After requesting an API key, _The Movie DB_ team will generate two keys: one called _API Key (v3 auth)_ and one called _API Read Access Token (v4 auth)_. Use the `v3` one to replace `<your API key here>` in the code snippet above.

We also need to install the `moviedb-promise` package. You can do this using `npm` on the command-line:

```bash
npm install moviedb-promise
```

Next, we need to add some TypeScript typings for the `moviedb-promise` libary that we're about to interact with. We don't _have_ to do this, but it will make working with the library much nicer, seeing as we're using TypeScript anyway.

Create a new file called `moviedb-promise.d.ts` inside the `custom_typings` folder and populate it with the following:

```js
declare module 'moviedb-promise' {
  type SearchOptions = {
    query: string;
  }

  type Configuration = {
    images: {
      base_url?: string;
      secure_base_url?: string;
      poster_sizes?: string[];
    }
  }

  type Movie = {
    id?: number;
    overview?: string;
    popularity?: number;
    title?: string;
    vote_average?: number;
    vote_count?: number;
    release_date?: string;
    poster_path?: string;
  }

  type SearchResults = {
    page?: number;
    total_pages?: number;
    total_results?: number;
    results?: Array<Movie>;
  }

  export default class MovieDB {
    constructor(apiKey: string)

    searchMovie(options: SearchOptions): Promise<SearchResults>;
    miscPopularMovies(): Promise<SearchResults>;
    configuration(): Promise<Configuration>;
  }
}
```

This isn't an exhaustive set of operations that the library supports, but it does contain all the types and functions that we're going to need to build this app.

Turning our attention back to the API class we're creating, open `src/api/index.ts` and start by importing the types that we'll need.

```js
import MovieDB, { Configuration, Movie } from "moviedb-promise";
import env from "../environment";
```

Next, we can create the MovieDB client and load its configuration. The configuration is another API call to the MovieDB API that returns some useful settings to us, the main one being the secure (HTTPS) base URL to movie poster images!

```js
const client = new MovieDB(env.apiKey);

let config: Configuration;

client.configuration().then(c => (config = c));
```

Now we can create our API class. The two operations we're going to make use of in our application are the ability to search for a movie by title and to retrieve the current popular movies:

```js
export class MovieApi {
  search(query: string) {
    return client
      .searchMovie({ query })
      .then(result => Object.assign(result, { 
        results: fixProfilePaths(result.results)
      }));
  }

  popularMovies() {
    return client
      .miscPopularMovies()
      .then(result => fixProfilePaths(result.results));
  }
}
```

The last thing we need to do is provide the implementation of the `fixProfilePaths` function. The movie data that we get from the API normally only includes a short path to the image (e.g. `/8763abc43c1/image.png`) — what we'd like to do is prepend the base path to that so that it becomes a fully qualified URL. `fixProfilePaths` will take a list of movies, and modify them to include the full image URL instead of just the path:

```js
const fixProfilePaths = (movies: Movie[]) => {
  return movies.filter(movie => movie.poster_path).map(movie => {
    return Object.assign(movie, {
      poster_path: `${config.images.secure_base_url}/w500${movie.poster_path}`
    });
  });
};
```

You'll notice that it also filters out movies that don't have a value for `poster_path`, so there's no point in prepending the full URL if we can't show the image anyway.

That completes the API class — let's move on to showing some movies!

## Getting movies on the screen

We'll begin by showing the most popular movies on the home page. Later, once we've got the 'watch list' implemented, we'll shuffle things around a bit to be more like the final version of the application.

For this, we'll need two components: one to show a single movie, and another to show a collection of movies together.

Starting with the single movie component, use the Aurelia CLI to generate a new component inside the `components` folder:

```bash
au generate component Movie
```

> **Note:** If the command above ends up with an error saying `TypeError [ERR_INVALID_ARG_TYPE]: The "path" argument must be of type string`, [you might need to remove the current `jest-cli` dependency and installing the latest version available (for the time being, `v23.1.0`)](https://github.com/timarney/react-app-rewired/issues/170#issuecomment-351925149).

Again, you'll notice that two more files have appeared inside `src/components`: `movie.html` and `movie.ts`. Open `movie.ts` and replace its contents with the following:

```js
import { bindable } from 'aurelia-framework';
import { Movie as MovieModel } from 'moviedb-promise';

export class Movie {
  @bindable model: MovieModel;
  isHovering = false;

  mouseOver() {
    this.isHovering = true;
  }

  mouseOut() {
    this.isHovering = false;
  }
}
```

Here we bring in a couple of types that we need: `bindable` allows us to create properties on our exported class that can be bound by components higher up the order, effectively allowing them to set which movie this component should display.

We've also added a couple of methods that allow us to tell whether or not the mouse is hovering over the component or not. This is so that we can include some nifty CSS which will show the movie description on hover (check out [the demo application](https://movies.elkdanger.co.uk/popular) if you haven't already, as it will become more clear).

Turning our attention to the view for this component, let's add the markup we need to support displaying a single movie on the screen. Open `movie.html` and replace the default content with the following:

{% highlight html %}
{% raw %}
<template>
  <require from="./movie.css"></require>

  <div class="card">

    <div class="movie-card ${isHovering ? 'active' : ''}" mouseover.delegate="mouseOver()" mouseout.delegate="mouseOut()">
      <img src="${model.poster_path}" alt="" class="card-img-top">
      <div class="movie-info">
        <p>${model.overview}</p>
      </div>
    </div>

    <div class="card-body">
      <h5 class="card-title">${model.title}</h5>
      <p>Avg. rating: ${model.vote_average}</p>
    </div>
  </div>
</template>
{% endraw %}
{% endhighlight %}

This is pretty standard markup for [Bootstrap's Card component](https://getbootstrap.com/docs/4.0/components/card/). For now, it mostly uses Aurelia's binding syntax for outputting properties of the movie into the HTML template.

 Notice the events which handle the `mouseover` and `mouseout` are used here to set an `active` class on the element that contains the movie poster and overview text. The idea is that, as you mouse over the poster, the overview text will come into view.

Also notice the reference to `movie.css`. Create that file now alongside the HTML file, and populate it with the following:

```css
.movie-card {
  position: relative;
}

.movie-card .movie-info {
  position: absolute;
  top: 0;
  left: 0;
  background: black;
  opacity: 0;
  height: 100%;
  padding: 1rem;
  transition: all 0.25s;
}

.movie-card.active .movie-info {
  opacity: 0.8;
}
```

This sets up an overlay over the movie poster image with a black background, with its `opacity` set to `0` (fully transparent). When the `active` class is applied, then the overlay becomes partially opaque. The idea is that the mouse events we applied in the previous step will apply this `active` class to the element, giving us the reveal effect allowing the user to read the overview text. Notice that there's also a `transition` setting, giving the overlay a nice fade—in fade—out effect whenever the class is applied.

Moving on, the second component we need to create is the 'movie list' — a component that displays a list of movie components. We can do that by using the Aurelia CLI tool, again creating it inside the `components` directory alongside the others:

```bash
au generate component MovieList
```

Open `movielist.ts` and replace the default content with the following:

```js
import { Movie } from "moviedb-promise";
import { bindable } from "aurelia-framework";

export class MovieList {
  @bindable movies: Movie[];
  @bindable title: string;
}
```

At this stage it's a pretty simple component that provides bindings for some movies (the ones we want to show in the list) as well as a title. We're providing the `movies` binding because we're going to be using this component to display a list of movies from a bunch of different sources — the search result, the popular movies and the watch list. All of these things are essentially just different lists of the same `Movie` type, so it makes sense to re—use this component as much as possible.

Open `movielist.html` and replace the default markup with this:

{% highlight html %}
{% raw %}
<template>
  <require from="./movie"></require>
  <require from="./movielist.css"></require>

  <h2>${title}</h2>

  <div class="movie-list row">
    <div class="col-sm-4 movie" repeat.for="movie of movies">
        <movie model.bind="movie"></movie>
    </div>
  </div>

</template>
{% endraw %}
{% endhighlight %}

Thanks to the `repeat.for` directive, this template will loop through the list of movies that have been bound to this component. From here we simply show the `movie` component that we created in the previous step, binding the `model` property to the movie instance that we currently have in the loop. Around that is just some Bootstrap markup to display all the movies in a nice column format.

We also bring in `movielist.css` for the list of movies, which has some minor styling to pad out each movie component a little bit:

```css
.movie {
  padding-bottom: 1rem;
}

h2 {
  margin-bottom: 1rem;
}
```

All that's left to do now is show this on a page! We're going to wrap a movie list component inside another component called `PopularMovies`, which will be in charge of loading the movies from the API and displaying them using the `MovieList` component. Use the CLI tool to generate a new component. This time though, we're going to put it in the `pages` folder:

```bash
au generate component PopularMovies
```

Next, open `popularmovies.ts` and add in the code to load the movies from the API: 

```js
import { Movie } from "moviedb-promise";
import { MovieApi } from '../api/index';
import { autoinject } from "aurelia-framework";

@autoinject
export class PopularMovies {    
  movies: Movie[];
  
  constructor(private api: MovieApi) {
  }

  async attached() {
    this.movies = await this.api.popularMovies()
  }
}
```

There are a few things to note here. First of all, we're bringing in the `MovieApi` type that we created a while back, and it's being injected into the component by [Aurelia's dependency injection framework](https://aurelia.io/docs/fundamentals/dependency-injection/). The `@autoinject` decorator allows Aurelia to automatically resolve the type and give us the right thing when the component is created.

Next, we're handling the `attached` method, which is part of Aurelia's component life—cycle. `attached` is called once the component has been attached to the DOM. At this point we call into the API to retrieve the popular movies. `this.api.popularMovies()` is a promise—based asynchronous method, which means we can take advantage of the [async/await](https://javascript.info/async-await) pattern and improve the readability of the code.

Now we can open `popularmovies.html` and create the template for this component:

{% highlight html %}
<template>
  <require from="../components/movielist"></require>
  <movie-list movies.bind="movies" title="Popular movies"></movie-list>
</template>
{% endhighlight %}

This is a simple template that delegates most of its work to the `MovieList` component. Notice though that Aurelia has determined that the tag name for the component is `movie-list` — it has split the name based on the camel—case convention, making it a bit easier to read in the HTML.

Finally, to get this all working and showing some movies on the screen, head to `app.html` and modify the template so that it include the `PopularMovies` component and displays it under the header:

{% highlight html %}
<template>
  <require from="./components/header"></require>
  <require from="./pages/popularmovies"></require>

  <header></header>

  <div class="container">
    <popular-movies></popular-movies>
  </div>

</template>
{% endhighlight %}

With any luck, you should now have the list of movies displayed inside your browser! Hover over the poster image and make sure you can see the movie overview text too, as that will show you that the mouse binding is working.

## Configuring the Aurelia router

In the next section we're going to be creating the user interface to allow the user to search for movies, so let's take this opportunity now to set up the Aurelia router to allow us to do that.

Open `app.ts`. The first thing we need to do is import some types that'll need for the routing to work:

```js
import { PLATFORM } from 'aurelia-pal';
import { RouterConfiguration, Router } from "aurelia-router";
```

Next, add the `configureRouter` method inside the `App` class that will be invoked by Aurelia when it is time to configure the router for the application:

```js
configureRouter(config: RouterConfiguration, router: Router) {
  config.options.pushState = true;

  config.map([
    { route: ['', 'popular'], name: 'popular', title: 'Popular movies', moduleId: PLATFORM.moduleName('pages/popularmovies'), nav: true },
  ]);  
}
```

The first thing we do here is configure the router to enable `pushState`, which allows us to use proper URLs like `/search` instead of the usual `/#/search` type, which will be required later once we start receiving callbacks from Auth0.

The other thing this function does is to set up a single route that points to our Popular Movies page by default. We'll come back and modify this later once we start adding the rest of the pages in.

The final thing we need to do is adjust the view so that the router can display its content on the page. Open `app.html` and remove the `<popular-movies>` element as well as the `require` element for it. Then, add in a `router-view` element to act as the viewport for the router content. You should end up with this:

{% highlight html %}
<template>
  <require from="./app.css"></require>
  <require from="./components/header"></require>

  <header></header>

  <div class="container">
    <router-view></router-view>
  </div>

</template>
{% endhighlight %}

If you run the app now, you should still see the Popular Movies page, and you should be able to see that if you either browse to `/` or `/popular` you will see the same data. You will change this later in the tutorial.

## Creating the search interface

Now that we have the facility to browse between pages, let's go ahead and tackle 'search'. This page is going to be very similar to Popular Movies, in that it simply uses the `MovieList` component to display the results that have been retrieved from a call to the API. We'll also put a search box in the header, allowing the user to type in some search criteria.

Let's start with the page. Create a new component in the `pages` directory called 'SearchResults':

```bash
au generate component SearchResults
```

Next, open `searchresults.html` and populate it with the following markup:

{% highlight html %}
<template>
  <require from="../components/spinner.html"></require>
  <require from="../components/movielist"></require>
 
  <movie-list movies.bind="results.results" title.bind="header"></movie-list>

  <spinner if.bind="isLoading"></spinner>   
    
</template>
{% endhighlight %}

There are a couple of things to notice here. First is that we're just using the usual `MovieList` component to display the results and to bind the `movies` property to a list of results. We'll tackle where those results come from in a moment.

The other thing to notice is that we've got a reference to a `spinner` component. This is just an animated graphic which will show while the results are being loaded. To create this, create a new component in the `components` directory called 'Spinner':

```bash
au generate component Spinner
```

Then, open `spinner.html` and replace its contents with the following:

{% highlight html %}

<template>
  <require from="./spinner.css"></require>

  <div class="spinner">
    <img src="spinner.svg" alt="Loading..">
  </div>
</template>
{% endhighlight %}

There's a couple of other files we need to create in this folder. First, `spinner.css`:

```css
.spinner {
  margin-bottom: 1rem;
  text-align: center;
}
```

And `spinner.svg`. This SVG file came from [Sam Herbert's excellent collection of SVG loaders](http://samherbert.net/svg-loaders/), and I used `rings.svg` in my implementation of the app. Pick which one you like from his [SVG loader repository](https://github.com/SamHerbert/SVG-Loaders/tree/master/svg-loaders) and save it alongside the `spinner` component as `spinner.svg`.

To finish off the search component, open `searchresults.ts` and start by importing some types:

```js
import { autoinject } from "aurelia-dependency-injection";
import { MovieApi } from "../api/index";
import { Movie, SearchResults as Results } from "moviedb-promise";
```

Then, update the `SearchResults` class so that it looks like the following:

```js
@autoinject
export class Search {
  searchTerm: string;
  header: string = 'Your search results';
  results: Results;
  isLoading = false;

  constructor(private api: MovieApi) {}

  async activate(args: { term: string }) {
    this.searchTerm = args.term;
    this.isLoading = true;
    this.results = null;

    this.results = await this.api.search(this.searchTerm);

    this.header = this.results.total_results > 0
      ? `Found ${this.results.total_results} movies that matched "${this.searchTerm}"`
      : `Didn't find any results for "${this.searchTerm}"`;

    this.isLoading = false;
  }
}
```

Again we're using the `autoinject` decorator which allows the `MovieApi` type to be automatically injected into the constructor when this component is created. Then, we implement one method `activate` which is called by the Aurelia router when this component has been activated through a route. This function is given the search term as an argument, meaning that we can use that to query the API.

Notice that this function uses the async/await pattern as well, meaning that we can write the call to the API in a way that appears to be syncronous, but actually it's using a Promise in the background.

Finally, the content for the header is devised based on the amount of search results that have been returned. If we get some results, the header tells us how many results there are for the given term, otherwise we simply display a message telling the user that there were no results.

The next part we need to tackle is updating the router so that the search page can be accessed by the browser. Re—open `app.ts` and update the router config to include this new search route:

```js
config.map([
  { route: ['', 'popular'], name: 'popular', title: 'Popular movies', moduleId: PLATFORM.moduleName('pages/popularmovies'), nav: true },
  { route: 'search/:term', name: 'search', moduleId: PLATFORM.moduleName('pages/searchresults')}
]);  
```

Here the route is configured so that the page can be accessed via the URL, including the search term. For example, we should now be able to find all the Godzilla movies by browsing to the search page at `/search/godzilla`!

The final thing to do is create the little search box that the user can use to find movies through the UI. Create a new component in the `components` directory called 'SearchBox':

```bash
au generate component SearchBox
```

Populate the `searchbox.html` file with the markup to display a form with a textbox:

{% highlight html %}
<template>

  <form class="form-inline my-2 my-lg-0" submit.trigger="doSearch()">
    <input type="search"
      value.bind="searchTerm"
      type="text"
      placeholder="Search for movies"
      class="form-control mr-sm-2">

      <button type="submit" class="btn btn-info">Search</button>
  </form>

</template>
{% endhighlight %}

Notice that the form has `submit.trigger="doSearch()"` applied to it, which means that when the form is submitted (either the user hits the enter key after entering a search term, or presses the 'search' button) then it will execute the `doSearch()` function inside the component. Another thing of importance is that the textbox's value is bound to the `searchTerm` property, so that we can retrieve the value that the user typed in when the form has been submitted. We'll see how this is created in the next piece of code.

Inside `searchbox.ts`, the component code should look like the following. Again the `autoinject` decorator is used to bring in the `Router` type through the constructor:

```js
import { autoinject, bindable } from "aurelia-framework";
import { EventAggregator } from "aurelia-event-aggregator";
import { Router } from "aurelia-router";

@autoinject
export class SearchBox {

  @bindable searchTerm: string;

  constructor(private router: Router, private events: EventAggregator) {
    this.events.subscribe('router:navigation:success', r => {      
      if (r.instruction.config.name === 'search') {
        this.searchTerm = r.instruction.params.term;
      }
    });
  }

  doSearch() {
    if (this.searchTerm && this.searchTerm! + "") {
      this.router.navigate(`search/${this.searchTerm}`);
    }
  }
}
```

There are a few pieces to this code, but let's go through the main areas:

* We have the bindable `searchTerm` property, which is bound to the search box's `value` property in the template
* In the constructor, we're bringing in the router and also the `EventAggregator`, and we subscribe to the `router:navigate:success` event. We do this so that, if the user is performing a search and browses to `/search/arthur` then we retrieve the search term from the router data and pre—fill the search term with it
* Finally, the `doSearch()` function is defined, and it basically takes the search term (if the user typed one in) and uses the router to navigate to the search results page

The last thing to do is place this new search control into the header, so that the user can interact with it. Re—open `header.html` and modify the template so that it includes the search control:

{% highlight html %}
{% raw %}
<template>

  <require from="./searchbox"></require>

  <nav class="navbar navbar-expand navbar-dark bg-primary justify-content-between">
    <span class="navbar-brand mb-0 h1" href="#">${appTitle}</span>

    <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent"
      aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>

    <div class="collapse navbar-collapse" id="navbarSupportedContent">
      <!-- new! -->
      <search-box></search-box>
      <!-- /new -->
    </div>
  </nav>

</template>
{% endraw %}
{% endhighlight %}

Now the search box should appear inside the navigation panel, and the user should be able to type in a search term and have the results displayed on the page. Neat!

One other thing we can do while we're inside the navigation container is display the available routes from the Router. If you look back at the router configuration we're building up inside `app.ts`, you'll notice that some of the routes have a `nav: true` property. This facilitates the ability to enumerate over the available routes and generate a set of navigation links. While we've got `header.html` open, let's do that now. Put this just inside the `div` with the 'collapse' class on it, to write out the available links:

{% highlight html %}
<ul class="navbar-nav mr-auto">
  <li class="nav-item ${item.isActive ? 'active' : ''}"
      repeat.for="item of router.navigation">
    <a class="nav-link" href.bind="item.href">${item.title}</a>
  </li>
</ul>
{% endhighlight %}

Now open `header.ts` and bring in the router using dependency injection:

```js
import { autoinject } from 'aurelia-framework';
import { Router } from 'aurelia-router';

@autoinject
export class Header {    
  appTitle = "Movielist";
  
  constructor(private router: Router) {}
}
```

This will give the template access to the router and the available navigation elements, enabling it to write out the navigation links. If you look at the page now in the browser, you should find that the 'Popular movies' navigation link is available.

## Adding Auth0 support

In the next section, we're going to be adding the facility to add movies to the watch list and displaying all of the movies in the watch list on the screen. However, we're going to make it so that the user has to be signed in for the 'add' button to become available. So, we're going to manage user authentication by using the Auth0 login page.

To do this, you first need to [sign up for a free Auth0 account](https://auth0.com/signup) if you don't already have one. Once you have created your account, you will have access to your [account management page](https://manage.auth0.com) where you can [add an application](https://manage.auth0.com/#/applications).

From this page, click 'Create application'. On the next page, enter a name for your app (I called mine 'Movielist') and for the 'application type', select 'Single Web Page Applications:

![Creating an Auth0 application](https://cdn.auth0.com/blog/aurelia-movies/creating-an-auth0-application.png)

Next, click the 'Create' button. On the next screen, select the 'Settings' tab, and scroll to the 'Allowed Callback URLs' field. In this box, insert the following value:

```bash
http://localhost:8080/callback
```

Scroll a little further down, and enter the following value for the 'Allowed Logout URLs':

```bash
http://localhost:8080/
```

Finally, click the 'Save changes' button at the bottom of the page to commit your changes. From this point, feel free to [set up social connections](https://manage.auth0.com/#/connections/social). You can then enable them for your app in the Application options under the Connections tab. For mine, I've enabled 'GitHub' and 'Google'.

### Configuring our app for Auth0

From your Auth0 Application Settings page, copy the value for Client ID. Next, open `aurelia_project/environments/dev.ts` and modify the configuration object to include the Client ID value that's in your clipboard. You will also need to head back to the settings page and retrieve the 'domain' value:

```js
export default {
  debug: true,
  testing: true,
  apiKey: 'd25d7a6ef37e1f8d1d30d96be59c6a3e',
  auth0: {
    clientId: '<your Auth0 client ID>',
    domain: '<your Auth0 domain>'
  }
};
```

In order to work with Auth0, we need to install the `auth0-js` package, as well as the TypeScript type definitions, which will make working with the library a bit easier:

```bash
npm install --save-dev auth0-js @types/auth0-js
```

Next, we're going to create a class that handles our authentication strategy for us. Create a new file `src/auth-service.ts`, and create the class `AuthService` inside it, bringing in some imports that we're going to be using:

```js
import { WebAuth, Auth0DecodedHash } from "auth0-js";
import { autoinject } from "aurelia-framework";
import { Router } from "aurelia-router";
import { EventEmitter } from "events";
import env from "./environment";

@autoinject
export class AuthService {
  private accessToken: string;
  private id_token: string;
  private expires_at: string;
  
  authNotifier = new EventEmitter();

  auth0 = new WebAuth({
    domain: env.auth0.domain,
    clientID: env.auth0.clientId,
    redirectUri: "http://localhost:8080/callback",
    audience: `https://${env.auth0.domain}/userinfo`,
    responseType: "token id_token",
    scope: "openid"
  });

  constructor(private router: Router) {}
}
```

Here we've set up quite a few things that we'll make use of in the next few code snippets, but the main thing about this code is the Auth0 WebAuth configuration. These values are mostly pulled from the configuration that we defined in the previous section, but make sure that the value for `redirectUri` matches the value that was given in the settings for your Auth0 application. The value for `audience` is calculated based on the domain.

Notice that we've also pulled in `Router`, so that we can send the user to the right place once they've logged in.

### Fleshing out the authentication service class

Let's put some methods into this class to perform authentication, and handling of the callback from Auth0 once the user has logged in. First up, `login()`:

```js
login(): void {
  localStorage.setItem('location', JSON.stringify({ url: this.router.currentInstruction.fragment }));
  this.auth0.authorize();
}
```

Here we delegate most of the work to the `auth0.authorize()` call, which will redirect the user away to the Auth0 login page. However, we also store the current route that the user is on so that once the user has logged in, we can redirect them back to the page that they were on when they hit the login button. Although we only have a couple of pages, remember that the user can search for movies, so this will allow them to continue where they left off with their search.

Next, the `handleAuthentication()` method. This will handle the callback once Auth0 has returned control to our application:

```js
handleAuthentication(): void {
  this.auth0.parseHash((err, authResult) => {
    if (authResult && authResult.accessToken && authResult.idToken) {
      this.setSession(authResult);

      const savedLocation = JSON.parse(localStorage.getItem('location'));
      const navigateTarget = savedLocation.url || 'watchlist';

      this.router.navigate(navigateTarget);

      this.authNotifier.emit('authChange', { authenticated: true });
    } else if (err) {
      console.log(err);
    }
  });
}
```

The call to `auth0.parseHash()` will allow the `auth0-js` library to process the token information sent to use from Auth0 in the return URL. We then take that information and create out auth session by storing those values in memory (the `setSession()` method is defined next). The rest of the code here deals with the loading of the previously saved URL, and returning the user to where they left off. We also emit an event to any listeners to let them know that the authentication state has changed.

Next, `setSession()`:

```js
setSession(authResult: Auth0DecodedHash): void {
  const expiresAt = JSON.stringify(
    authResult.expiresIn * 1000 + new Date().getTime()
  );

  this.accessToken = authResult.accessToken;
  this.id_token = authResult.idToken;
  this.expires_at = expiresAt;
}
```

After working out the expiry time, we simply store the token values for later use.

The next thing we need is the ability to determine whether or not the user is currently logged in, and we can do that with an `isAuthenticated()` method:

```js
isAuthenticated(): boolean {
  if (!this.accessToken)
    return false;

  const expiresAt = JSON.parse(this.expires_at);
  return new Date().getTime() < expiresAt;
}
```

To do this, we just check if `this.accessToken` has been defined. If it has, we also check that the current time is still within the calculated expiry time.

Finally, we need a way to log out of the application:

```js
logout(): void {
  delete this.accessToken;
  delete this.id_token;
  delete this.expires_at;

  this.authNotifier.emit('authChange', { authenticated: false });
}
```

Now that the `AuthService` class has been defined, we can start to use it in the next section.

### Routing the Auth0 callback

In order for the callback from Auth0 to work correctly, we'll need to create some routing for it. To achieve this, we need to:

* Create a new compnonent to invoke the `handleAuthentication()` method in the authentication service
* Configure the Aurelia router to accept the `/callback` URL and pass it on to this new callback component.

Let's update the router configuration first. Open `app.ts` and modify the router configuration to route `/callback` to a new component that will handle the route:

```js
config.map([
  { route: ['', 'watchlist'], name: 'watchlist', title: 'Watchlist', moduleId: PLATFORM.moduleName('pages/watchlist'), nav: true },
  { route: 'popular', name: 'popular', title: 'Popular movies', moduleId: PLATFORM.moduleName('pages/popularmovies'), nav: true },
  { route: 'search/:term', name: 'search', moduleId: PLATFORM.moduleName('pages/searchresults')},
  { route: 'callback', name: 'callback', moduleId: PLATFORM.moduleName('callback') }
]);
```

Next, create a new file in the `src` directory called `callback.ts`, and insert a new `Callback` class:

```js
import { autoinject } from 'aurelia-framework';
import { AuthService } from 'auth-service';

@autoinject
export class Callback {
  constructor(private auth: AuthService) {
    auth.handleAuthentication();
  }
}
```

Here is where we invoke the call to `handleAuthentication()` and where the auth session is created, completing the round—trip which enables the user to log into our application.

## Creating the movie watch list

Now we can get to the meat of the application: adding movies to a 'watch list'. This will involve:

* adding an 'add' button on each movie from search results or popular movies
* adding a 'remove' button on each movie inside the watch list
* the facility to add and remove a movie to/from an array in local storage
* a new view to display the movies from the watch list using the `MovieList` component we created earlier

The interaction with [local storage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) is going to be marshalled through a new class called `WatchlistManager`. Create a new file inside the `src` folder called `watchlist-manager.ts`, and start by importing some types that we'll need:

```js
import { EventEmitter } from 'events';
import { Movie } from "moviedb-promise";

const storageKey = 'watchlist';

export class WatchlistManager {
}
```

Let's add an array to store the movies once they've been read from local storage, and an `EventEmitter` so that other types can subscribe to events that we generate:

```js
private items: Movie[];
notifier = new EventEmitter();

constructor() {
  this.items = this.load();
}
```

Next, we need methods to load and save the data from/to local storage:

```js
private load(): Movie[] {
  return localStorage.getItem(storageKey)
  ? JSON.parse(localStorage.getItem(storageKey))
  : [];
}

private save(): void {
  localStorage.setItem(storageKey, JSON.stringify(this.items));
}
```

We also need methods to add and remove movies to/from the watch list. Notice that we're emitting some events to others to let them know that we've added or removed a movie, and what movie it was. This will come in handy later when we configure the UI to show whether something has just been added to the watch list or not:

```js
add(movie: Movie) {
  if (!this.isAdded(movie)) {
    this.items.push(movie);
    this.save();

    this.notifier.emit('movie:added', movie, this.items);
  }
}

remove(movie: Movie) {
  if(this.isAdded(movie)) {
    this.items.splice(this.items.indexOf(movie), 1);
    this.save();
    this.notifier.emit('movie:removed', movie, this.items);      
  }
}
```

Finally, we can implement a couple utility methods to retrieve the items, and to determine whether or not a movie is in the watchlist:

```js
get() {
  return this.items;
}

isAdded(movie: Movie): boolean {
  return this.items.find(m => m.id == movie.id) != undefined;
}
```

This gives us everything we need to load, store, add and remove movies.

The next thing we can do is create a new page which displays movies that are currently in the watch list. This will be very similar to the 'popular movies' and 'search results' pages; we're just changing where the movies come from.

Create a new component inside the `pages` folder:

```bash
au generate component Watchlist
```

Open `watchlist.html` and populate it with the following template:

{% highlight html %}
<template>
  <require from="../components/movielist"></require>

  <movie-list movies.bind="watchlist.get()" title="Your watchlist"></movie-list>

  <div class="alert alert-info" if.bind="!hasMovies">
    <h4 class="alert-heading">There's nothing here!</h4>
    <p>Try looking through <a href="${router.generate('popular')}">popular movies</a> to add them to your watchlist</p>
  </div>

</template>
{% endhighlight %}

The main difference here compared to the other pages that we've done, is that there's a new panel on this screen which displays a short message on the screen when there are no movies in the watch list, and a prompt to try looking through the popular movies list. This is just a link to the 'popular movies' page, generated by the router. Otherwise, it pulls in the `MovieList` component and assigns the `movies` property to the value `watchlist.get()`, which is one of the methods that we created in the previous step.

Now open `watchlist.ts` and configure it like so:

```js
import { Router } from 'aurelia-router';
import { autoinject } from "aurelia-framework";
import { WatchlistManager } from '../watchlist-manager';

@autoinject
export class Watchlist {
  hasMovies: boolean;

  constructor(private watchlist: WatchlistManager, private router: Router) {
    this.hasMovies = watchlist.get().length > 0;

    const handler = (movie, list) => this.hasMovies = list.length > 0;

    this.watchlist.notifier.addListener('movie:removed', handler);
  }
}
```

Here we bring in the `WatchlistManager` we created earlier using dependency injection, as well as the router. We set `hasMovies` to a value depending on whether or not there are any movies in the watch list (thus showing the "There's nothing here!" panel) and we also listen out for the `movie:removed` event from the watch list manager. This enables us to re—surface the panel if the user were to remove the last movie in the watch list.

Finally, let's reconfigure the router so that the watch list is shown by default when the home page is loaded. We will also have a third route which will still enable us to reach the list of popular movies. Open up `app.ts` again and reconfigure the router to add in the watch list page:

```js
config.map([
  { route: ['', 'watchlist'], name: 'watchlist', title: 'Watchlist', moduleId: PLATFORM.moduleName('pages/watchlist'), nav: true },
  { route: 'popular', name: 'popular', title: 'Popular movies', moduleId: PLATFORM.moduleName('pages/popularmovies'), nav: true },
  { route: 'search/:term', name: 'search', moduleId: PLATFORM.moduleName('pages/searchresults')},
  { route: 'callback', name: 'callback', moduleId: PLATFORM.moduleName('callback') }
]);
```

Notice that we've set `nav: true` on the watch list route as well, which means if you reload the page now, you'll see both 'Watchlist' and 'Popular movies' appear in the navigation bar. Also, you should now see the panel letting you know that you haven't added anything to the watch list yet — let's fix that now!

To start adding movies to the watch list, we need to revisit the `Movie` component and add some buttons on there. We're going to add two buttons — one for adding a movie if it's not already in the list, and one for removing the movie when it's already in the list.

Open `movie.html` and add three buttons into the `card-body` element:

{% highlight html %}
{% raw %}
<template>
  <!-- require movie.css -->
  <div class="card">
    <!-- ... div.movie-card ... -->
    <div class="card-body">
      <h5 class="card-title">${model.title}</h5>
      <p>Avg. rating: ${model.vote_average}</p>
      <a if.bind="!isAuthenticated" click.trigger="auth.login()" href="#" class="btn btn-success">Sign in</a>
      <a if.bind="!isInWatchlist && isAuthenticated" click.trigger="addMovie(model)" href="#" class="btn btn-success">Add</a>
      <a if.bind="isInWatchlist && isAuthenticated" click.trigger="removeMovie(model)" href="#" class="btn btn-outline-success">Remove</a>
    </div>
  </div>
</template>
{% endraw %}
{% endhighlight %}

The 'add' button will invoke the `addMovie()` function, while the 'remove' button will invoke the `removeMovie()` function. If the user hasn't logged in yet, the 'Sign in' button will invoke the login process and redirect the user to Auth0 for authentication.

Let's add the methods for adding and removing movie into the class defined in `movie.ts`:

```js
addMovie(movie: MovieModel) {
  this.watchlist.add(movie);
  this.isInWatchlist = true;
}

removeMovie(movie: MovieModel) {
  this.watchlist.remove(movie);
  this.isInWatchlist = false;
}
```

We'll also need to bring in the `WatchlistManager` and `AuthService` types into the constructor, as well as a new property to record whether the movie is in the watch list or not:

```js
import { autoinject, bindable } from 'aurelia-framework'; // this has been modified to include 'autoinject'
import { WatchlistManager } from '../watchlist-manager';
import { AuthService } from '../auth-service';

// ...

@autoinject
export class Movie {
  //...
  isInWatchlist = false;
  isAuthenticated = false;
  //...

  constructor(private watchlist: WatchlistManager, private auth: AuthService) {
    this.isAuthenticated = auth.isAuthenticated();

    auth.authNotifier.addListener('authChange', state => {
      this.isAuthenticated = state.authenticated;
    });
  }
  //...
```

Finally, we need to somehow initialize `isInWatchlist` when the component is loaded, so let's handle the `bind` lifecycle event and set it there:

```js
bind() {    
  this.isInWatchlist = this.watchlist.isAdded(this.model);    
}
```

With this code in place, you should now be able to sign in to your application using the Auth0 login page, add and remove movies to and from the watch list, and be able to browse to the watch list page and see the list of movies that you've added. Furthermore, you should be able to do this from either the popular movies page, or the search results page!

The last thing to achieve is logging out of the application. We're going to do this by placing a link inside the navigation bar which allows the user to log out (we can add it after `<search-box></search-box>`), which will also update any links on the page allowing the user to sign in again. Open `header.html` and adjust the markup to include this new link after the search box:

{% highlight html %}
<!-- ... -->
<ul class="navbar-nav">
  <li class="nav-item" if.bind="isAuthenticated"><a href="#" click.trigger="auth.logout()" class="nav-link">Log out</a></li>
</ul>
<!-- ... -->
{% endhighlight %}

Next, we need to modify the component code for the header to bring in the authentication service, and to handle things when the auth state changes. Update `header.ts` to look like the following:

```js
import { autoinject } from 'aurelia-framework';
import { Router } from 'aurelia-router';
import { AuthService } from 'auth-service';

@autoinject
export class Header {    
  appTitle = "Movielist";
  isAuthenticated = false;
  
  constructor(private router: Router, private auth: AuthService) {
    this.isAuthenticated = auth.isAuthenticated();

    auth.authNotifier.on('authChange', state => {
      this.isAuthenticated = state.authenticated;
    });
  }
}
```

With this in place, you will be able to log out of the application and all of the 'Add' and 'Remove' buttons on each movie will update to reflect the change in auth state.

{% include tweet_quote.html quote_text="I just built a movie listing application with Aurelia." %}

## Summary and next steps

In this article you saw how to create an Aurelia TypeScript application that allowed you to browse for popular movies or search for your own favourites. We saw how to create components using the CLI tool, how to bind variables between the component and the template, and how to use dependency injection and the `autoinject` decorator to bring in other types and create a loosely—coupled framework of code.

We created an Auth0 application and use the Auth0 JavaScript library to enable the user to log in using the Auth0 login page. Once the user is authenticated, they can begin to add and remove movies to and from a watch list that they can refer to later. Once they're done adding movies, they're able to log out of the application again.

The application could be further extended to support TV shows, or could use the information retrieved from Auth0 to get profile—based information such as the user's email address or avatar for display somewhere, creating a profile of sorts. Instead of watch lists being stored in local storage, they could be stored inside a database enabling a user to 'share' a watch list with another user. All these are useful features that are left as an exercise for you, the reader, to have fun with.
