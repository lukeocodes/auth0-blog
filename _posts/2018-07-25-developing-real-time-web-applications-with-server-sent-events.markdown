---
layout: post
title: "Developing Real-Time Web Applications with Server-Sent Events"
description: "Learn how to create real-time web applications by using the Server-Sent Events specification."
date: 2018-07-25 08:30
category: Technical Guide, Backend
author:
  name: "Andrea Chiarelli"
  url: "https://twitter.com/andychiare"
  mail: "andrea.chiarelli.ac@gmail.com"
  avatar: "https://pbs.twimg.com/profile_images/827888770510880769/nnvUxzSd_400x400.jpg"
design:
  image: https://cdn.auth0.com/blog/server-sent-events/server-sent-events-standard-logo.png
  bg_color: "#222328"
tags:
- real-time
- backend
- node-js
- react
- server-sent-events
- sse
- javascript
related:
- 2018-06-12-real-time-charts-using-angular-d3-and-socket-io
- 2017-11-21-developing-real-time-apps-with-meteor
---

**TL;DR:** [Server-Sent Events (SSE)](https://html.spec.whatwg.org/multipage/server-sent-events.html) is a standard that enables Web servers to push data in real time to clients. In this article, we will learn how to use this standard by building a flight timetable demo application with React and Node.js. However, the concepts you will learn following this tutorial are applicable to any programming language and technology. [You can find the final code of the application in this GitHub repository](https://github.com/andychiare/server-sent-events).

{% include tweet_quote.html quote_text="Learn how to build real-time web applications with the Server-Sent Events protocol." %}

## Introducing on Server-Sent Events

The [typical interactions between browsers and servers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Session) consist of browsers requesting resources and servers providing responses. But, can we make our servers send data to clients at any time without explicit requests?

The answer is _yes_! We can achieve that by using [Server-Sent Events (which is also known as *SSE* or *Event Source*)](https://html.spec.whatwg.org/multipage/server-sent-events.html), a W3C standard that allows servers to push data to clients asynchronously. This may suggest using that annoying polling we'd implement to get the progress status from a long server processing but, thanks to SSE, we don't have to implement polling to wait for a response from the server. We don't even need any complex and strange protocol. That is, we can continue to use the standard HTTP protocol.

So, let's take a look at how to use Server-Sent Events in a realistic application.

## Building a Real-Time App with Server-Sent Events

In order to learn how to use Server-Sent Events, we are going to develop a simple flight timetable application (similar to those flight trackers you can find at any airport). The timetable app will consist of a simple web page showing a list of flights as shown in the following picture:

![Real-time flights tracker timetable](https://cdn.auth0.com/blog/server-sent-events/developing-real-time-web-applications.png)

Through this real-time app, we can find the flight arrival timetable and, after implementing Server-Sent Events, we will see automatically updates when the state of flights change. In our demo application, we are going to simulate the flight state changes using scheduled events. However, one can easily replace this mechanism with more realistic ones on production-ready applications.

So, let's start coding and discover how the Server-Sent Events standard works.

### Scaffolding a React Application

As the first step, let's scaffold our React client application. To make things simple, we will use [`create-react-app`](https://github.com/facebook/create-react-app) to set up our React-based client. So, [let's make sure that we have Node.js installed](https://nodejs.org/en/download/) on our machine and let's type the following command in a terminal window:

```bash
# installing create-react-app globally
npm install -g create-react-app
```

After installing `create-react-app` in our development machine, let's create the basic template of a React application by typing:

```bash
create-react-app real-time-sse-app
```

After a few seconds, we will get a new directory called `real-time-sse-app` with all the files we need inside it. In particular, the `src` subdirectory contains the source code of our brand-new React application. Our goal is to change the code of this basic React application and replace it with our flight timetable frontend application.

Since our application will show data in a tabular form, we will install a React component that simplifies the task of rendering data into tables: [_React Table_](https://react-table.js.org). To add this component to our application, we will type the following commands in our terminal:

```bash
# make sure we are on the React app project root
cd real-time-sse-app

# then install the React Table dependency
npm install react-table
```

Now, we are ready to start changing the application code. So, let's open the `App.js` file (which resides inside the `src` directory) and let's replace its content with the following one:

```javascript
// src/App.js

import React, { Component } from 'react';
import ReactTable from 'react-table';
import 'react-table/react-table.css';
import { getInitialFlightData } from './DataProvider';

class App extends Component {
  constructor(props) {
    super(props);
    this.state = {
      data: getInitialFlightData()
    };

    this.columns = [{
      Header: 'Origin',
      accessor: 'origin'
    }, {
      Header: 'Flight',
      accessor: 'flight'
    }, {
      Header: 'Arrival',
      accessor: 'arrival'
    }, {
      Header: 'State',
      accessor: 'state'
    }];
  }

  render() {
    return (
      <div className="App">
        <ReactTable
          data={this.state.data}
          columns={this.columns}
        />
      </div>
    );
  }
}

export default App;
```

Here, we are importing the stuff we need to set up the flight timetable. In particular, in addition to standard React basic elements, we are importing the `ReactTable` component with its basic stylesheets and a method called `getInitialFlightData` from a module called `DataProvider`. We haven't defined this module yet but we will, quite soon. The goal of this function will be to provide our application with flight data that is used to initialize the `App` component's `state`.

In the constructor, we also define the table's structure by mapping flight properties to table columns. As we can see, this mapping consists of an array of objects like the following:

```javascript
this.columns = [{
    Header: 'Origin',
    accessor: 'origin'
  }, {
    Header: 'Flight',
    accessor: 'flight'
  }, {
    Header: 'Arrival',
    accessor: 'arrival'
  }, {
    Header: 'State',
    accessor: 'state'
  }];
```

As defined above, each flight object contains a `Header` property (representing the column's header) and an `accessor` property (representing the flight property whose value will be shown in that column). _React Table_ has many other options to define the table's structure, but `Header` and `accessor` are sufficient for our goal.

Finally, within the `render` method of `App` component, we include the `ReactTable` element and pass to it the flight data and the columns as props.

Now, within the `src` folder, let's create a file named `DataProvider.js` and populate it with the following code:

```javascript
// src/DataProvider.js

export function getInitialFlightData() {
  return [{
    origin: 'London',
    flight: 'A123',
    arrival: '08:15',
    state: ''
  }, {
    origin: 'Berlin',
    flight: 'D654',
    arrival: '08:45',
    state: ''
  }, {
    origin: 'New York',
    flight: 'U213',
    arrival: '09:05',
    state: ''
  }, {
    origin: 'Buenos Aires',
    flight: 'A987',
    arrival: '09:30',
    state: ''
  }, {
    origin: 'Rome',
    flight: 'I768',
    arrival: '10:10',
    state: ''
  }, {
    origin: 'Tokyo',
    flight: 'G119',
    arrival: '10:35',
    state: ''
  }];
}
```

The module above exports the `getInitialFlightData` function that simply returns a static array of flight data. In a real application, the function would request data from a server. However, for the purpose of this article, the current implementation is enough. 

So far, our application presents the flight data in tabular form. Let's check it out in a web browser! To run the app, type the following command in the terminal:

```bash
# from the real-time-sse-app, start the React app
npm start
```

After a few seconds, we should see in the browser the list of flights as shown in the picture above. If the browser doesn't open automatically, please open it and go to [`http://localhost:3000`](http://localhost:3000).

> **Note:** For some unknown reason, the application fails to start, issuing `npm i` from the `real-time-sse-app` directory might solve the problem.

### Consuming Server-Sent Events with React

After making our React application present some static data, let's add the functionality it needs to consume server-side generated events.

To do this, we are going to use the [`EventSource` API](https://developer.mozilla.org/en-US/docs/Web/API/EventSource), a standard interface created to interact with the Server-Sent Events protocol. As MDN documentation says, "_an `EventSource` instance opens a persistent connection to an HTTP server, which sends events in `text/event-stream` format. The connection remains open until closed by calling `EventSource.close()`_"

So, as the first step, let's add the `eventSource` property to the `App` component and assign an `EventSource` instance to it. The code below shows how we achieve that:

```javascript
// src/App.js

// ... import statements ...

class App extends Component {
  constructor(props) {
    // ... super, state, and columns ...

    this.eventSource = new EventSource('events');
  }

  // ... render ...
}

export default App;
```

The `EventSource()` constructor creates an object that initializes a communication channel between the client and the server. This channel is unidirectional, meaning that events flow from the server to the client, never the opposite way. To create a new instance, we pass it as argument an URL that represents an endpoint from where we want to receive events. Since we are using HTTP, the URL can be any valid relative or absolute web address, including any possible query strings.

In our case, the client expects a stream of events from the `events` endpoint within the same domain of the client. That is, if the client URL is, as in our example, `http://localhost:3000` then the server endpoint pushing data will be at `http://localhost:3000/events`.

Now, let's capture events sent by the server by adding a few lines of code. We are going to add a new method called `updateFlightState` to our `App` component. This method will be called whenever we receive a message from the server and will update the component state with the message data. The following code snippet shows what changes we need to make in our `App` component to handle server-sent events:

```javascript
// src/App.js

// ... import statements ...

class App extends Component {
  // ... constructor ...

  componentDidMount() {
    this.eventSource.onmessage = (e) => this.updateFlightState(JSON.parse(e.data));
  }
  
  updateFlightState(flightState) {
    let newData = this.state.data.map((item) => {
      if (item.flight === flightState.flight) {
        item.state = flightState.state;
      }
      return item;
    });

    this.setState(Object.assign({}, {data: newData}));
  }

  // ... render ...
}

export default App;
```

As we can see, we added an event handler to the `onmessage` property of the `eventSource` object in the `componentDidMount()` method. The `onmessage` property points to an event handler that will be called when an event comes from the server. In our case, the assigned event handler calls the `updateFlightState()` method to update the component `state` with the data sent by the server. Each event carries data in the `e.data` property represented as a string. For our application, the data will be a JSON string that represents updated flight data, as we will see in the next section.

Although we can't test the application yet (we still have to create our backend and send events from it), our React application is now ready to handle server-sent events. Not hard, huh?

## Building Real-Time Backends with Server-Sent Events

The server-side of our application will be simple Node.js web server that responds to requests submitted to the `events` endpoint. To implement it, let's create a new directory called `real-time-sse-backend` at the same level of the `real-time-sse-app` directory. Within the `server` directory, let's create a new file called `server.js` and put the following code inside it:

```javascript
// server.js

const http = require('http');

http.createServer((request, response) => {
  console.log('Requested url: ' + request.url);

  if (request.url.toLowerCase() === '/events') {
    response.writeHead(200, {
      'Connection': 'keep-alive',
      'Content-Type': 'text/event-stream',
      'Cache-Control': 'no-cache'
    });

    setTimeout(() => {
      response.write(
        'data: {"flight": "I768", "state": "landing"}'
      );
      response.write('\n\n');
    }, 3000);

    setTimeout(() => {
      response.write(
        'data: {"flight": "I768", "state": "landed"}'
      );
      response.write('\n\n');
    }, 6000);

  } else {
    response.writeHead(404);
    response.end();
  }
}).listen(5000, () => {
  console.log('Server running at http://127.0.0.1:5000/');
});
```

At the beginning of the file, we import the `http` module and we use its `createServer` method to run a web server whose behavior is described by the callback function passed as an argument. The callback function verifies that the requested URL is `/events` and, only in this case, initiates a response by sending a few HTTP headers. The headers sent by the server are very important in order to establish a live communication channel with the client.

In fact, the `keep-alive` value for the `Connection` header informs the client that this is a permanent connection. With that, the client knows that this is a connection that doesn't end with the first bunch of data received.

The `text/event-stream` value for the `Content-Type` header determines the way the client should interpret the data that it will receive. In practice, this value informs the client that this connection uses the Server-Sent Events protocol.

Finally, the `Cache-Control` header asks the client not to store data into its local cache, so that data read by the client is really sent by the server and not some old, out-of-date data received in the past.

After sending these headers, the client using the `EventSource()` constructor will wait for events reporting newly available data. The rest of the function body schedules the execution of a few functions in order to simulate the change of a flight state. At each scheduled function execution, a string in the following form is sent to the client:

```bash
data: xxxxxxx
```

The `xxxxxxx` represents the data to be sent to the client. In our case, we send a JSON string representing a flight. We can send multiple data lines in an event response but the response must be closed by a double empty line. In other words, our event message could have the following schema:

```bash
data: This is a message\n
data: A long message\n
\n
\n
```

In order to execute the web server we created so far, let's type the following commands on the terminal:

```bash
# make sure we are in the real-time-sse-backend directory
cd real-time-sse-backend

# run the server
node server.js
```

## Consuming Server-Sent Events

After building both our React client and our Node.js server, we want to make them communicate with each other. However, they run on different domains. Currently, the React client is running on `http://localhost:3000` and the Node.js server is running on `http://localhost:5000`.

This means that, if we don't do anything, our React client won't be able to issue HTTP requests to the Node.js server due to the [same-origin policy](https://en.wikipedia.org/wiki/Same-origin_policy) applied by the browser itself. 

We could get around the problem by simply adding a `proxy` value in the `package.json` file, as mentioned in the [`create-react-app` documentation](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#proxying-api-requests-in-development). However, due to a [known issue](https://github.com/facebook/create-react-app/issues/3391), this workaround is not applicable currently.

So, in order to enable communication between our client app and our backend server, we need to make our server support [CORS (Cross-origin resource sharing)](https://auth0.com/docs/cross-origin-authentication). With this approach, the server authorizes a client published on a different domain to request its resources. To enable CORS in our Node.js server, we can simply add a new header to be sent to the client: the `Access-Control-Allow-Origin` header. After making this change, the call to `response.writeHead` will look like this:

```javascript
response.writeHead(200, {
  'Connection': 'keep-alive',
  'Content-Type': 'text/event-stream',
  'Cache-Control': 'no-cache',
  'Access-Control-Allow-Origin': '*'
});
```

The asterisk assigned to the `Access-Control-Allow-Origin` header indicates that any client (from any domain) is authorized to access this URL. It may not be the desired solution in a production environment. In fact, in a production environment, we should adopt a different approach, such as put the two applications under the same domain (by using a reverse proxy), or by enabling [CORS](https://auth0.com/docs/cross-origin-authentication) more selectively (i.e. authorizing only specific domains).

Once we have enabled CORS on the Node.js server, we should change the URL passed to the `EventSource()` constructor. So, let's open the `./src/App.js` file on our React application and replace the line that defines `this.eventSource` with this:

```javascript
this.eventSource = new EventSource('http://localhost:5000/events');
```

Bingo! If we run our backend server again and then launch the client application, after a few seconds, we will see our browser showing our real-time application.

![Locally running the real-time flight tracker web application.](https://cdn.auth0.com/blog/server-sent-events/developing-real-time-web-applications-with-react-and-node.png)

As a recap, this is how we run our projects:

```bash
# from the backend directory
cd real-time-sse-backend

# start the Node.js server
node server.js

# and from the real-time-sse-app directory
cd real-time-sse-app

# start the React application
npm start
```

> **Note:** It might be easier to run both applications from different terminal sessions. Otherwise, we might have to run the backend server in the background by issuing `node server.js &`.

## Managing Event Types with Server-Sent Events

The timetable application that we have developed so far responds to server events always in the same way: by updating a specific flight sent in the event's `data` property. This is awesome but, how could we manage a different situation?

Suppose, for example, that we want to remove the row that describes a flight after a certain amount of time that this flight landed. How could the server send an event that is not a state change? How can the client capture an event saying that it should remove a row from the table?

We could think of using the `data` property of the event to specify an event information that distinguishes different event types. However, the Server-Sent Events protocol allows us to specify an event so that we can handle different type of events in an easy way. We are talking about the `event` property on the server-sent event. Let's take a look at how our server's code changes:

```javascript
// server.js

const http = require('http');

http.createServer((request, response) => {
  console.log('Requested url: ' + request.url);

  if (request.url.toLowerCase() === '/events') {
    response.writeHead(200, {
      'Connection': 'keep-alive',
      'Content-Type': 'text/event-stream',
      'Cache-Control': 'no-cache',
      'Access-Control-Allow-Origin': '*'
    });

    setTimeout(() => {
      response.write(
        'event: flightStateUpdate\n'
      );
      response.write(
        'data: {"flight": "I768", "state": "landing"}'
      );
      response.write('\n\n');
    }, 3000);

    setTimeout(() => {
      response.write(
        'event: flightStateUpdate\n'
      );
      response.write(
        'data: {"flight": "I768", "state": "landed"}'
      );
      response.write('\n\n');
    }, 6000);

    setTimeout(() => {
      response.write(
        'event: flightRemoval\n'
      );
      response.write(
        'data: {"flight": "I768"}'
      );
      response.write('\n\n');
    }, 9000);

  } else {
    response.writeHead(404);
    response.end();
  }
}).listen(5000, () => {
  console.log('Server running at http://127.0.0.1:5000/');
});
```

While defining the response, we added a new `event` property before the `data` property. This `event` property helps us to specify the type of event we are sending to clients. In the example shown above, we set `filghtStateUpdate` as the value for the previously existing events and added a new event with the `flightRemoval` value for the `event` property.

As such, we are saying to our client that some events must update flights' state (e.g. from `landing` to `landed`) and others must remove flights.

After refactoring our backend server, we will need to update our client application to handle these different types of event. So, let's open the `src/App.js` on our React app and update it as follows:

```javascript
// src/App.js

// ... import statements ...

class App extends Component {
  // ... constructor ... 

  componentDidMount() {
    this.eventSource.addEventListener('flightStateUpdate', 
      (e) => this.updateFlightState(JSON.parse(e.data)));
    this.eventSource.addEventListener('flightRemoval',
      (e) => this.removeFlight(JSON.parse(e.data)));
  }

  removeFlight(flightInfo) {
    const newData = this.state.data.filter((item) => item.flight !== flightInfo.flight);

    this.setState(Object.assign({}, {data: newData}));
  }

  // ... render, updateFlightState ...
}

export default App;
```

As we can see, the body of the `componentDidMount()` method has no longer the assignment of the event handler to the `onmessage` property. Now, we are using the `addEventListener()` method to assign an event handler to a specific event. In this way, we are able to easily assign a specific event handler to each event generated by the server like it was generated by any standard HTML element.

In the example, we assigned the `updateFlightState()` method to the `flightStateUpdate` event and the `removeFlight()` method to the `flightRemoval` event.

If we run both our projects again and open our React application on a browser ([`http://localhost:3000/`](http://localhost:3000/)), after 9 seconds (as defined in the last `setTimeout` on our `server.js` file), the flight to Rome will be removed.

## Handling Connection Closure on Server-Sent Events

The Server-Sent Event connection between the client and the server is a streaming connection. This means that the connection will be kept active indefinitely unless the client or the server stops running. If our server has no more events to send or the client isn't longer interested in server's events, how can we explicitly stop the currently active connection?

Let's consider the case where the client wants to stop the event stream. To learn about this, let's create a button that allows the user to stop receiving new events. So, let's change the `render()` method of the `App` class, as shown below:

```javascript
render() {
  return (
    <div className="App">
      <button onClick={() => this.stopUpdates()}>Stop updates</button>
      <ReactTable
        data={this.state.data}
        columns={this.columns}
      />
    </div>
  );
}
```

Here we added a `<button>` element and bound the click event to the `stopUpdates()` method of the same component. This method will look like the following:

```javascript
stopUpdates() {
  this.eventSource.close();
}
```

In other words, to stop the event stream, we simply invoked the `close()` method of the `eventSource` object.

Closing the event stream on the client doesn't automatically closes the connection on the server side. This means that the server will continue to send events to the client. To avoid this, we need to handle the connection close request on the server side. We can do it by adding an event handler for the `close` event on the server side, as shown by the following code:

```javascript
// server.js

const http = require('http');

http.createServer((request, response) => {
  console.log(`Request url: ${request.url}`);

  request.on('close', () => {
    if (!response.finished) {
      response.end();
      console.log('Stopped sending events.');
    }
  });

  // ... etc ...
}).listen(5000, () => {
  console.log('Server running at http://127.0.0.1:5000/');
});
```

The `request.on()` method catches the `close` request and executes the callback function. This function invokes the `response.end()` method to close the HTTP connection. Also, it checks if the connection is already closed, which is a situation that may occur when multiple closing requests are sent by the client.

Since our server event generators are scheduled by using `setTimeout()`, it could happen that an attempt to send an event to the client may be made after the connection has been closed, which would raise an exception. We can avoid this by checking if the connection is still active, as shown in the following example:

```javascript
setTimeout(() => {
  if (!response.finished) {
  response.write(
      'event: flightStateUpdate\n'
    );
    response.write(
      'data: {"flight": "I768", "state": "landing"}'
    );
    response.write('\n\n');
  }
}, 3000);	
```

> **Note:** We have to implement the same strategy for the other timeout events.

Now, when we need to close the connection from the server, we will need to invoke the `response.end()` method, as seen before. Also, the client should be informed about the closure, so that it can free resources on its side.

To achieve that, an easy strategy to follow is to create a specific type of event that informs the client that they have to close the connection. For example, our server could simply generate a `closedConnection` event as follows:

```javascript
setTimeout(() => {
  if (!response.finished) {
  response.write(
      'event: closedConnection\n'
    );
    response.write(
      'data: '
    );
    response.write('\n\n');
  }
}, 3000);
```

Then, to handle this event on our React application, we could update our `src/App.js` as follows:

```javascript
// src/App.js

// ... import statements ...

class App extends Component {
  // ... constructor ...

  componentDidMount() {
    // ... flightStateUpdate and flightRemoval event handlers ...
    this.eventSource.addEventListener('closedConnection',
      (e) => this.stopUpdates());
  }

  // ... updateFlightState and removeFlight methods ...

  stopUpdates() {
    this.eventSource.close();
  }

  // ... render ...
}

export default App;
```

As we can see, handling this new event type is as easy as assigning the `stopUpdates()` method to the `closedConnection` event.

## Handling Connection Recovery on Server-Sent Events

So far, we built a real-time application based on Server-Sent Events that is quite complete. We are able to get different types of events pushed by the server and to control the end of the event stream. But, what happens if the client loses some event due to network issues? Of course, it depends on the specific application. In some situations, we may ignore the loss of some event, in some others we can't.

Let's consider, for example, the event stream we implemented. If a network issue happens and the client loses the `flightStateUpdate` event that puts the flight into the landing state, it could not be a big problem. The user would just miss the landing phase on the timetable but, when the connection gets restored, the timetable would provide the correct information with the subsequent states.

However, if the network issue happens immediately after the flight enters in the landing state and the connection is restored after the `flightRemoval` event, we have an issue: the flight will remain in the landing state forever and we need to handle this awkward situation.

The Server-Sent Events protocol help us by providing a mechanism to identify events and to restore a dropped connection. Let's learn about it.

When the server generates an event, we have the ability to assign an identifier by attaching an `id` keyword to the response to be sent to the client. For example, we could send the `flightStateUpdate` event as shown by the following code:

```javascript
setTimeout(() => {
  if (!response.finished) {
    const eventString = 'id: 1\nevent: flightStateUpdate\ndata: {"flight": "I768", "state": "landing"}\n\n';
    response.write(eventString);
  }
}, 3000);
```

Here, we are making a little refactoring to our code to add the `id` keyword with `1` as its value.

When a network issue happens and the connection to an event stream is lost, the browser will automatically attempt to restore the connection. When the connection is established again, the browser will automatically send the identifier of the last received event in the `Last-Event-Id` HTTP header. So, the server should be changed to handle this request to restore the event stream properly. It is up to the server to decide if it should send all missed events or if it should continue with newly generated events. Anyway, if the server needs to send all missed events, it also needs to store all events already sent to the client.

Let's implement this strategy in our server. With a bit of refactoring, the following is the final version of the server side code:

```javascript
// server.js

const http = require('http');

http.createServer((request, response) => {
  console.log(`Request url: ${request.url}`);

  const eventHistory = [];

  request.on('close', () => {
    if (!response.finished) {
      response.end();
      console.log('Stopped sending events.');
    }
  });

  if (request.url.toLowerCase() === '/events') {
    response.writeHead(200, {
      'Connection': 'keep-alive',
      'Content-Type': 'text/event-stream',
      'Cache-Control': 'no-cache',
      'Access-Control-Allow-Origin': '*'
    });

    checkConnectionToRestore(request, response, eventHistory);

    sendEvents(response, eventHistory);
  } else {
    response.writeHead(404);
    response.end();
  }
}).listen(5000, () => {
  console.log('Server running at http://127.0.0.1:5000/');
});

function sendEvents(response, eventHistory) {
  setTimeout(() => {
    if (!response.finished) {
      const eventString = 'id: 1\nevent: flightStateUpdate\ndata: {"flight": "I768", "state": "landing"}\n\n';
      response.write(eventString);
      eventHistory.push(eventString);
    }
  }, 3000);

  setTimeout(() => {
    if (!response.finished) {
      const eventString = 'id: 2\nevent: flightStateUpdate\ndata: {"flight": "I768", "state": "landed"}\n\n';
      response.write(eventString);
      eventHistory.push(eventString);
    }
  }, 6000);

  setTimeout(() => {
    if (!response.finished) {
      const eventString = 'id: 3\nevent: flightRemoval\ndata: {"flight": "I768"}\n\n';
      response.write(eventString);
      eventHistory.push(eventString);
    }
  }, 9000);

  setTimeout(() => {
    if (!response.finished) {
      const eventString = 'id: 4\nevent: closedConnection\ndata: \n\n';
      eventHistory.push(eventString);
    }
  }, 12000);
}

function checkConnectionToRestore(request, response, eventHistory) {
  if (request.headers['last-event-id']) {
    const eventId = parseInt(request.headers['last-event-id']);

    const eventsToReSend = eventHistory.filter((e) => e.id > eventId);

    eventsToReSend.forEach((e) => {
      if (!response.finished) {
        response.write(e);
      }
    });
  }
}
```

In the new version of our backend, we introduced an array called `eventHistory` to store the events sent to the client. Then, we assigned to the `checkConnectionToRestore()` function the task of checking and restoring possible broken connections and we encapsulated the code for event generation in the `sendEvents()` function.

Now, we can see that each time an event is sent to the client, it is also stored in the `eventHistory`. For example, this is the code that handles the first event:

```javascript
setTimeout(() => {
  if (!response.finished) {
    const eventString = 'id: 1\nevent: flightStateUpdate\ndata: {"flight": "I768", "state": "landing"}\n\n';
    response.write(eventString);
    eventHistory.push(eventString);
  }
}, 3000);
```

If the `checkConnectionToRestore()` function finds the `Last-Event-Id` HTTP header in the request, it filters the already sent events in the `eventHistory` array and sends them again to the client:

```javascript
function checkConnectionToRestore(request, response, eventHistory) {
  if (request.headers['last-event-id']) {
    const eventId = parseInt(request.headers['last-event-id']);

    const eventsToReSend = eventHistory.filter((e) => e.id > eventId);

    eventsToReSend.forEach((e) => {
      if (!response.finished) {
        response.write(e);
      }
    });
  }
```

With these changes, we make our backend more robust and more resilient.

{% include tweet_quote.html quote_text="Updating the state of frontend applications with Server-Sent Events is quite easy." %}

## Browser Support for Server-Sent Events

According to [`caniuse.com`](https://caniuse.com/#search=server%20sent%20events), Server-Sent Events are currently supported by all major browsers but Internet Explorer, Edge, and Opera Mini. Although [supporting them in Edge is under consideration](https://developer.microsoft.com/en-us/microsoft-edge/platform/status/serversenteventseventsource/), the lack of universal support forces us to use polyfills, such as [Remy Sharp's EventSource.js](https://github.com/remy/polyfills/blob/master/EventSource.js), [Yaffle's EventSource](https://github.com/Yaffle/EventSource), or [AmvTek's EventSource](https://github.com/amvtek/EventSource).

Using these polyfills is very simple. In this section, we will see how to use AmvTek's polyfill. The process of using the other ones is not so different. In order to add AmvTek's EventSource polyfill to our React client application, we need to install it via `npm`, as shown below: 

```bash
npm install eventsource-polyfill
```

Then, we should import the module in the `App` component's module:

```react
// src/App.js

// ... other import statements ...
import 'eventsource-polyfill';

// ... App class definition and export ...
```

And that's all. The polyfill will define an `EventSource` constructor only if it is not natively supported and our code will continue to work as before also on browsers that don't support it.

## Server-Sent Events vs WebSockets

Using the Server-Sent Events protocol helps us to solve some common issues that involve waiting for data from the server. So, instead of implementing a long polling, whose main drawback is consuming resources both on the client and on the server side, we get a simple and performant solution.

An alternative approach is to use [WebSockets, a standard TCP based protocol providing full duplex communication between the client and the server](https://www.w3.org/TR/websockets/). What benefits does WebSockets bring with respect to Server-Sent Events? When to use one technology instead of the other?

The following are some considerations to keep in mind when choosing between Server-Sent Events and WebSockets:

- WebSockets support a bidirectional communication, while Server-Sent Events support only communication from the server to the client.
- WebSockets is a low-level protocol, while Server-Sent Events is based on HTTP and so it doesn't require additional settings in the network infrastructure.
- WebSockets supports binary data transfer, while Server-Sent Events supports only text-based data transfer. That is, if we want to transfer binary data via Server-Sent Events we need to encode it in Base64.
- The combination of low-level protocol and support of binary data transfer makes WebSockets more suitable than Server-Sent Events for applications requiring real-time binary data transfer as may happen in gaming or other similar application types.

{% include asides/javascript-at-auth0.markdown %}

## Summary

In this article, we used Server-Sent Events to develop a real-time application that simulates a flight timetable. During the development, we had the opportunity to explore the features that the standard provides to support event typing, connection control, and restoring. We also learned how to add support Server-Sent Events on browsers that do not support it by default. Lastly, we took a look at a brief comparison between Server-Sent Events and WebSockets.

If needed, you can [check and download the final code of the project developed throughout this article from this GitHub repository](https://github.com/andychiare/server-sent-events).
