---
layout: post
title: "Developing Well-Organized APIs with Node.js, Joi, and Mongo"
description: "In this article, you will learn how to create well-organized APIs with Node.js, Restify, Joi, and MongoDB."
date: 2018-06-07 08:30
category: Technical Guide, Backend, NodeJS
author:
  name: "Biodun Chris"
  url: "https://twitter.com/biodunch"
  avatar: "https://cdn.auth0.com/blog/guest-author/biodun-chris.jpeg"
design:
  bg_color: "#333333"
  image: https://cdn.auth0.com/blog/logos/node.png
tags:
- nodejs
- mongo
- joi
- restify
- mongoose
- api
- rest
- backend
- node
- di
- dependency-injection
related:
- 2018-05-02-nodejs-10-new-changes-deprecations
- 2018-03-29-vuejs-kanban-board-the-development-process
- 2016-11-21-building-and-authenticating-nodejs-apps
---

**TL;DR:** In this article, you will learn how to build well-organized APIs with [Node.js](https://nodejs.org/), [Restify](https://restify.com), [Joi](https://github.com/hapijs/joi), and [MongoDB](https://mongodb.org). You will also have a chance to learn how to use dependency injection on this stack. If needed, [you can find the code developed throughout this article in this GitHub repository](https://github.com/auth0-blog/nodejs-restify).

{% include tweet_quote.html quote_text="Learn how to build well-organized APIs with @nodejs, Restify, Joi, and @MongoDB." %}

## Developing Well-Organized APIs with Node.js

In this article, you will build a birthdates API that lets you register and query birthdays from the people you know.

Structuring Node.js APIs has been a pain most beginner Node.js developers have been through. So, in the next sections, you will learn how to properly structure and organise a RESTful Node.js API with some cool tools like Restify, Joi, and MongoDB. 

## What Tools Will You Use

As mentioned, you will use tools like Restify, Joi, and MongoDB. This section provides a brief overview of these tools.

### Restify

[Restify](http://restify.com/) is a Node.js web service framework optimized for building semantically-correct RESTful web services ready for production. If you check their official web page, you will see that big companies like Netflix and Pinterest use Restify in their applications.

### Joi

You will use [Joi](https://github.com/hapijs/joi) to validate `params`, `queries`, and the `body` of requests. Joi, seating at the route level, basically check for correctness of the requests sent to the API. Only requests that are semantically correct reach the controllers of your application.

### MongoDB

MongoDB is a cross-platform and open-source document-oriented database (also known as a NoSQL database). You will use MongoDB to persist and retrieve the data (birthdates) managed by the API that you will build throughout the article.

## Building the Birthdates API

Firstly, you will need to check if you have Node.js, NPM, and a MongoDB instance installed on your development machine:

```bash
node --version
npm --version
mongo --version
```

If you don't have Node.js and NPM, go [grab them from this URL](https://nodejs.org/en/download/). If you don't have MongoDB, you can find instructions on [how to install it in this URL](https://docs.mongodb.com/manual/installation/).

### Birthdates API Endpoints

After checking the dependencies, you can start building your application. In the end, you will have an API with the following endpoints:

* `POST` `/users`: An endpoint to create new users.
* `GET` `/users/{username}`: An endpoint to retrieve users by their usernames. 
* `POST` `/birthdates`: And endpoint to register new birthdates.
* `POST` `/birthdates/{username}`: An endpoint to retrieve the birthdate of a certain user.

### Setup the Project Structure

You will start the creation of your app from scratch. So, create a new directory called `birthdates-api` (this will be referenced as the project root) and execute the following commands:


```bash
# if you haven't yet, create the project root
mkdir birthdates-api

# move the terminal into it
cd birthdates-api

# create subdirectories
mkdir -p app/configs app/controllers app/lib app/middlewares \
  app/models app/routes app/services app/validations
```

Next, you will need to initialize the project with `npm` so you get a `package.json` file to describe your project and manage its dependencies. So, execute the command below in the project root and follow the prompt (set the `entry point` to `server.js`):

```bash
# answer all questions asked by NPM
npm init
```

### Installing Dependencies

After initialising your project with NPM, you can install its dependencies:

```bash
npm install --save mongoose restify joi http-status restify-errors restify-url-semver winston@next dotenv
```

The list below provides a brief explanation of what these libraries do:

* `mongoose` is a MongoDB object modeling tool designed to work in an asynchronous environment.
* `http-status` is a utility to interact with HTTP status code.
* `restify-errors` is a library that contains sets of error contructors to create new error objects with their default status codes.
* `restify-url-semver` is a library used for versioning the Restify API.
* `winston` is a universal logging library with support for multiple transports.
* `dotenv` is a tiny package that loads environment variables from `.env` file into `process.env`.

### Creating the App Configuration

An app’s configuration is everything that is likely to vary between environments. So, using environment variables makes it easy to change values of configs depending on the environment without having to change any code.

As such, you will create a new file called `.env` in the project root and add to it the following variables:

```bash
NODE_PATH=.
APPLICATION_ENV=development

APP_NAME=birthdate-api
APP_PORT=5000
LOG_PATH=logs/birthdate-api.log
LOG_ENABLE_CONSOLE=true

DB_PORT=27017
DB_HOST=localhost
DB_NAME=birthdates
```

Next, you will load these variables in a `configs.js` file. So, inside the `./app/configs` directory, create the `configs.js` file and add the code below:

```js
'use strict';

module.exports = () => ({
  app: {
    name: process.env.APP_NAME,
    port: process.env.APP_PORT || 8000,
    environment: process.env.APPLICATION_ENV,
    logpath: process.env.LOG_PATH,
  },
  mongo: {
    port: process.env.DB_PORT,
    host: process.env.DB_HOST,
    name: process.env.DB_NAME
  },
  application_logging: {
    file: process.env.LOG_PATH,
    level: process.env.LOG_LEVEL || 'info',
    console: process.env.LOG_ENABLE_CONSOLE || true
  }
});
```

Node.js environment variables are always loaded into the `process.env` object. So, to access any variable declared in `.env` file, all you need to do is to call `process.env.VAR_NAME`. That is, if you want to change a variable due to change in the environment, you don't need to modify the `config` file. You just need to modify the `.env` file.

### Setting Up the Restify Server

The first thing you will do to set up your new Restify Server is to create a file called `jsend.js` in the `./app/lib` directory. Inside this file, you will add the following code:

```js
'use strict';

function formatJSend(req, res, body) {
  function formatError(res, body) {
    const isClientError = res.statusCode >= 400 && res.statusCode < 500;
    if (isClientError) {
      return {
        status: 'error',
        message: body.message,
        code: body.code
      };
    } else {
      const inDebugMode = process.env.NODE_ENV === 'development';

      return {
        status: 'error',
        message: inDebugMode ? body.message : 'Internal Server Error',
        code: inDebugMode ? body.code : 'INTERNAL_SERVER_ERROR',
        data: inDebugMode ? body.stack : undefined
      };
    }
  }

  function formatSuccess(res, body) {
    if (body.data && body.pagination) {
      return {
        status: 'success',
        data: body.data,
        pagination: body.pagination,
      };
    }

    return {
      status: 'success',
      data: body
    };
  }

  let response;
  if (body instanceof Error) {
    response = formatError(res, body);
  } else {
    response = formatSuccess(res, body);
  }

  response = JSON.stringify(response);
  res.header('Content-Length', Buffer.byteLength(response));
  res.header('Content-Type', 'application/json');

  return response;
}

module.exports = formatJSend;
```

The main function, `formatJSend` (the other two are used only by this function), checks the body of the response to see if it's an error type or not. If it is, it calls the `formatError` function that construct a `jsend` compliant error response with the HTTP status code. Otherwise, the `formatSuccess` is called for every successful request transaction and formatted following the `jsend` json response specification.

Your formatter overrides the default Restify formatter for `content-type` of `application/json`.

Now, you can create a `server.js` file in the project root to start the Restify server and use your `jsend` formatter.

```js
'use strict';

require('dotenv').config();
const config = require('./app/configs/configs')();
const restify = require('restify');
const versioning = require('restify-url-semver');

// Initialize and configure restify server
const server = restify.createServer({
  name: config.app.name,
  versions: ['1.0.0'],
  formatters: {
    'application/json': require('./app/lib/jsend')
  }
});

// Set API versioning and allow trailing slashes
server.pre(restify.pre.sanitizePath());
server.pre(versioning({prefix: '/'}));

// Set request handling and parsing
server.use(restify.plugins.acceptParser(server.acceptable));
server.use(restify.plugins.queryParser());
server.use(
  restify.plugins.bodyParser({
    mapParams: false
  })
);

// start server
server.listen(config.app.port, () => {
  console.log(`${config.app.name} Server is running on port - 
    ${config.app.port}`);
});
```

The first thing you did was to load the variables from the `.env` file into `process.env` once the app starts up. Then, you started importing dependencies and creating the server with the configuration you created previously.

> Unix users can use `export $(cat .env | sed -e /^$/d -e /^#/d | xargs)` in the project root to load the `.env` variables to shell environment.

Inside the project root, you can start the server with this command:

```bash
node server.js
```

If it was successful, you should see this in your console:

![Node.js application running](https://cdn.auth0.com/blog/nodejs-apis/server-running.png)

### Setting up a Logger

Now, you will create a wrapper for the `winston` library to format the output of the logs and create transport based on the environment.

So, create a file called `logger.js` inside `./app/lib/` and add the following code:

```js
'use strict';

const {createLogger, format, transports} = require('winston');
const {combine, timestamp, label, prettyPrint} = format;

const createTransports = function (config) {
  const customTransports = [];

  // setup the file transport
  if (config.file) {

    // setup the log transport
    customTransports.push(
      new transports.File({
        filename: config.file,
        level: config.level
      })
    );
  }

  // if config.console is set to true, a console logger will be included.
  if (config.console) {
    customTransports.push(
      new transports.Console({
        level: config.level
      })
    );
  }

  return customTransports;
};

module.exports = {
  create: function (config) {
    return new createLogger({
      transports: createTransports(config),
      format: combine(
        label({label: 'Birthdates API'}),
        timestamp(),
        prettyPrint()
      )
    });
  }
};
```

For a better understanding on how to create Winston logger, you can have a look at the [official Winston documentation](http://github.com/winstonjs/winston). 

For this tutorial, you created two transports; `console` and `file`. Both transports use the log level you set in your environment variable which can be overridden in the code with `logger.log_level(...)` (e.g. `logger.info(...)` instead of `logger.log(...)`).

Based on the environment, you can set the value of console logging to false or true in the `.env` file. Again, for this tutorial, you enabled logging to the console so you can see all your logs in the console without having to tail a log file. However, logging to the console is not advisable in a production environment.

### Setting Up Dependency Injection

Instead of having your objects creating a dependency, you can pass the needed dependencies into the object externally. To achieve this, you can use an object higher up in the dependency graph or a dependency injector (library) to pass the dependencies. 

For this tutorial, you will use a custom library called `service_locator` to inject dependencies into the objects that need to hold a reference to them.

So, create a file called `service_locator.js` inside the `./app/lib/` directory and add the following code:

```js
'use strict';

function ServiceLocator() {
  this.dependencyMap = {};
  this.dependencyCache = {};
}

ServiceLocator.prototype.register = function (dependencyName, constructor) {
  if (typeof constructor !== 'function') {
    throw new Error(dependencyName + ': Dependency constructor is not a function');
  }

  if (!dependencyName) {
    throw new Error('Invalid depdendency name provided');
  }

  this.dependencyMap[dependencyName] = constructor;
};

ServiceLocator.prototype.get = function (dependencyName) {
  if (this.dependencyMap[dependencyName] === undefined) {
    throw new Error(dependencyName + ': Attempting to retrieve unknown dependency');
  }

  if (typeof this.dependencyMap[dependencyName] !== 'function') {
    throw new Error(dependencyName + ': Dependency constructor is not a function');
  }

  if (this.dependencyCache[dependencyName] === undefined) {
    const dependencyConstructor = this.dependencyMap[dependencyName];
    const dependency = dependencyConstructor(this);
    if (dependency) {
      this.dependencyCache[dependencyName] = dependency;
    }
  }

  return this.dependencyCache[dependencyName];
};

ServiceLocator.prototype.clear = function () {
  this.dependencyCache = {};
  this.dependencyMap = {};
};

module.exports = new ServiceLocator();
```

The following list provides a brief analysis of the code above:

* The `register` method takes in the dependency name and its constructor, then proceeds to add it to the `dependencyMap` object initialized in your `ServiceLocator` constructor.
* The `get` method retrieves a dependency from the `dependencyMap` object that matches the name passed in as the function argument. If the requested dependency is not in the cache, it initializes the dependency and adds it to the cache then returns it.
* The `clear` method basically just removes all dependencies from the map and from the cache.

You will use the `serviceLocator` object to manage our app dependencies.

Now, you can proceed to create a file called `di.js` (Dependency Injection) inside the `./app/configs/` where you will initialize all your app dependencies.

```js
'use strict';

const serviceLocator = require('../lib/service_locator');
const config = require('./configs')();

serviceLocator.register('logger', () => {
  return require('../lib/logger').create(config.application_logging);
});

serviceLocator.register('httpStatus', () => {
  return require('http-status');
});

serviceLocator.register('mongoose', () => {
  return require('mongoose');
});

serviceLocator.register('errs', () => {
  return require('restify-errors');
});

module.exports = serviceLocator;
```

Calling the `register` method adds a dependency to the dependency graph that can be retrieved by calling the `get` method with the `dependencyName`.

{% include tweet_quote.html quote_text="Using Dependency Injection with @nodejs is easy. You don't even need an NPM package for this." %}

### Setting Up a Database

Before you proceed, you will need to start a MongoDB server in a separate terminal window and leave it running:

```bash
mongod
```

> **Note:** You can also use a service like [mLab](https://mlab.com/) or you can [use Docker to initiliase a MongoDB instance](https://github.com/brunokrebs/cheat-sheet/tree/master/docker#mongodb-commands).

If the command above fails, check out their [documentation](https://docs.mongodb.com/manual/tutorial/) on how to start the server for your OS.

Then, create a file called `database.js` inside the `./app/configs/`directory with the following code:

```js
'use strict';

const serviceLocator = require('../lib/service_locator');
const logger = serviceLocator.get('logger');

class Database {
  constructor(port, host, name) {
    this.mongoose = serviceLocator.get('mongoose');
    this._connect(port, host, name);
  }

  _connect(port, host, name) {
    this.mongoose.Promise = global.Promise;
    this.mongoose.connect(`mongodb://${host}:${port}/${name}`);
    const {connection} = this.mongoose;
    connection.on('connected', () =>
      logger.info('Database Connection was Successful')
    );
    connection.on('error', (err) =>
      logger.info('Database Connection Failed' + err)
    );
    connection.on('disconnected', () =>
      logger.info('Database Connection Disconnected')
    );
    process.on('SIGINT', () => {
      connection.close();
      logger.info(
        'Database Connection closed due to NodeJs process termination'
      );
      process.exit(0);
    });

    // initialize Model
    require('../models/Users');
  }
}

module.exports = Database;
```

This class has just a single method that connects to the running MongoDB server. Note that you added some listeners for series of events (like `error` or `disconnected`) that might occur in the server lifecycle. Lastly, in the `_connect()` method, you initialized your `users` model. You will create the `./app/models/Users` module now.

For your Birthdates API, you need to define a model, `Users`, and a submodel, `Birthdates`. The `Users` model will hold the properties of the user using the API (e.g. `username`, `birthdate`, and `birthdates`). The `birthdates` submodel will define the `fullname` and `birthdates` of your friends and you will embed it as an array inside the `Users` model.

So, create the `Users.js` file inside the `./app/models/` directory and add this code:

```js
'use strict';

const config = require('../configs/configs');
const serviceLocator = require('../lib/service_locator');
const mongoose = serviceLocator.get('mongoose');

const birthdatesSchema = new mongoose.Schema({
  fullname: {
    type: String,
    trim: true,
    required: true
  },
  birthdate: {
    type: Date,
    required: true
  }
});

const userSchema = new mongoose.Schema({
    username: {
      type: String,
      trim: true,
      required: true,
      unique: true,
      lowercase: true
    },
    birthdate: {
      type: Date,
      required: true
    },
    birthdates: [birthdatesSchema]
  },
  {
    timestamps: true
  }
);

module.exports = mongoose.model('Users', userSchema);
```

In this file, you defined two schemas:

* `birthdatesSchema`: This used as a subdocument in the users model defining the `fullname` of the person whose birthdates you want to save and the `birthdates` properties.
* `userSchema`: You defined `username` of the user, their `birthdate`, and an array of `birthdates` from the `birthdatesSchema`. You also enabled `timestamp` which automatically adds `created_at` and `updated_at` properties to every document.

### Developing the API Source Code

Next, you will set up `services`, `controllers`, and `routers` to handle requests. Inside the `./app/services` directory, you will create the `user` service to handle business related to the `users` endpoint (such as creating users, fetching users, etc). So, create a file called `user.js` inside this directory and add the following code to it:

```js
'use strict';

class UserService {
  constructor(log, mongoose, httpStatus, errs) {
    this.log = log;
    this.mongoose = mongoose;
    this.httpStatus = httpStatus;
    this.errs = errs;
  }

  async createUser(body) {
    const Users = this.mongoose.model('Users');
    const {username} = body;
    const user = await Users.findOne({username});

    if (user) {
      const err = new this.errs.InvalidArgumentError(
        'User with username already exists'
      );
      return err;
    }

    let newUser = new Users(body);
    newUser.birthdate = new Date(body.birthdate);
    newUser = await newUser.save();

    this.log.info('User Created Successfully');
    return newUser;
  }

  async getUser(username) {
    const Users = this.mongoose.model('Users');
    const user = await Users.findOne({username});

    if (!user) {
      const err = new this.errs.NotFoundError(
        `User with username - ${username} does not exists`
      );
      return err;
    }

    this.log.info('User fetched Successfully');
    return user;
  }
}

module.exports = UserService;
```

This is a brief analysis over this file:

* The `constructor` initalizes all the dependencies passed to it from your dependency injection file when creating the object.
* The `createUser` method checks if the user with the username in the request body exists then throws an error that 'User with username already exists' already exists. Otherwise, it proceeds to save the user and returns the result to the controller.
* The `getUser` method fetches the user that matches the `username` provided and returns the result to the controller.

Now, you have the chunks of code to create a user and get a specified user. So, you can proceed to create the controller to implement this service. 

By the way, noticed how you were able to receive and initialize the dependencies of the `user service` inside its constructor? The dependency injection makes that possible, you will get to that part soon.

Now, inside the `./app/controllers/` directory, create another `user.js` file. Now, insert this code inside it:

```js
'use strict';

class UserController {
    constructor(log, userService, httpSatus) {
        this.log = log;
        this.userService = userService;
        this.httpSatus = httpSatus;
    }

    async create(req, res) {
        try {
            const { body } = req;
            const result = await this.userService.createUser(body);
            
            res.send(result);
        } catch (err) {
            this.log.error(err.message);
            res.send(err);
        }
    }

    async get(req, res) {
        try{
            const { username } = req.params;
            const result = await this.userService.getUser(username);
            
            res.send(result);
        } catch (err) {
            this.log.error(err.message);
            res.send(err);
        }
    }

}

module.exports = UserController;
```

The controller basically calls the services to perform particular actions and sends the response back to the client.

Now, you can create the birthdate service and controller to save and fetch birthdates. So, inside the `./app/services` directory create a file called `birthdates.js` and add this:

```js
'use strict';

class BirthdateService {
  constructor(log, mongoose, httpStatus, errs) {
    this.log = log;
    this.mongoose = mongoose;
    this.httpStatus = httpStatus;
    this.errs = errs;
  }

  async createBirthdate(username, body) {
    const Users = this.mongoose.model('Users');
    const user = await Users.findOne({username});
    const {birthdate, fullname} = body;

    if (!user) {
      const err = new this.errs.NotFoundError(
        `User with username - ${username} does not exists`
      );
      return err;
    }

    user.birthdates.push({
      birthdate: this.formatBirthdate(birthdate),
      fullname
    });

    return user.save();
  }

  formatBirthdate(date) {
    return new Date(date);
  }

  async getBirthdates(username) {
    const Users = this.mongoose.model('Users');
    const user = await Users.findOne({username});

    if (!user) {
      const err = new this.errs.NotFoundError(
        `User with username - ${username} does not exists`
      );
      return err;
    }

    return user.birthdates;
  }
}

module.exports = BirthdateService;
```

The following list provides a brief analysis over the code above:

* The `constructor` method initalizes all the dependencies passed to it from your dependency injection file when creating the object.
* The `createBirthdate` method fetches the user that made the request, then saves the `birthdates` with the `fullname` and returns the result to the Birthdate controller completing the request.
* The `getBirthdates` method gets all the `birthdates` of the user that made the request and returns the result to the Birthdate controller.

Now, create the `birthdates.js` file inside the `./app/controllers/` directory with this code:

```js
'use strict';

const serviceLocator = require('../lib/service_locator');

class BirthdateController {
  constructor(log, birthdateService, httpSatus) {
    this.log = log;
    this.birthdateService = birthdateService;
    this.httpSatus = httpSatus;
  }

  async create(req, res) {
    try {
      const {body} = req;
      const {username} = req.params;
      const result = await this.birthdateService.createBirthdate(
        username,
        body
      );
      if (result instanceof Error)
        res.send(result);
      else res.send(`${body.fullname}'s birthdate saved successfully!`)
    } catch (err) {
      this.log.error(err.message);
      res.send(err);
    }
  }

  async listAll(req, res) {
    try {
      const {username} = req.params;
      const result = await this.birthdateService.getBirthdates(username);
      res.send(result);
    } catch (err) {
      this.log.error(err.message);
      res.send(err);
    }
  }
}

module.exports = BirthdateController;
```

Now, you need to register the services and controllers you just created in your `serviceLocator` module. You will do this so you can inject dependencies with ease.

So, open up the `./app/configs/di.js` file and insert the following code just before the last line (i.e. before `module.exports = serviceLocator;`):

```js
// ... leave the rest above untouched ...

serviceLocator.register('birthdateService', (serviceLocator) => {
    const log = serviceLocator.get('logger');
    const mongoose = serviceLocator.get('mongoose');
    const httpStatus = serviceLocator.get('httpStatus');
    const errs = serviceLocator.get('errs');
    const BirthdateService = require('../services/birthdates');

    return new BirthdateService(log, mongoose, httpStatus, errs);
});

serviceLocator.register('userService', (serviceLocator) => {
    const log = serviceLocator.get('logger');
    const mongoose = serviceLocator.get('mongoose');
    const httpStatus = serviceLocator.get('httpStatus');
    const errs = serviceLocator.get('errs');
    const UserService = require('../services/user');

    return new UserService(log, mongoose, httpStatus, errs);
});

serviceLocator.register('birthdateController', (serviceLocator) => {
    const log = serviceLocator.get('logger');
    const httpStatus = serviceLocator.get('httpStatus');
    const birthdateService = serviceLocator.get('birthdateService');
    const BirthdateController = require('../controllers/birthdates');

    return new BirthdateController(log, birthdateService, httpStatus);
});

serviceLocator.register('userController', (serviceLocator) => {
    const log = serviceLocator.get('logger');
    const httpStatus = serviceLocator.get('httpStatus');
    const userService = serviceLocator.get('userService');
    const UserController = require('../controllers/user');

    return new UserController(log, userService, httpStatus);
});

module.exports = serviceLocator;
```

You are almost ready to take this baby for a spin!!! But, lastly, you have to set up routes and request validation with `joi`.

### Setting Up Validation With Joi

If you are not familiar with Joi, you can check out [their GitHub repo](https://github.com/hapijs/joi#example) for a quick start. But, as you will see, it's easy to use Joi.

First, you will need to create blueprints or schemas for JavaScript objects (an object that stores information) to ensure validation of key information. Joi runs validation against the rule you declared in the schemas for all requests.

So, inside the `./app/validations/` directory, create a file called `create_user.js` and add this code to it:

```js
'use strict';

const joi = require('joi');

module.exports = joi.object().keys({
  username: joi.string().alphanum().min(4).max(15).required(),
  birthdate: joi.date().required()
}).required();
```

The above schema defines the following constraints:

* `username`
    * a required string;
    * must contain only alphanumeric characters;
    * at least 4 characters long but no more than 15;
* `birthdate`
    * a required date field;

Now that you know how `joi` works, you can create validations for the remaining endpoints. So, create a file called `create_birthdates.js` inside the `./app/validations` directory and add this code to it:

```js
'use strict';

const joi = require('joi');

module.exports = joi.object().keys({
    fullname: joi.string().min(5).max(60).required(),
    birthdate: joi.date()
}).required();
```

Then, create a file called `get_birthdates-user.js` inside the same directory and add this:

```js
'use strict';

const joi = require('joi');

module.exports = {
    username: joi.string().alphanum().min(4).max(30).required()
};
```

After defining the validation rules, you will need to create a tiny library to run the validation everytime request is made. So, inside the `./app/lib/` directory, create a file called `validator.js` and add this:

```js
'use strict';

let httpStatus = require('http-status');
let errors = require('restify-errors');

module.exports.paramValidation = function (log, joi) {

  return function (req, res, next) {

    // always allow validation to allow unknown fields by default.
    let options = {
      allowUnknown: true
    };

    let validation = req.route.spec.validation; //validation object in route
    if (!validation) {
      return next(); // skip validation if not set
    }

    let validProperties = ['body', 'query', 'params'];

    for (let i in validation) {
      if (validProperties.indexOf(i) < 0) {
        log.debug('Route contains unsupported validation key');
        throw new Error('An unsupported validation key was set in route');

      } else {
        if (req[i] === undefined) {
          log.debug('Empty request ' + i + ' was sent');

          res.send(
            httpStatus.BAD_REQUEST,
            new errors.InvalidArgumentError('Missing request ' + i)
          );
          return;
        }

        let result = joi.validate(req[i], validation[i], options);

        if (result.error) {
          log.debug('validation error - %s', result.error.message);

          res.send(
            httpStatus.BAD_REQUEST,
            new errors.InvalidArgumentError(
              'Invalid request ' + i + ' - ' + result.error.details[0].message
            )
          );
          return;

        } else {
          log.info('successfully validated request parameters');
        }
      }
    }

    next();
  };
};
```

As you can see, firstly, the function retrieves the `validation` property defined in the route `spec`. Then, you proceed to check if the `request` object contains a valid property (key e.g. `body`, `query` or `params`). If it does, you validate the value obtained from the `request` object with the predefined set of rules (schema) for the request path. If the input is invalid, the result will be an `Error` object.

Now, you can define the routes and provide the validation rules for all the paths. So, inside `./app/routes/`, create the `routes.js` file and add this:

```js
'use strict';

module.exports.register = (server, serviceLocator) => {

  server.post(
    {
      path: '/users',
      name: 'Create User',
      version: '1.0.0',
      validation: {
        body: require('../validations/create_user')
      }
    },
    (req, res, next) =>
      serviceLocator.get('userController').create(req, res, next)
  );

  server.get(
    {
      path: '/users/:username',
      name: 'Get User',
      version: '1.0.0',
      validation: {
        params: require('../validations/get_birthdates-user.js')
      }
    },
    (req, res, next) =>
      serviceLocator.get('userController').get(req, res, next)
  );

  server.get(
    {
      path: '/birthdates/:username',
      name: 'Get Birthdates',
      version: '1.0.0',
      validation: {
        params: require('../validations/get_birthdates-user.js')
      }
    },
    (req, res, next) =>
      serviceLocator.get('birthdateController').listAll(req, res, next)
  );

  server.post(
    {
      path: '/birthdates/:username',
      name: 'Create Birthdate',
      version: '1.0.0',
      validation: {
        body: require('../validations/create_birthdates')
      }
    },
    (req, res, next) =>
      serviceLocator.get('birthdateController').create(req, res, next)
  );
};
```

Now, you have four endpoints defined:

- `POST` `/users`;
- `GET` `/users/{username}`;
- `GET` `/birthdates/{username}`;
- `POST` `/birthdates/{username}`;

After defining the route specification in the first arguments, you specify the controller to handle the request to that particular path.

{% include tweet_quote.html quote_text="Joi can help you keep your data consistent by validating requests in @nodejs applications." %}

### Handling Restify Errors

Restify has built-in error event listener that get fired when an `Error` is encountered by restify as part of a `next(error)` statement. So, now you need to add handlers for possible errors you want to listen for.

So, inside `./app/lib/`, create `error_handler.js` and add this:

```js
'use strict';

module.exports.register = (server) => {
    var httpStatusCodes = require('http-status');

    server.on('NotFound', (req, res) => {
        res.send(
            httpStatusCodes.NOT_FOUND,
            new Error('Method not Implemented', 'METHOD_NOT_IMPLEMENTED')
        );
    });

    server.on('VersionNotAllowed', (req, res) => {
        res.send(
            httpStatusCodes.NOT_FOUND,
            new Error('Unsupported API version requested', 'INVALID_VERSION')
        );
    });

    server.on('InvalidVersion', (req, res) => {
        res.send(
            httpStatusCodes.NOT_FOUND,
            new Error('Unsupported API version requested', 'INVALID_VERSION')
        );
    });

    server.on('MethodNotAllowed', (req, res) => {
        res.send(
            httpStatusCodes.METHOD_NOT_ALLOWED,
            new Error('Method not Implemented', 'METHOD_NOT_ALLOWED')
        );
    });

    server.on('restifyError', (req, res) => {
        res.send(httpStatusCodes.INTERNAL_SERVER_ERROR, err);
    });
};
```

Whenever an error is encountered in your application, the appropriate error handler is called. If an error not defined above occurs, the last handler you declared, `restifyError`, will catch it.

### Wrapping Up

To wrap up your Node.js API, you will need to update the `server.js` file to initialize the route and our request validator. So, open this file and replace the code with this:

```js
'use strict';

require('dotenv').config();
const config = require('./app/configs/configs')();
const restify = require('restify');
const versioning = require('restify-url-semver');
const joi = require('joi');

// Require DI
const serviceLocator = require('./app/configs/di');
const validator = require('./app/lib/validator');
const handler = require('./app/lib/error_handler');
const routes = require('./app/routes/routes');
const logger = serviceLocator.get('logger');
const server = restify.createServer({
  name: config.app.name,
  versions: ['1.0.0'],
  formatters: {
    'application/json': require('./app/lib/jsend')
  }
});

// Initialize the database
const Database = require('./app/configs/database');
new Database(config.mongo.port, config.mongo.host, config.mongo.name);

// Set API versioning and allow trailing slashes
server.pre(restify.pre.sanitizePath());
server.pre(versioning({ prefix: '/' }));

// Set request handling and parsing
server.use(restify.plugins.acceptParser(server.acceptable));
server.use(restify.plugins.queryParser());
server.use(
  restify.plugins.bodyParser({
    mapParams: false
  })
);

// initialize validator for all requests
server.use(validator.paramValidation(logger, joi));

// Setup Error Event Handling
handler.register(server);

// Setup route Handling
routes.register(server, serviceLocator);

// start server
server.listen(config.app.port, () => {
  console.log(`${config.app.name} Server is running on port - 
    ${config.app.port}`);
});
```

That's it!!! You have just built a well-organised API with Restify. Time take your baby for a spin:

```bash
# make sure you are running this from the project root
node server.js
```

After running your application, you can issue the following create a `User`:

```sh
curl -H "Content-Type: application/json" -X POST -d '{
  "username": "biodunch",
  "birthdate": "12/2/2000"
}' localhost:5000/v1/users
```

Then, you can register a birthdate of a friend of this user with this command:

```sh
curl -H "Content-Type: application/json" -X POST -d '{
  "fullname": "Falomo Olumide",
  "birthdate":"10/3/2000"
}' localhost:5000/v1/birthdates/biodunch
```

Then, to fetch the birthdates saved by `biodunch`, you can issue this command:

```sh
curl localhost:5000/v1/birthdates/biodunch
```

This will get you a response similar to:

```json
{
   "status":"success",
   "data":[
      {
         "_id":"5adbb5e59b09be4ea8da5903",
         "birthdate":"2000-10-02T23:00:00.000Z",
         "fullname":"Falomo Olumide"
      }
   ]
}
```

{% include asides/node.markdown %}

## Conclusion

In this article, you learned how to use great tools like Node.js, Mongoose, Restify, Joi, and Mongo to build well-organised RESTful APIs. You also learned that without too much effort you can integrate a simple module to handle the injection of dependencies on modules. Although lengthy, the content of this article is not hard to grasp and paves the way for more complex applications.

In a next opportunity, you will see how to integrate the project created here in a CI/CD (Continuous Integration and Continuous Delivery) workflow to automate the release process. Stay tuned!
