![alt text](static/nodejs.png "Node.js") ![alt text](static/hapi.png "Hapi")

# Node.js Style Guide & Best Pratices: Hapi

Hapi is a rich framework for building applications and services that allows developers to focus on writing reusable application logic instead of spending time building infrastructure.

*Hapi* plugin system allows to quickly build, extend, and compose brand-specific features on top of its rock-solid architecture.

In this guide, the following sections are covered:
* [Hello World](#hello-world)
* [Application structure](#application-structure)
* [Plugins](#plugins)
* [Api](#api)

## Hello World

Here is a basic example in *Hapi* to launch an application and to open *http://localhost:8000/hello* in your browser:

```javascript
'use strict';

import Hapi from 'hapi ';

// Create a server with a host and port
const server = new Hapi.Server();
server.connection({
  host: 'localhost',
  port: 8000
});

// Add the route
server.route({
  method: 'GET',
  path:'/hello',
  handler: (request, reply) => reply('hello world');
});

// Start the server
server.start(() => {
  console.log('Server running at:', server.info.uri);
});
```

*Hapi*'s great power relies in the extensive and powerful plugin system that allows you to very easily break your application up into isolated pieces of business logic, and reusable utilities.

A very rich set of plugins is available in the community, but custom plugins can be also used if required. A very simple plugin looks like:

```javascript
exports.register = (server, options, next) => {
  // Code
  // ...

  next();
};

exports.register.attributes = {
  name: 'my-plugin',
  version: '1.0.0'
};
```

The *options* parameter is the custom configuration when you use the plugin. The *next* is a method to be called when the plugin has completed the steps to be registered. Finally, the *server* object is a reference to the *server* your plugin is being loaded in.

Additionally, *attributes* is an object to provide some additional information about the plugin, as name or version.

In order to use a plugin, it needs to be registered in the server. For example:

```javascript
// load one plugin
server.register(
  {
    register: require('myplugin'),
    options: {
      key: 'value'
    },
  },
  (err) => {
    if (err) {
    console.error('Failed to load plugin:', err);
    }
  }
);

// load multiple plugins
server.register(
  [
    {
      register: require('myplugin'),
      options: {}
    },
    {
      register: require('yourplugin')
    }
  ],
  (err) => {
    if (err) {
      console.error('Failed to load a plugin:', err);
    }
  }
);
```

## Application structure

To create a new API server with *Hapi*, the following structure can be used:

```
+ server
| |
| + api
| | |
| | + my-endpoints
| |   |- my-endpoints.contoller.js
| |   |- index.js
| |
| + plugins
| | |- my-plugin.js
| |
| + config
| | |
| | + environment
| |   |- index.js
| |   |- development.js
| |   |- production.js
| |   |- other-environment.js
| |
| |- app.js
| |- routes.js
|
|- package.json
```

The *app.js* file is the main file of the application. The server is started and configured like this:

```javascript
import Hapi from 'hapi';
import config from './config/environment';

// Create a server with a host and port
const server = new Hapi.Server();
server.connection({ host: config.ip,  port: config.port , routes: { cors: true }});

// Register the server and start the application
server.register([
    {
      register: require('./routes') // config routes in external file
    },
    {
      register: require('hapi-mongodb'),
      options: {url: config.mongo.url}
    }
  ],
  {
    routes: {
      prefix: config.routes.prefix // prefix for all the api routes
    }
  },
  (err) => {
    if (err) throw err;

    server.start(() => {
      console.log('Server running at', server.info.uri);
    })
  }
);
```

In the *routes.js* file, all service routes are configured in a plugin:

```javascript
/**
 * Main application routes
 */

'use strict';

exports.register = (server, options, next) => {
  import myEndPoints from './api/my-endpoints';
  myEndPoints(server);

  next();
};
exports.register.attributes = {
  name: 'my-routes',
  version: '0.1.0'
};
```

In *config/environment*, external application's configuration is placed by environment. The *plugins* folder contains all needed custom plugins : routes, scheduler, utils... And finally, *api* folder contains all application's endpoints/services. Each set of endpoints is an individual folder with the *index.js* and the *controller.js*.

**index.js**
```javascript
'use strict';

import Assets from './assets.controller';

module.exports = (server) => {
  server.route({
    method: 'GET',
    path: '/assets',
    handler: (request, reply, next) => {
      Assets.getAssetsByAttributes(request, reply, next);
    }
  });

  server.route({
    method: 'POST',
    path: '/assets',
    handler: (request, reply, next) => {
      Assets.create(request, reply, next);
    }
  });

  server.route({
    method: 'PUT',
    path: '/assets/{key}',
    handler: (request, reply, next) => {
      Assets.modify(request, reply, next);
    }
  });

  server.route({
    method: 'DELETE',
    path: '/assets/{key}',
    handler: (request, reply, next) => {
      Assets.remove(request, reply, next);
    }
  });
}
```

**controller.js**
```javascript
'use strict';

export const getAssetsByAttributes = (req, res, next) => res([]).code(200);

export const create = (req, res, next) => res({}).code(201);

export const modify = (req, res, next) => res({}).code(200);

export const remove = (req, res, next) => res({}).code(200);
```

## Plugins

There is a large set of plugins for *Hapi* to implement a very rich number of standard tasks. The most popular ones are the following:

**Authentication**

- Third-party login: [bell](https://github.com/hapijs/bell)
- JSON Web Token (JWT): [hapi-auth-jwt](https://github.com/ryanfitz/hapi-auth-jwt)
- Custom authentication: [hapi-auth-hawk](https://github.com/hapijs/hapi-auth-hawk)

**API documentation**

- Swagger: [hapi-swagger](https://github.com/glennjones/hapi-swagger)

**Logging**

- Multiple outputs: [good](https://github.com/hapijs/good)
- Health server: [hapi-alive](https://github.com/idosh/hapi-alive)
- Process dump and cleaning up: [poop](https://github.com/hapijs/poop)

**Templating**

- JSON view engine: [hapi-json-view](https://github.com/gergoerdosi/nesive-hapi-json-view)

**Utilities**

- Socket: [hapi-io](https://github.com/sibartlett/hapi-io)
- Routes loader: [hapi-router](https://github.com/bsiddiqui/hapi-router)

**Validation**

- Request parameters validation: [ratify](https://github.com/mac-/ratify)

**Other modules (no plugins)**

- HTTP-friendly errors: [boom](https://github.com/hapijs/boom)
- General purpose utilities: [hoek](https://github.com/hapijs/hoek)
- Schema description and validator: [joi](https://github.com/hapijs/joi)
- Testing utility with code coverage: [lab](https://github.com/hapijs/lab)
- Multi-strategy object caching: [catbox](https://github.com/hapijs/catbox)

## API

*Hapi* has a fairly extensive [API](http://hapijs.com/api), that we can refer to develop new functionality on the applications.

The API is divided in four blocks:

* Server
* Request
* Response
* Plugins


___

[BEEVA](https://www.beeva.com) | Technology and innovative solutions for companies
