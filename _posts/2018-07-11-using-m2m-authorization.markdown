---
layout: post
title: "Using Machine to Machine (M2M) Authorization"
description: "How to set up non-interactive apps using the client credentials grant and Auth0 to perform IoT device, CLI tool, and more machine to machine API authorization."
date: 2018-07-11 08:30
category: Technical Guide, Identity
author:
  name: Sebastián Peyrott
  url: https://twitter.com/speyrott?lang=en
  mail: speyrott@auth0.com
  avatar: https://en.gravatar.com/userimage/92476393/001c9ddc5ceb9829b6aaf24f5d28502a.png?size=200
design:
  image: https://cdn.auth0.com/blog/m2m/machine-to-machine-authorization-logo.png
  bg_color: "#222228"
tags:
- auth
- authentication
- authorization
- m2m
- machine
- machine-to-machine
- machine-2-machine
- api
- non-interactive
- cli
- service
- daemon
- backend
related:
- 2018-02-07-oauth2-the-complete-guide
- 2018-05-23-introducing-the-mfa-api
---

Many times, a secure, authorized communication channel between different parts of an autonomous system is required. Think of two backend services from different companies communicating through the internet. For these cases, OAuth 2.0 provides the [client credentials grant](https://tools.ietf.org/html/rfc6749#section-4.4) flow. In this post, we will take a look at how the client credentials grant from [OAuth 2.0](https://tools.ietf.org/html/rfc6749) can be used with Auth0 for [machine to machine (M2M) communications](https://auth0.com/machine-to-machine).

{% include tweet_quote.html quote_text="Learn to use machine to machine authorization with Auth0!" %}

[Get the code](https://github.com/auth0-samples/auth0-api-auth-samples) for the sample in this post.

---

## Machine to Machine Communications
There are many parts of a system where machine to machine communications make sense: service to service, daemon to backend, CLI client to internal service, [IoT](https://auth0.com/blog/javascript-for-microcontrollers-and-iot-part-1/) tools. The key aspect of these communications lies on the fact that the element to establish trust in the system is the _client_. What does this mean? In contrast to usual systems where an authorization process attempts to establish trust by authorizing _a user_, in this case what must be authorized and trusted is the client. In other words, there is no need for a user to interact with the system to authenticate, rather the system must authenticate and authorize the _client_. To be clear, in this case, the client is simply an application, process or even an autonomous system. For these scenarios, typical authentication schemes like username + password, social logins, etc. don't make sense.

The [client credentials grant] from OAuth 2.0 attempts to fulfill the need for these scenarios. In the client credentials grant, the client holds two pieces of information: the client ID and the client secret. With this information, the client can request an access token for a protected resource.

![Client Credentials Grant](https://cdn.auth0.com/docs/media/articles/api-auth/client-credentials-grant.png)

1. The client makes a request to the authorization server sending the `client ID`, the `client secret`, along with the `audience` and other claims claims.
2. The authorization server validates the request, and, if successful, sends a response with an access token.
3. The client can now use the access token to request the protected resource from the resource server.

Since the client must always hold the client secret, this grant is only meant to be used in _trusted_ clients. In other words, clients that hold the _client secret_ must always be used in places where there is no risk of that secret being misused. For example, while it may be a good idea to use the client credentials grant in an internal daemon that sends reports across the web to a different part of your system, it cannot be used for a public tool that any external user can download from GitHub.

## Client Credentials Grant
The client credentials grant is very simple to use. The following are the relevant HTTP requests:

```
POST https://<YOUR_AUTH0_DOMAIN>/oauth/token
Content-Type: application/json
{
  "audience": "<API_IDENTIFIER>",
  "grant_type": "client_credentials",
  "client_id": "<YOUR_CLIENT_ID>",
  "client_secret": "<YOUR_CLIENT_SECRET>"
}
```

A successful authorization request results in a response like the following:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJz93a...k4laUWw",
  "token_type": "Bearer",
  "expires_in": 86400
}
```

In Auth0, to use the client credentials grant, you can [create a new "machine to machine" application from the dashboard:](https://auth0.com/docs/applications/machine-to-machine)

![Machine to Machine in the Auth0 Dashboard](https://cdn.auth0.com/blog/m2m/2-create.png)

A machine to machine application requires the selection of at least one API. This is the API that gets selected through the `audience` claim in the HTTP request above.

![Select API in the Auth0 Dashboard](https://cdn.auth0.com/blog/m2m/3-select-api.png)

You can create audiences for your APIs and protected resources in the [API](https://manage.auth0.com/#/apis) section of the Auth0 Dashboard. You can enable or disable these APIs in the `APIs` tab of your application.

### Granular Permissions in Auth0
Thanks to the use of [rules in Auth0](https://auth0.com/docs/rules/current), it is very easy to have granular permissions for [machine to machine communications](https://auth0.com/machine-to-machine). When a client uses the client credentials grant, a rule can be run to check for any data in the request, including `scopes` or `roles`. With this information, you can choose to either grant or deny the request.

{% include tweet_quote.html quote_text="With Auth0 Rules, you can have powerful granular permissions even for machine to machine authorization!" %}

Rules are little pieces of code that are run every time a request is made. They are very flexible and powerful. This is how they look:

```javascript
function (user, context, callback) {
  if (context.clientID === "BANNED_CLIENT_ID") {
    return callback(new UnauthorizedError('Access to this application has been temporarily revoked'));
  }

  callback(null, user, context);
}
```

In this rule, access to a specific client ID has been denied. Since rules are JavaScript code, any complex logic for permissions or authorization can be added to your apps.

If you don't need the flexibility of rules, you can still achieve a level of granularity with scopes. These can be enabled on a per API and per application/client basis.

## Common Use Cases for M2M Communications
In this section, we will take a look at common scenarios where M2M communications might make sense.

### Backend-to-Backend (Services/Daemons)
This is one of the most common scenarios. Let's say you have different internal services that produce data in the form of logs. You are storing those logs locally in your own data warehouse. However, as part of your policies, you also want those logs to be stored offsite in a cold storage solution. You send those logs over the internet to the cold storage service in a different cloud provider. To authorize log storage from different services in your network, you use the client credentials grant, giving each client a client ID and a client secret.

![Machine to Machine Logging](https://cdn.auth0.com/blog/m2m/3-backend-to-backend.png)

What about other common services like job schedulers, daemons, continuous processes? These are also great scenarios where machine to machine authorization makes sense. Say you have a job scheduler that lives in your network but on a different cloud provider that offers a great deal for GPU processing. You only want your authorized clients to make use of the service. For this reason, you use the client credentials grant and only authorize those client IDs that you want to allow.

### Internet of Things Devices
The rise of [Internet of Things devices](https://auth0.com/blog/javascript-for-microcontrollers-and-iot-part-1/) makes a great case for machine to machine solutions. Many (if not most) of IoT devices are small, autonomous, specialized devices that collect and send data to a server. Let's say you have a fleet of small IoT devices that you use to collect data on the number of cars that are parked in your parking lot. You have many devices that are continuously reporting data to a central server. These devices communicate over WiFi. To avoid intrusions, and even though your WiFi network is password protected, you use the client credentials grant, giving each IoT device its own client ID and client secret. These devices are trusted: you set them up and no-one else can interact with them.

### CLI clients
When systems become big, automation starts to make more and more sense. Many tasks that are repetitive, but that still require an administrator to supervise them, start to become scripts. These scripts are run when administrators need to run them. Since these scripts interact with sensitive parts of a company's architecture, they usually have some sort of protection. In these cases, administrators may choose to create CLI apps that have the necessary rights to perform the actions, but that are only available in certain computers, like their own, or in mainframes. In these cases, the client credentials grant also makes sense. These CLI apps are trusted, but they also need access to sensitive parts of a system. Protecting access to sensitive parts of the system by requiring the client ID and client secret can be a solution.

## Code Example: Gift Deliveries App
If you are interested in seeing code that can apply to a real-world scenario, we have a sample app just for that purpose. The [Gift Deliveries app](https://github.com/auth0-samples/auth0-api-auth-samples/tree/master/machine-to-machine) shows how two backend services from the same company can interact securely with the use of the client credentials grant.

In this app, one service, the [World Mappers API](https://github.com/auth0-samples/auth0-api-auth-samples/tree/master/machine-to-machine/worldmappers-api-nodejs) exposes an API that can be used to find coordinates, addresses, and directions; while another service, the [Gift Deliveries Service](https://github.com/auth0-samples/auth0-api-auth-samples/tree/master/machine-to-machine/giftdeliveries-nodejs), uses the `World Mappers API` to get directions from one point to the destination address (to send a gift).

![Gift Deliveries App](https://cdn.auth0.com/blog/m2m/4-gift-deliveries-app.png)

Both of these services run in the internal network of an online shop, therefore, the `Gift Deliveries` client is a trusted client, which allows it to use the client credentials grant.

If you take a look at the `giftdeliveries-nodejs/index.js` file you will see the code that gets an access token using the client credentials grant:

```javascript
var getAccessToken = function(callback) {
  if (!env('AUTH0_DOMAIN')) {
    callback(new Error('The AUTH0_DOMAIN is required in order to get an access token (verify your configuration).'));
  }

  logger.debug('Fetching access token from https://' + env('AUTH0_DOMAIN') + '/oauth/token');

  var options = {
    method: 'POST',
    url: 'https://' + env('AUTH0_DOMAIN') + '/oauth/token',
    headers: {
      'cache-control': 'no-cache',
      'content-type': 'application/json'
    },
    body: {
      audience: env('RESOURCE_SERVER'),
      grant_type: 'client_credentials',
      client_id: env('AUTH0_CLIENT_ID'),
      client_secret: env('AUTH0_CLIENT_SECRET')
    },
    json: true
  };

  request(options, function(err, res, body) {
    if (err || res.statusCode < 200 || res.statusCode >= 300) {
      return callback(res && res.body || err);
    }

    callback(null, body.access_token);
  });
}
```

If you want to run this app, <a href="https://auth0.com/signup" data-amp-replace="CLIENT_ID" data-amp-addparams="anonId=CLIENT_ID(cid-scope-cookie-fallback-name)">sign up for a free Auth0 account</a>, clone the [repository](https://github.com/auth0-samples/auth0-api-auth-samples) and follow the [README](https://github.com/auth0-samples/auth0-api-auth-samples/blob/master/machine-to-machine/README.md).

## Conclusion
[Machine to machine communications](https://auth0.com/machine-to-machine) are part of almost any modern architecture. OAuth 2.0 and Auth0 provide the necessary building blocks to make its use in your architectures a breeze. From backend-to-backend, services, daemons, IoT devices, and even CLI tools, the client credentials grant remains a simple yet useful approach to the problem of authorization between autonomous and semi-autonomous system. This grant, augmented with the power of rules, can cover all common use cases while remaining flexible for more granular security needs in the future. If you are using, or interested in using, machine to machine communications in your products, leave us a comment below. If you are unsure how any of this can apply to your architecture, [hit the "talk to sales" button in our homepage](https://auth0.com/), we can help you out!