---
layout: post
title: "Getting Started with the New WebVR API"
description: "Learn how to create virtual reality experiences on the web using the Threejs WebGL library and the WebVR UI library."
longdescription: "In this article, you will learn how to create virtual reality experiences on the web using the Threejs WebGL library and the WebVR UI library. You will also use Auth0 to fetch users' name to show in the WebVR environment that you will create."
date: 2018-06-21 18:50
category: Technical Guide, Frontend
author:
  name: "Fikayo Adepoju"
  url: "https://twitter.com/coderonfleek"
  mail: "fik4christ@yahoo.com"
  avatar: "https://cdn.auth0.com/blog/guest-authors/fikayo-adepoju.jpeg"
design:
  bg_color: "#42007d"
  image: "https://cdn.auth0.com/blog/getting-started-with-the-new-webvr-api/logo.png"
tags:
- webvr
- webgl
- auth0
- threejs
- virtual-reality
- 3d
related:
- 2018-02-06-developing-games-with-react-redux-and-svg-part-1
- 2018-02-08-developing-games-with-react-redux-and-svg-part-2
---

**TL;DR:** One of the greatest moments for the web is when an API emerges and brings the ability to create an application on the web (with web technologies) that was only possible to develop natively. The emergence of [the WebVR API](https://developer.mozilla.org/en-US/docs/Web/API/WebVR_API) is one of such fireworks moment that opens a whole new world of exciting possibilities. The WebVR API empowers web developers to build virtual-reality experiences with web technologies and also enable users to have that fully immersed experience using just their browser.

This article takes you through a small project built with WebVR. Although you won't be going into details on the basics, it will help you to understand all the moving parts and how everything fits together to create amazing virtual reality scenes.

You can find the whole code for this application in [this GitHub](https://github.com/auth0-blog/webvr-tutorial) repository and a live sample [over here](https://plugintests-e13a7.firebaseapp.com).

{% include tweet_quote.html quote_text="Check this quick tutorial on setting up a 3D virtual reality on the web." %}

## Prerequisites

Building virtual-reality (VR) experiences in the web starts with building great WebGL (Web Graphics Layer) content. [WebGL is a browser API that allows us to design 3D graphics on the web](https://developer.mozilla.org/en-US/docs/Web/API/WebGL_API).

WebGL by itself is a very complex API to use. Luckily, libraries like [ThreeJS](https://threejs.org) are available to help you build 3D content fast and easy.

## What You Will Build

In this article, you will build an application that allows users to log in with their Google account and then be taken into a 3D world where the user sees their name floating in space.

## Setting Up Auth0

To authenticate your users with Google, you are going to use [Auth0](https://auth0.com/) and [Auth0 Lock](https://auth0.com/docs/libraries/lock/), an embeddable authentication form for desktop, tablet, and mobile devices. If you don't have an Auth0 account yet, <a href="https://auth0.com/signup" data-amp-replace="CLIENT_ID" data-amp-addparams="anonId=CLIENT_ID(cid-scope-cookie-fallback-name)">now is a good time to sign up for a free one.</a> No credit cards required! :)

### Creating an Auth0 Application

After signing up for your Auth0 account, you will have to create a new [Auth0 Application](https://auth0.com/docs/applications) to represent your WebVR app. To do this, [head to the Applications section of your management dashboard](https://manage.auth0.com/#/applications) and click on the _Create Application_ button. Then, fill out the form presented as follows:

- _Name_: Any name to identify your application. Something like "WebVR Tutorial" will do.
- _Choose an application type_: Select _Single Page Web Applications_ here.

![Creating the Auth0 Application for your WebVR app.](https://cdn.auth0.com/blog/webvr/creating-an-auth0-application.png)

After filling in this form just hit the _Create_ button. When done, Auth0 will redirect you to the _Quick Start_ section of your new Auth0 Application. From there, click on the _Settings_ tab. This will open a form with a bunch of fields. At this moment, you will only need to do one thing: add `http://localhost:8080/` to the _Allowed Callback URLs_. Just don't forget to hit the _Save_ button after making this change (you can also hit `Ctrl` + `S` or `⌘` + `S`).

## Creating the WebVR Application

Now that you already have your Auth0 account properly configured, it is time to start creating your app. So, the first thing you will do is to create a new directory called `webvr-tutorial` in your computer (from now on, this directory will be referenced as the _project root_).

```bash
# create the project root
mkdir webvr-tutorial

# move the terminal into it
cd webvr-tutorial
```

After that, you will create a new file called `index.html` in the project root and will add the following code to it:

{% highlight html %}
{% raw %}
<html>
<head>
  <title>WebVR Demo</title>
</head>
<body>
<script src="https://cdn.auth0.com/js/lock/11.5.2/lock.min.js"></script>
<script>
  const lock = new Auth0Lock("<AUTH0-CLIENT-ID>", "<AUTH0-DOMAIN>", {
    allowedConnections: ["google-oauth2"],
  });

  lock.on("authenticated", function (authResult) {
    lock.getUserInfo(authResult.accessToken, function (error, profile) {
      if (error) {
        console.log(error);
        return;
      }

      localStorage.setItem("accessToken", authResult.accessToken);
      localStorage.setItem("profile", JSON.stringify(profile));

      //Go to VR Page
      window.location.replace("stage.html");
    });
  });

  lock.on("authorization_error", function (error) {
    console.log("authorization_error", error);
  });

  lock.show();
</script>
</body>
</html>
{% endraw %}
{% endhighlight %}

> **Note:** You will have to replace `<AUTH0-CLIENT-ID>` and `<AUTH0-DOMAIN>` with your own Auth0 properties. That is, you will have to replace `<AUTH0-CLIENT-ID>` with the _Client ID_ property found in the _Settings_ tab of the Auth0 Application that you created in the previous section and you will have to replace `<AUTH0-DOMAIN>` with the domain of your Auth0 tenant (something like: `bk-samples.auth0.com`). You can also find this information in the _Settings_ tab of your Auth0 Application.

In the `<script>` section of the code above, you first set up Auth0 Lock using your Auth0 Application's _Client Id_ and _Domain_ then you instructed it to use the Google login by entering `google-oauth2` into the `allowedConnections` array.

After that, you set up a listener on the `authenticated` event. This event is then handled by calling the Lock's `getUserInfo` function and passing the access token returned from the authentication results. The callback to this function will save the access token and user profile in the browser's local storage to access later.

After the authentication process is fulfilled, you redirected your users to a page called `stage.html`. You will create your WebVR world in this page in no time.

Also, notice that you made your `index.html` call Lock's `show` method to instantly display Auth0's login box when the page loads.

## Building the Scene with WebVR

Now to the main action. You are going to build a 3D scene using a skybox, add users' name from the saved profile, and make it rotate in space right before the user's eyes.

You will make this viewable using a Head Mounted Device like Google Cardboard or a simple web browser.

### Adding the Libraries in the WebVR App

To begin, you first need to add the required libraries to build your 3D scene and use the WebVR API.

In this tutorial, you will use the following libraries:

1.  [ES6 Promise Polyfill](https://github.com/stefanpenner/es6-promise)
2.  [ThreeJS Core Library](https://github.com/mrdoob/three.js/)
3.  [WebVr Polyfill](https://github.com/immersive-web/webvr-polyfill)
4.  [Google WebVr UI](https://github.com/googlevr/webvr-ui)

So, now, create the `stage.html` file and add the following code:

{% highlight html %}
{% raw %}
<!DOCTYPE html>
<html lang="en">
<head>
  <title>WebVR Demo</title>
  <meta charset="utf-8">
  <meta name="viewport"
        content="width=device-width, user-scalable=no, minimum-scale=1.0, maximum-scale=1.0, shrink-to-fit=no">
  <meta name="mobile-web-app-capable" content="yes">
  <meta name="apple-mobile-web-app-capable" content="yes"/>
  <meta name="apple-mobile-web-app-status-bar-style" content="black-translucent"/>
  <style>
    body {
      width: 100%;
      height: 100%;
      background-color: #000;
      color: #fff;
      margin: 0px;
      padding: 0;
      overflow: hidden;
    }

    /* Position the button at the bottom of the page. */

    #ui {
      position: absolute;
      bottom: 10px;
      left: 50%;
      transform: translate(-50%, -50%);
      text-align: center;
      font-family: 'Karla', sans-serif;
      z-index: 1;
    }

    a#magic-window {
      display: block;
      color: white;
      margin-top: 1em;
    }
  </style>
</head>

<body>
<div id="ui">
  <div id="vr-button"></div>
  <a id="magic-window" href="#">Try it without a headset</a>
</div>
</body>
<script src="https://cdnjs.cloudflare.com/ajax/libs/es6-promise/4.1.1/es6-promise.min.js"></script>
<script src="http://threejs.org/build/three.js"></script>
<script src="http://threejs.org/examples/js/controls/VRControls.js"></script>
<script src="http://threejs.org/examples/js/effects/VREffect.js"></script>
<script src='https://cdn.jsdelivr.net/npm/webvr-polyfill@0.9.41/build/webvr-polyfill.js'></script>
<script src="https://googlevr.github.io/webvr-ui/build/webvr-ui.min.js"></script>
</html>
{% endraw %}
{% endhighlight %}

The code above can be described as the boilerplate markup for WebVR projects. In the `<head>` section, `meta` tags are used to set defaults that optimize and make the mobile experience responsive across supported devices.

Then, you have some basic styling for page style normalization and for styling the WebVR user interface (UI).

After that, in the `<body>` section of the page, you have the markup that holds your WebVR UI widgets. Then, after the `<body>` tag, you are including all the required libraries and polyfills.

### Setting Up 3D Scenes in WebVR

To set up your scene, you need to setup some global variables which will represent all the elements needed in your scene that needs to be accessible globally.

You will also need to declare three functions:

1. A function that runs immediately when the page is loaded and sets up the entire scene.
2. A function to handle rendering of the scene and animations.
3. A function to handle responsiveness of the application to changes in the window size.

Below is the complete code with the variable declaration and the three functions, followed by the event handler that handles the page `load` event. Place this code right after all the library that you included in the `stage.html` file:

{% highlight html %}
{% raw %}
<script>
  // Last time the scene was rendered.
  let lastRenderTime = 0;
  // Currently active VRDisplay.
  let vrDisplay;
  // How big of a box to render. **Skybox: size skybox
  const boxSize = 5;
  // Various global THREE.Objects.
  let scene;
  let cube;
  let textMesh;
  let controls;
  let effect;
  let camera;
  // EnterVRButton for rendering enter/exit UI.
  let vrButton;

  //User Profile
  const profile = JSON.parse(localStorage.getItem("profile"));

  function onLoad() {
    /* Threejs Section */
    // configuring crossOrigin
    THREE.TextureLoader.prototype.crossOrigin = '';

    // Setup three.js WebGL renderer. Note: Antialiasing is a big performance hit.
    // Only enable it if you actually need to.
    const renderer = new THREE.WebGLRenderer({antialias: true});
    renderer.setPixelRatio(window.devicePixelRatio);

    // Append the canvas element created by the renderer to document body element.
    document.body.appendChild(renderer.domElement);

    // Create a three.js scene.
    scene = new THREE.Scene();

    // Create a three.js camera.
    const aspect = window.innerWidth / window.innerHeight;
    camera = new THREE.PerspectiveCamera(75, aspect, 0.1, 10000);

    controls = new THREE.VRControls(camera);

    //*Set standing to 'true' to indicate that the user is standing
    controls.standing = true;
    //*Set the camera vertical position to the eye level of user
    camera.position.y = controls.userHeight;

    // Apply VR stereo rendering to renderer.
    effect = new THREE.VREffect(renderer);
    effect.setSize(window.innerWidth, window.innerHeight);


    //Add Skybox
    const desertGeometry = new THREE.CubeGeometry(10000, 10000, 10000);
    const desertMaterials = [
      new THREE.MeshBasicMaterial({ map: new THREE.TextureLoader().load("https://cdn.auth0.com/blog/webvr/desertsky_front.png"), side: THREE.DoubleSide }),
      new THREE.MeshBasicMaterial({ map: new THREE.TextureLoader().load("https://cdn.auth0.com/blog/webvr/desertsky_back.png"), side: THREE.DoubleSide }),
      new THREE.MeshBasicMaterial({ map: new THREE.TextureLoader().load("https://cdn.auth0.com/blog/webvr/desertsky_up.png"), side: THREE.DoubleSide }),
      new THREE.MeshBasicMaterial({ map: new THREE.TextureLoader().load("https://cdn.auth0.com/blog/webvr/desertsky_down.png"), side: THREE.DoubleSide }),
      new THREE.MeshBasicMaterial({ map: new THREE.TextureLoader().load("https://cdn.auth0.com/blog/webvr/desertsky_right.png"), side: THREE.DoubleSide }),
      new THREE.MeshBasicMaterial({ map: new THREE.TextureLoader().load("https://cdn.auth0.com/blog/webvr/desertsky_left.png"), side: THREE.DoubleSide })
    ];

    const desertMaterial = new THREE.MultiMaterial(desertMaterials);

    const desert = new THREE.Mesh(desertGeometry, desertMaterial);

    console.log(desert);

    scene.add(desert);

    const ambientLight = new THREE.AmbientLight(0xffffff);
    scene.add(ambientLight);

    /* Create 3D objects. */

    //Add name text
    const loader = new THREE.FontLoader();

    loader.load('https://cdn.auth0.com/blog/webvr/helvetiker_regular.typeface.json', function (font) {
      const textGeometry = new THREE.TextGeometry(profile.name, {
        font: font,
        size: 80,
        height: 5,
        curveSegments: 12,
        bevelEnabled: true,
        bevelThickness: 3,
        bevelSize: 2,
        bevelSegments: 5
      });

      const textMaterial = new THREE.MeshBasicMaterial();

      textMesh = new THREE.Mesh(textGeometry, textMaterial);

      textMesh.position.set(-400, controls.userHeight, -550.5);

      console.log(textMesh);

      scene.add(textMesh);
    });

    /* Screen and VR Setup */

    window.addEventListener('resize', onResize, true);
    window.addEventListener('vrdisplaypresentchange', onResize, true);

    // Initialize the WebVR UI.
    const uiOptions = {
      color: 'black',
      background: 'white',
      corners: 'square'
    };
    vrButton = new webvrui.EnterVRButton(renderer.domElement, uiOptions);
    vrButton.on('exit', function () {
      camera.quaternion.set(0, 0, 0, 1);
      camera.position.set(0, controls.userHeight, 0);
    });
    vrButton.on('hide', function () {
      document.getElementById('ui').style.display = 'none';
    });
    vrButton.on('show', function () {
      document.getElementById('ui').style.display = 'inherit';
    });
    document.getElementById('vr-button').appendChild(vrButton.domElement);
    document.getElementById('magic-window').addEventListener('click', function () {
      vrButton.requestEnterFullscreen();
    });

    navigator.getVRDisplays().then(function (displays) {
      if (displays.length > 0) {
        vrDisplay = displays[0];
        vrDisplay.requestAnimationFrame(animate);
      }
    });
  }

  // Request animation frame loop function
  function animate(timestamp) {
    const delta = Math.min(timestamp - lastRenderTime, 500);
    lastRenderTime = timestamp;

    // Apply rotation to cube mesh
    if (cube) {
      cube.rotation.y += delta * 0.0006;
    }

    if (textMesh) {
      textMesh.rotation.x += delta * 0.0003;
      textMesh.rotation.y += delta * 0.0003;
      textMesh.rotation.z += delta * 0.0003;
    }

    // Only update controls if we're presenting.
    if (vrButton.isPresenting()) {
      controls.update();
    }
    // Render the scene.
    effect.render(scene, camera);

    vrDisplay.requestAnimationFrame(animate);
  }

  function onResize(e) {
    effect.setSize(window.innerWidth, window.innerHeight);
    camera.aspect = window.innerWidth / window.innerHeight;
    camera.updateProjectionMatrix();
  }

  window.addEventListener('load', onLoad);
</script>
{% endraw %}
{% endhighlight %}

To have a better understanding of what is going on, the following section will provide a better explanation of different parts of this code.

#### **The `onLoad` Function**

A lot is going on in this function because it is the function that sets up your 3D scene and the WebVR UI.

The first this is that you do is to create a WebGL renderer and attach it to the page body. Thus your scene will fill up the whole page.

```javascript
// Setup three.js WebGL renderer. Note: Antialiasing is a big performance hit.
// Only enable it if you actually need to.
const renderer = new THREE.WebGLRenderer({antialias: true});
renderer.setPixelRatio(window.devicePixelRatio);

// Append the canvas element created by the renderer to document body element.
document.body.appendChild(renderer.domElement);
```

Then, you create a Threejs scene instance into which you can add your 3D elements.

```javascript
// Create a three.js scene.
scene = new THREE.Scene();
```

After this, you create your camera, attach VR controls to it, and set its y-axis to the standing position of the user.

```javascript
// Create a three.js camera.
const aspect = window.innerWidth / window.innerHeight;
camera = new THREE.PerspectiveCamera(75, aspect, 0.1, 10000);

controls = new THREE.VRControls(camera);

//*Set standing to 'true' to indicate that the user is standing
controls.standing = true;
//*Set the camera vertical position to the eye level of user
camera.position.y = controls.userHeight;
```

Next, you apply VR stereo rendering to your renderer.

```javascript
effect = new THREE.VREffect(renderer);
effect.setSize(window.innerWidth, window.innerHeight);
```

To create the 3D world in which the user will be immersed in, you create a skybox using the Threejs `BoxGeometry` and map materials of a desert environment around it. Finally, you add it to the scene.

```javascript
//Add Skybox
const desertGeometry = new THREE.CubeGeometry(10000, 10000, 10000);
const desertMaterials = [
  new THREE.MeshBasicMaterial({ map: new THREE.TextureLoader().load("https://cdn.auth0.com/blog/webvr/desertsky_front.png"), side: THREE.DoubleSide }),
  new THREE.MeshBasicMaterial({ map: new THREE.TextureLoader().load("https://cdn.auth0.com/blog/webvr/desertsky_back.png"), side: THREE.DoubleSide }),
  new THREE.MeshBasicMaterial({ map: new THREE.TextureLoader().load("https://cdn.auth0.com/blog/webvr/desertsky_up.png"), side: THREE.DoubleSide }),
  new THREE.MeshBasicMaterial({ map: new THREE.TextureLoader().load("https://cdn.auth0.com/blog/webvr/desertsky_down.png"), side: THREE.DoubleSide }),
  new THREE.MeshBasicMaterial({ map: new THREE.TextureLoader().load("https://cdn.auth0.com/blog/webvr/desertsky_right.png"), side: THREE.DoubleSide }),
  new THREE.MeshBasicMaterial({ map: new THREE.TextureLoader().load("https://cdn.auth0.com/blog/webvr/desertsky_left.png"), side: THREE.DoubleSide })
];

const desertMaterial = new THREE.MultiMaterial(desertMaterials);

const desert = new THREE.Mesh(desertGeometry, desertMaterial);

console.log(desert);

scene.add(desert);
```

Then, you add lighting to your scene. For this scene, all you need is the ambient light. So you create a white colored light and add it to your scene.

```javascript
const ambientLight = new THREE.AmbientLight(0xffffff);
scene.add(ambientLight);
```

Then, you add the users' name in space using a `TextGeometry` after loading your desired font and add it to the scene. Note that you get users' name from the profile data stored in `localStorage`.

```javascript
//Add name text
const loader = new THREE.FontLoader();

loader.load('https://cdn.auth0.com/blog/webvr/helvetiker_regular.typeface.json', function (font) {
  const textGeometry = new THREE.TextGeometry(profile.name, {
    font: font,
    size: 80,
    height: 5,
    curveSegments: 12,
    bevelEnabled: true,
    bevelThickness: 3,
    bevelSize: 2,
    bevelSegments: 5
  });

  const textMaterial = new THREE.MeshBasicMaterial();
  textMesh = new THREE.Mesh(textGeometry, textMaterial);
  textMesh.position.set(-400, controls.userHeight, -550.5);
  console.log(textMesh);
  scene.add(textMesh);
});
```

Next thing you do is setup event handlers to handle window resizing and you also set up your WebVR UI.

```javascript
window.addEventListener('resize', onResize, true);
window.addEventListener('vrdisplaypresentchange', onResize, true);

// Initialize the WebVR UI.
const uiOptions = {
  color: 'black',
  background: 'white',
  corners: 'square'
};
vrButton = new webvrui.EnterVRButton(renderer.domElement, uiOptions);
vrButton.on('exit', function () {
  camera.quaternion.set(0, 0, 0, 1);
  camera.position.set(0, controls.userHeight, 0);
});
vrButton.on('hide', function () {
  document.getElementById('ui').style.display = 'none';
});
vrButton.on('show', function () {
  document.getElementById('ui').style.display = 'inherit';
});
document.getElementById('vr-button').appendChild(vrButton.domElement);
document.getElementById('magic-window').addEventListener('click', function () {
  vrButton.requestEnterFullscreen();
});
```

Lastly, you check for VR displays on the browser `navigator` and call your `animate` function with the display API.

```javascript
navigator.getVRDisplays().then(function(displays) {
  if (displays.length > 0) {
    vrDisplay = displays[0];
    vrDisplay.requestAnimationFrame(animate);
  }
});
```

This function needs to be called immediately when the page loads thus you set up an event handler to handle that:

```javascript
window.addEventListener('load', onLoad);
```

#### **The `animate` Function**

```javascript
function animate(timestamp) {
  const delta = Math.min(timestamp - lastRenderTime, 500);
  lastRenderTime = timestamp;

  // Apply rotation to cube mesh
  if (cube) {
    cube.rotation.y += delta * 0.0006;
  }

  if (textMesh) {
    textMesh.rotation.x += delta * 0.0003;
    textMesh.rotation.y += delta * 0.0003;
    textMesh.rotation.z += delta * 0.0003;
  }

  // Only update controls if we're presenting.
  if (vrButton.isPresenting()) {
    controls.update();
  }
  // Render the scene.
  effect.render(scene, camera);

  vrDisplay.requestAnimationFrame(animate);
}
```

This function handles the rendering and re-rendering of the scene and also animations on the page. Anytime the page renders, you update the controls, change the position properties of the Text Mesh to create an animated effect and then call your `render` function to render or re-render the scene.
You use `requestAnimationFrame` to ensure that your animations are optimized.

#### **The `onResize` function**

```javascript
function onResize(e) {
  effect.setSize(window.innerWidth, window.innerHeight);
  camera.aspect = window.innerWidth / window.innerHeight;
  camera.updateProjectionMatrix();
}
```

This simple function simply re-calibrates the camera position settings and page dimensions anytime the display is resized.

Done! That's all the code you need for the project. Now you can proceed to test it in a web browser.

## Testing the WebVR App

To test your WebVR application, you can take advantage of tools like [`http-server`](https://github.com/indexzero/http-server). If you do have Node.js and NPM installed in your machine, you can install `http-server` globally with this command:

```bash
npm install http-server -g
```

Then, you can go to your project root and type:

```bash
http-server
```

This will run a lightweight HTTP server that you can access in web browser heading to [`http://localhost:8080/`](http://localhost:8080/). Accessing this page will bring the login form of Auth0 where you will be able to sign in with your Google account.

After the authentication process, you will see a screen like this one:

![WebVR application up and running.](https://cdn.auth0.com/blog/webvr/webvr-application-up-and-running.png)

Then, if you click on the _Try it without a headset_ link, you will be able to look around by clicking and moving your mouse.

Nice right?

{% include tweet_quote.html quote_text="I just built a 3D world on the web with WebVR." %}

## Conclusion

The future of virtual reality is on the web. The web offers a simple and quick way for the user to easily get immersed in a virtual reality. As the WebVR API continues to evolve, the future is exciting for developers developing virtual experiences on the web.
