---
layout: post
title: "Creating Web Components with Stencil"
description: "A practical tutorial showing how to create Web Components using Stencil."
longdescription: "In this article, you will learn how to use Stencil to create Web Components that can be used in any modern web application. You will start by creating a simple rating component that allows users to choose a number of stars and then you will see how to integrate it with React and Angular."
date: 2018-06-19 08:30
category: Technical Guide, Frontend, JavaScript
author:
  name: "Andrea Chiarelli"
  url: "https://twitter.com/andychiare"
  mail: "andrea.chiarelli.ac@gmail.com"
  avatar: "https://cdn.auth0.com/blog/guest-author/andrea-chiarelli.jpg"
design:
  bg_color: "#2f333b"
  image: https://cdn.auth0.com/blog/creating-web-components-with-stencil/logo.png
tags:
- stencil
- javascript
- web-components
- frontend
- react
- angular
related:
- 2017-03-16-web-components-how-to-craft-your-own-custom-components
- 2016-10-05-build-your-first-app-with-polymer-and-web-components
---

**TL;DR:** This article will show you how to create Web Components with Stencil, a tool that allows you to use a high-level syntax to define components and that generates them based on vanilla JavaScript. Throughout the article, you will build a simple rating component and then you will integrate it with React and Angular. [You can find the final code of this component in this GitHub repository](https://github.com/auth0-blog/rating-stencil-component).

---

## What are Web Components

Have you ever struggled with integrating UI components implemented for different JavaScript frameworks or libraries, say for example *Angular* or *React* or *Vue* or whatever? Are you tired to reimplement the same UI component for each new framework or library? Do you know that a solution to this problem has already existed for some years? It is called [Web Components](https://www.webcomponents.org/).

Web Components are a set of [standard specifications](https://www.webcomponents.org/specs) that allow you to create custom and reusable components by simply using HTML, CSS, and JavaScript. In other words, Web Components allow you to define new custom HTML tags and behaviors by using standard technologies. These custom components should be natively supported by any Web browser, regardless the framework you are using to build your Web pages or your Web application. This should be the end of any JavaScript library interoperability nightmare, but... but there are still a few problems: mainly [the browser support for all the features](https://caniuse.com/#search=web%20component) and the low level of Web Components APIs.

## How Stencil Fits in the Web Components World

In past years a few libraries tried to remedy these Web Component problems providing a higher level of abstraction and filling the browser's lack of support regarding some basic features. Among others, [Polymer](https://www.polymer-project.org/) and [X-Tag](https://x-tag.github.io/) helped many developers to adopt Web Components in their projects.

In [August 2017 the Ionic team announced](https://www.youtube.com/watch?v=UfD-k7aHkQE) [Stencil.js](https://stenciljs.com), a performant compiler that generates Web Components by combining the best concepts of the most popular UI JavaScript frameworks and libraries. Unlike *Polymer* and *X-Tag*, *Stencil* is not another library that allows you to use Web Components in your project. It is a building tool that allows you to use a high-level abstraction to define your UI components and to generate pure JavaScript code implementing standard-based Web Components. The compiled code runs in all major browsers since Stencil uses a small polyfill only on browsers that lack some needed features.

{% include tweet_quote.html quote_text="The @stenciljs library is building tool that facilitates the creation of web components that you can use with @reactjs and @angular." %}

So, let's give Stencil a try and see how to build and use a Web Component.

## Setup of the Stencil Environment

To become familiar with *Stencil*, we are going to build a rating Web Component, that is a UI component allowing the user to provide his feedback about a product, an article or whatever by assigning a number of stars like in the following picture:

![Stencil Component that you will build through the tutorial](https://cdn.auth0.com/blog/stencil/rating-component.png)

As a first step towards this goal, you need to setup the *Stencil* development environment. So, be sure to get installed at least version 6.11.0 of [Node.js](https://nodejs.org) and then clone the component starter project from the Ionic team's GitHub repository, as shown below:

```shell
git clone https://github.com/ionic-team/stencil-component-starter rating-stencil-component
cd rating-stencil-component
git remote rm origin
npm install
```

These commands clone the `stencil-component-starter` repository into a local folder named `rating-stencil-component`, then change the current folder and remove the remote repository's reference. The last command installs all dependencies required by the *Node.js* starter project.

The component starter project provides a standard *Node.js* development environment. In particular, you can see a few configuration files in the root folder and the `src` folder containing a folder structure, as shown in the following picture:

![Basic directory structure of a Stencil project.](https://cdn.auth0.com/blog/stencil/basic-directory-structure.png)

The component starter project contains a very basic and working component that you can see in action by typing `npm start`. After a few seconds a browser window like the following will be open:

![A basic component built with Stencil.](https://cdn.auth0.com/blog/stencil/initial-component.png)

We are going to build our component by exploiting the infrastructure of this basic project.

## Creating a Basic Stencil Component

In order to implement our rating component, let's create a `my-rating-component` folder inside the `/src/components` folder. In this newly created folder, put one file named `my-rating-component.tsx` and one named `my-rating-component.css`. The `.css` file will contain the following code:

```css
.rating {
  color: orange;
}
```

The `.tsx` file will contain the following TypeScript code:

```typescript
import  { Component } from  '@stencil/core';

@Component({
  tag: 'my-rating',
  styleUrl: 'my-rating-component.css',
  shadow: true
})
export  class  MyRatingComponent  {
  render() {
    return  (
      <div>
        <span class="rating">&#x2605;</span>
        <span class="rating">&#x2605;</span>
        <span class="rating">&#x2605;</span>
        <span class="rating">&#x2606;</span>
        <span class="rating">&#x2606;</span>
        <span class="rating">&#x2606;</span>
      </div>
    );
  }
}
```

The `.tsx` extension indicates that the file contains TypeScript and JSX code. Actually, these are the languages used by Stencil to define the component. A Stencil component is a TypeScript class, `MyRatingComponent` in the example, that implements a `render()` method. This class is decorated by the `@Component` decorator, previously imported from the `stencil/core` module. This decorator allows you to define some meta-data about the component itself. In particular, we defined the tag name that will be associated with the component. This means that you will use the `<my-rating></my-rating>` element to put this component inside an HTML page. We also defined the CSS file containing styling settings for the component via the `styleUrl` property. The last property, `shadow`, isolates the internal component DOM and styles so that it is shielded by name conflicts and accidental collisions. This feature should be granted by the [Web Component's Shadow DOM](https://developers.google.com/web/fundamentals/web-components/shadowdom). Anyway, if the browser doesn't support it, a polyfill will be used.

The `render()` method describes the component's appearance by using JSX expressions. In our example the component's markup consists of a sequence of six-span HTML elements: three of them contain the HTML entity for the full star (`&#x2605;`) and the other three contain the code for the empty star (`&#x2606;`).

## Manually Testing a Stencil Component

Now that you have defined your first component, you can can remove the default component included in the starter component project. So remove the `/src/my-component` folder. Then open the `index.html` file in the `src` folder and replace its content with the following markup:

```html
<!DOCTYPE html>
<html dir="ltr" lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=5.0">
  <title>Stencil Component Starter</title>
  <script src="/build/mycomponent.js"></script>
</head>
<body>
  <my-rating></my-rating>
</body>
</html>
```

As you can see, the body of the HTML page contains the newly defined `<my-rating>` tag that identifies our component. After saving the `index.html` file, type `npm start` in a console window and you will see your component in a browser as in the following picture:

![First version of the Stencil rating component.](https://cdn.auth0.com/blog/stencil/initial-rating-component.png)

> **Note**: The Stencil development environment provides support for hot reloading, so if your environment is running after an `npm start`, any changes to the project's files will cause its automatic rebuilding and running. This means that you don't need to type again `npm start`.

## Adding Properties to Stencil Components

The component you have created so far is not so interesting, after all. It is quite static since it simply shows a fixed number of full and empty stars and the user cannot interact with it. It would be a bit more useful if at least the user could assign the total number of stars to show and the number of full stars indicating the current rating value. Adding these features is quite simple. Let's change the component definition as follows:

```typescript
import  { Component, Prop } from  '@stencil/core';

@Component({
   tag: 'my-rating',
   styleUrl: 'my-rating-component.css',
   shadow: true
})
export  class  MyRatingComponent  {
  @Prop() maxValue: number = 5;
  @Prop() value: number = 0;

  createStarList() {
    let starList = [];

    for (let i = 1; i <= this.maxValue; i++) {
      if (i <= this.value) {
        starList.push(<span class="rating">&#x2605;</span>);
      } else {
        starList.push(<span class="rating" >&#x2606;</span>);
      }
    }

    return starList;
  }

   render() {
    return  (
      <div>
        {this.createStarList()}
      </div>
    );
   }
}
```

As a first difference with respect to the previous version, you can see that we imported the `@Prop()` decorator. This decorator allows you to map the properties of the component class to attributes in the markup side of the component. We added the `maxValue` property, that represents the maximum number of stars to show, and the `value` property, that indicates the current rating value and so the number of full stars to be shown. As you can see, each property has a default value. These properties decorated with `@Prop()` allows you to use the component's markup as follows:

```html
<my-rating max-value="6" value="2"></my-rating>
```

By using this markup you are mapping the value of `max-value` attribute to the `maxValue` property and the value of `value` attribute to the `value` property. Notice how the [kebab case](http://wiki.c2.com/?KebabCase) naming style of the attribute names is mapped to the camel case naming style of the class properties.

Finally, the `createStarList()` method dynamically creates the sequence of the stars to display based on `maxValue` and `value` properties' value.

By applying these changes you will get the following result:

![Stencil rating component running with props.](https://cdn.auth0.com/blog/stencil/rating-component-with-props.png)

## The Reactive Nature of Properties on Stencil

The component's properties are not only a way to set customized initial values through HTML attributes. The mapping between the attributes and the properties is *reactive*. This means that any change to the attribute fires the render() method so that the component's UI is updated. You can verify this behavior by changing the content of the index.html file as follows:

```html
<!DOCTYPE html>
<html dir="ltr" lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=5.0">
  <title>Stencil Component Starter</title>
  <script src="/build/mycomponent.js"></script>
  <script>
  setTimeout(function() {
    let myRatingComponent = document.getElementById("myRatingComponent");
    myRatingComponent.value = 4;
  }, 5000)
  </script>
</head>
<body>
  <my-rating id="myRatingComponent" max-value="6" value="2"></my-rating>
</body>
</html>
```

We assigned an `id` attribute to the component's markup and added a script block calling the `setTimeout()` JavaScript function that schedules the execution of a function after 5 seconds. The scheduled function changes the `value` property of the component. So you will see your rating component with an initial number of two full stars and after five seconds you will see it with four full stars.

## Managing State of Stencil Components

Now we want to add more interactivity to our rating component. We want the number of full stars of the component to follow the mouse movement when it is over it. It should return to its original number when the mouse is out of its area, like in the following animation:

![Animated rating component built with Stencil.](https://cdn.auth0.com/blog/stencil/animated-rating-component-v2.gif)

In addition, we want to set a new value when the user clicks on one of the component's stars.

In order to manage this dynamic change of stars, you can assign an internal state to your component. The state of a component is a set of data internally managed by the component itself. This data cannot be directly changed by the user, but the component can modify it accordingly to its internal logic. Any change to the state causes the execution of the `render()` method.

Stencil allows to define the component state through the `@State()` decorator, so we can add a new property to internally track the stars to display in a given moment. The following is a first change to your code toward the dynamic behavior of the rating component:

```typescript
import  { Component, Prop, State } from  '@stencil/core';

@Component({
   tag: 'my-rating',
   styleUrl: 'my-rating-component.css',
   shadow: true
})
export  class  MyRatingComponent  {
  @Prop() maxValue: number = 5;
  @Prop() value: number = 0;
  
  @State() starList: Array<object> = [];

  createStarList() {
    let starList = [];

    for (let i = 1; i <= this.maxValue; i++) {
      if (i <= this.value) {
        starList.push(<span class="rating">&#x2605;</span>);
      } else {
        starList.push(<span class="rating" >&#x2606;</span>);
      }
    }

    this.starList = starList;
  }

   render() {
    return  (
      <div>
        {this.starList}
      </div>
    );
   }
}
```

With respect to the previous version, this code imports the `@State()` decorator and apply it to the newly introduced `starList` property. This property is an array of objects and represents the component state that will contain the JSX description of the stars to display. Consequently, the `createStarList()` method has been modified so that is assigned the resulting array to the state property. Finally, the `starList` property is used inside the JSX expression returned by the `render()` method.

> **Note**: Stencil watches state and props for changes in order to run the `render()` method. However, it actually compares references for changes, so a change in data inside an array or to an object's property doesn't cause a re-rendering of the component. You need to assign a new array or object to the state.

## Handling Events with Stencil

Once we added support for state management, let's make the user interact with our component. For this purpose, you need to capture mouse events in order to create the visual effect described above and to allow the user to assign a new rating value. You can handle the needed mouse event by adding to the component some new code, as shown in the following:

```typescript
import  { Component, Prop, State } from  '@stencil/core';

@Component({
   tag: 'my-rating',
   styleUrl: 'my-rating-component.css',
   shadow: true
})
export  class  MyRatingComponent  {
  @Prop() maxValue: number = 5;
  @Prop() value: number = 0;

  @State() starList: Array<object> = [];

  setValue(newValue) {
    this.value = newValue;
    this.createStarList(this.value);
  }

  createStarList(numberOfStars: number) {
    let starList = [];

    for (let i = 1; i <= this.maxValue; i++) {
      if (i <= numberOfStars) {
        starList.push(<span class="rating" onMouseOver={() => this.createStarList(i)} onMouseOut={() => this.createStarList(this.value)} onClick={() => this.setValue(i)}>&#x2605;</span>);
      } else {
        starList.push(<span class="rating" onMouseOver={() => this.createStarList(i)} onMouseOut={() => this.createStarList(this.value)} onClick={() => this.setValue(i)}>&#x2606;</span>);
      }
    }

    this.starList = starList;
  }

   render() {
    return  (
      <div>
        {this.starList}
      </div>
    );
   }
}
```

We added a few attributes to the JSX definition of the single star: `onMouseOver`, to capture the presence of the mouse over the component, `onMouseOut`, to capture the mouse exit from the component area, and `onClick`, to capture the click event on a star of the component. As per JSX semantics, these attributes are mapped to the corresponding HTML attributes `onmouseover`, `onmouseout` and `onclick`. Each attribute has an arrow function assigned that invokes the component's methods `createStarList()` and `setValue()`.

The first one is a slightly changed version of previous `createStarList()` method. Now it accepts an argument that defines the number of stars to display, and this parameter is used instead of the fixed `value` property.

The `setValue()` method takes the new value, assigns it to the `value` property and calls the `createStarList()` method to generate the new list of stars.

Now let's restore the previous version of the `index.html` file  by removing the `setTimeout()` code. It will look as follows:

```html
<!DOCTYPE html>
<html dir="ltr" lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=5.0">
  <title>Stencil Component Starter</title>
  <script src="/build/mycomponent.js"></script>
</head>
<body>
  <my-rating id="myRatingComponent" max-value="6" value="2"></my-rating>
</body>
</html>
```

Now let's run the project and look at the browser.

## Managing Stencil Components' Lifecycle

Unfortunately running the code written until now results in a blank page. What happens?

Reviewing the code, you can see that the `render()` method include the `starList` property in its JSX expression. This property is assigned inside the `createStarList()` method, but who does invoke this method in the initial step of the component creation?

You need to invoke the `createStarList()` method to correctly initialize your component. You may think to invoke it in the component's constructor, but it will have a strange behavior: its initial visualization will take into account the default values of `value` and `maxValue` properties since the list of stars will be built before the component receives the correct values from the DOM.

The right moment to initialize the component should be when it has been loaded into the DOM.

Fortunately, Stencil provides us with a few hooks to handle the various events of the component lifecycle:

- `componentWillLoad`
  The component is ready to be loaded into the DOM, but it is not rendered yet
- `componentDidLoad`
   The component has been loaded and rendered
- `componentWillUpdate`
  The component is about to be updated
- `componentDidUpdate`
  The component has been updated
- `componentDidUnload`
  The component has been removed from the DOM

To use this hooks you simply need to implement a method with the same name in your component.

So, in our case, you can implement the `componentWillLoad()` method and initialize the component, as shown below:

```typescript
import  { Component, Prop, State } from  '@stencil/core';

@Component({
   tag: 'my-rating',
   styleUrl: 'my-rating-component.css',
   shadow: true
})
export  class  MyRatingComponent  {
  @Prop() maxValue: number = 5;
  @Prop() value: number = 0;

  @State() starList: Array<object> = [];

  componentWillLoad() {
    this.createStarList(this.value);
  }

  setValue(newValue) {
    this.value = newValue;
    this.createStarList(this.value);
  }

  createStarList(numberOfStars: number) {
    let starList = [];

    for (let i = 1; i <= this.maxValue; i++) {
      if (i <= numberOfStars) {
        starList.push(<span class="rating" onMouseOver={() => this.createStarList(i)} onMouseOut={() => this.createStarList(this.value)} onClick={() => this.setValue(i)}>&#x2605;</span>);
      } else {
        starList.push(<span class="rating" onMouseOver={() => this.createStarList(i)} onMouseOut={() => this.createStarList(this.value)} onClick={() => this.setValue(i)}>&#x2606;</span>);
      }
    }

    this.starList = starList;
  }

   render() {
    return  (
      <div>
        {this.starList}
      </div>
    );
   }
}
```

This small change solves our issue, but another problem still remains: when a user clicks a star, the number of full stars doesn't change. Why the `setValue()` method does not work?

## Managing Mutable Properties on Stencil

We said above that a component property decorated with `@Prop()` acquires a reactive nature. This means that if a change to the property occurs from the external of the component, this immediately fires the re-rendering of the component itself. However, the property change can only occur by an external action. Props are immutable from inside the component. This means that an internal attempt change the value of a prop doesn't work. This is the reason why the `setValue()` method doesn't work as expected.

There are a couple of solutions to this issue.

The first solution requires to define a new state property, say `internalValue`, and to refer to this property instead of the `value` property to generate the list of full stars. If you choose this way, your component's code will look as follows:

```typescript
import  { Component, Prop, State } from  '@stencil/core';

@Component({
   tag: 'my-rating',
   styleUrl: 'my-rating-component.css',
   shadow: true
})
export  class  MyRatingComponent  {
  @Prop() maxValue: number = 5;
  @Prop() value: number = 0;

  @State() starList: Array<object> = [];
  @State() internalValue: number;

  componentWillLoad() {
    this.createStarList(this.value);
  }

  setValue(newValue) {
    this.internalValue = newValue;
    this.createStarList(this.internalValue);
  }

  createStarList(numberOfStars: number) {
    let starList = [];

    for (let i = 1; i <= this.maxValue; i++) {
      if (i <= numberOfStars) {
        starList.push(<span class="rating" onMouseOver={() => this.createStarList(i)} onMouseOut={() => this.createStarList(this.internalValue)} onClick={() => this.setValue(i)}>&#x2605;</span>);
      } else {
        starList.push(<span class="rating" onMouseOver={() => this.createStarList(i)} onMouseOut={() => this.createStarList(this.internalValue)} onClick={() => this.setValue(i)}>&#x2606;</span>);
      }
    }

    this.starList = starList;
  }

   render() {
    return  (
      <div>
        {this.starList}
      </div>
    );
   }
}
```

This code solves the problem but adds duplicate information inside the component.

The second solution is easier to implement and is based on declaring a prop as *mutable*. You can accomplish this by simply passing the `{mutable: true}` object to the `@Prop()` decorator. In this case, your code will look as follows:

```typescript
import  { Component, Prop, State } from  '@stencil/core';

@Component({
   tag: 'my-rating',
   styleUrl: 'my-rating-component.css',
   shadow: true
})
export  class  MyRatingComponent  {
  @Prop() maxValue: number = 5;
  @Prop({mutable: true}) value: number = 0;

  @State() starList: Array<object> = [];

  componentWillLoad() {
    this.createStarList(this.value);
  }

  setValue(newValue) {
    this.value = newValue;
    this.createStarList(this.value);
  }

  createStarList(numberOfStars: number) {
    let starList = [];

    for (let i = 1; i <= this.maxValue; i++) {
      if (i <= numberOfStars) {
        starList.push(<span class="rating" onMouseOver={() => this.createStarList(i)} onMouseOut={() => this.createStarList(this.value)} onClick={() => this.setValue(i)}>&#x2605;</span>);
      } else {
        starList.push(<span class="rating" onMouseOver={() => this.createStarList(i)} onMouseOut={() => this.createStarList(this.value)} onClick={() => this.setValue(i)}>&#x2606;</span>);
      }
    }

    this.starList = starList;
  }

   render() {
    return  (
      <div>
        {this.starList}
      </div>
    );
   }
}
```

By declaring a prop as mutable you allow it to be changed from inside the component, and then the `setValue()` method will work.

## Emitting Events from Stencil Components

Stencil also allows you to emit events so that users of your component can be informed when something happens. For example, you could emit an event when the current value of your rating component changes. Let's make a few changes to the code of the component:

```typescript
import  { Component, Prop, State, EventEmitter, Event } from  '@stencil/core';

@Component({
   tag: 'my-rating',
   styleUrl: 'my-rating-component.css',
   shadow: true
})
export  class  MyRatingComponent  {
  @Prop() maxValue: number = 5;
  @Prop({ mutable: true }) value: number = 0;

  @State() starList: Array<object> = [];

  @Event() onRatingUpdated: EventEmitter;

  componentWillLoad() {
    this.createStarList(this.value);
  }

  setValue(newValue) {
    this.value = newValue;
    this.createStarList(this.value);
    this.onRatingUpdated.emit({ value: this.value });
  }

  createStarList(numberOfStars: number) {
    let starList = [];

    for (let i = 1; i <= this.maxValue; i++) {
      if (i <= numberOfStars) {
        starList.push(<span class="rating" onMouseOver={() => this.createStarList(i)} onMouseOut={() => this.createStarList(this.value)} onClick={() => this.setValue(i)}>&#x2605;</span>);
      } else {
        starList.push(<span class="rating" onMouseOver={() => this.createStarList(i)} onMouseOut={() => this.createStarList(this.value)} onClick={() => this.setValue(i)}>&#x2606;</span>);
      }
    }

    this.starList = starList;
  }

   render() {
    return  (
      <div>
        {this.starList}
      </div>
    );
   }
}
```

The first thing we notice is the import of the `EventEmitter` class and of the `@Event()` decorator. Both are used to define the `onRatingUpdated` property. This property is an instance of the `EventEmitter` class and provides a way to generate an event with the same name. In fact, we added a call to its method `emit()` inside the `setValue()` method. We also passed an object representing the current value to the `emit()` method.

You can handle the `onRatingUpdate` event as shown by the following code:

```html
<!DOCTYPE html>
<html dir="ltr" lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=5.0">
  <title>Stencil Component Starter</title>
  <script src="/build/mycomponent.js"></script>
  <script>
    window.onload = function () {
      let rating = document.getElementById("myRatingComponent");
      rating.addEventListener("onRatingUpdated", function (event) {
        alert(event.detail.value);
      })
    };
  </script>
</head>
<body>
  <my-rating id="myRatingComponent" max-value="6" value="2"></my-rating>
</body>
</html>
```

We simply added an event listener that will show an alert with the new value assigned to the rating component when the user clicks a star.

## Preparing Stencil Components for Production

Now that we have a working component and are satisfied with its features, we can prepare it for production building. Since we used the component starter project, you will find references to a generic component's name. For example, if you open the `stencil.config.js` file in a text editor, you will find the following content:

```javascript
exports.config = {
  namespace: 'mycomponent',
  outputTargets:[
    { 
      type: 'dist' 
    },
    { 
      type: 'www',
      serviceWorker: false
    }
  ]
};

exports.devServer = {
  root: 'www',
  watchGlob: '**/**'
}
```

This file contains Stencil specific config options and, among the others, you can find the generic `mycomponent` name assigned to the `namespace` property. You might want to change it into a more meaningful name, say for example `my-rating`.

Even the `package.json` file contains some generic names:

```js
{
  "name": "my-component",
  "version": "0.0.1",
  "description": "Stencil Component Starter",
  "main": "dist/mycomponent.js",
  "types": "dist/types/components.d.ts",
  "collection": "dist/collection/collection-manifest.json",
  "files": [
    "dist/"
  ],
  "browser": "dist/mycomponent.js",
  // ...
}
```

You should consistently change the reference to the generic names *my-component* and *mycomponent* with the name you prefer for your component. For example, you could change it as follows:

```json
{
  "name": "my-rating",
  "version": "0.0.1",
  "description": "A rating Web component compiled with Stencil",
  "main": "dist/my-rating.js",
  "types": "dist/types/components.d.ts",
  "collection": "dist/collection/collection-manifest.json",
  "files": [
    "dist/"
  ],
  "browser": "dist/my-rating.js",
  ...
}
```

Once you have prepared your build configuration, type the command `npm run build` to start the building task.

After a few seconds, you will find a `dist` folder in the root of the project folder with everything you need to use your brand new Web Component. This folder contains three subfolders and one file:

- `my-rating.js`: This file is the entry point of our component.
- `collection`: This folder is useful when you are building a complete application with Stencil since it allows to share components more efficiently.
- `my-rating`: This folder contains the compiled code of your Stencil component implementing a true Web Component.
- `types`: This folder contains the TypeScript definitions that may be useful when you use your component in a TypeScript application.

In other words, Stencil generates code for different production scenarios. You can bring everything every time or make a selection. Anyway, the minimal content required in a context with HTML and vanilla JavaScript consists of `my-rating.js` file and `my-rating` folder.

So, if you make these changes to your Stencil project, don't forget to update the `./src/index.html` file as follows:

```html
<!DOCTYPE html>
<html dir="ltr" lang="en">
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0, maximum-scale=5.0">
  <title>Stencil Component Starter</title>
  <script src="/build/my-rating.js"></script>
  <script>
    window.onload = function () {
      let rating = document.getElementById("myRatingComponent");
      rating.addEventListener("onRatingUpdated", function (event) {
        alert(event.detail.value);
      })
    };
  </script>
</head>
<body>
<my-rating id="myRatingComponent" max-value="6" value="2"></my-rating>
</body>
</html>
```

## Using Stencil Component with React and Angular

After building your component, you are ready to use it in your Web application. As we said at the beginning of this post, Web Components are meant to be natively supported by standard browsers as long as they support [the features required by the standard](https://caniuse.com/#search=web%20component). However, as Stencil produces code to polyfill the missing features, you can actually use your Web Component in any recent browser (e.g. Microsoft Edge).

In order to use your rating component in a plain HTML page you should simply insert a script tag in the head section with a reference to the `my-rating.js` file, as shown in the following example:

```html
<!DOCTYPE html>
<html dir="ltr" lang="en">
<head>
  <meta charset="utf-8">
  <title>My Application</title>
  <script src="myRatingComponent/my-rating.js"></script>
  <script>
    window.onload = function () {
      let rating = document.getElementById("myRatingComponent");
      rating.addEventListener("onRatingUpdated", function (event) {
        alert(event.detail.value);
      })
    };
  </script>
</head>
<body>
  <my-rating id="myRatingComponent" max-value="6" value="2"></my-rating>
</body>
</html>
```

Here we created an HTML page with the same logic we used in the development environment.

If you have an application built with React, you can still use your rating component without any problem. Assuming you have built your React app with [create-react-app](https://github.com/facebook/create-react-app), you need to copy the folder containing the distribution code (`dist`) inside the `public` folder of your project. Then open the `index.html` file and put in it the same HTML script element we've seen for the vanilla JavaScript case. Now you can use the `<my-rating>` tag inside any JSX expression in your application. For example, the following is the standard App component created by `create-react-app` with the rating component used in the JSX:

```javascript
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';

class App extends Component {
  render() {
    return (
      <div className="App">
        <header className="App-header">
          <img src={logo} className="App-logo" alt="logo" />
          <h1 className="App-title">Welcome to React</h1>
        </header>
        <p className="App-intro">
          To get started, edit <code>src/App.js</code> and save to reload.
        </p>
		<my-rating id="myRatingComponent" max-value="6" value="2"></my-rating>
      </div>
    );
  }
}

export default App;
```

Similarly, you can integrate the rating component in your Angular application. In this case, we assume that your application has been created with [Angular CLI](https://cli.angular.io/), so the folder of the Web Component should be copied in the `asset` folder of the Angular project. As usual, you need to put the script reference in the `index.html` file to make the component's code available. Now you need to change the `app.module.ts` file as in the following example:

```typescript
import { BrowserModule } from '@angular/platform-browser';
import { NgModule, CUSTOM_ELEMENTS_SCHEMA } from '@angular/core';

import { AppComponent } from './app.component';

@NgModule({
  declarations: [AppComponent],
  imports: [BrowserModule],
  providers: [],
  bootstrap: [AppComponent],
  schemas: [CUSTOM_ELEMENTS_SCHEMA]
})
export class AppModule { }
```

The `CUSTOM_ELEMENTS_SCHEMA` allows Angular to ignore unknown tags, since they are Web Components. This prevents the compiler raises errors because of the presence of `<my-rating>` tag in the application's markup. In fact, now you can use this tag in any HTML template.

{% include tweet_quote.html quote_text="Using @stenciljs web components with @reactjs and @angular is easy." %}

{% include asides/javascript-at-auth0.markdown %}

## Summary

In this post, we explored how Stencil allows us to create Web Component without too much effort. We used Stencil's syntax to incrementally define a rating component: from the definition of its appearance to the management of interactivity. In the end, we compiled the component for using in production and analyzed a few scenarios of integration.

The final code of the project developed throughout the post can be downloaded from this [GitHub repository](https://github.com/auth0-blog/rating-stencil-component).
