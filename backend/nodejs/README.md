![alt text](static/nodejs.png "Node.js")

# Node.js Style Guide & Best Pratices
This document describes the way we use Node.js in BEEVA. We use it to develop lightweight and efficient network apps using an event-driven, non-blocking I/O on the top of Chrome's V8 JavaScript engine.

## Index

* [Introduction](#introduction)
  * [Purpose](#purpose)
  * [Challenges](#challenges)

* [Frameworks](#frameworks)
  * [Comparison](#comparison)

* [DevOps](#devOps)
  * [Logging](#logging)
  * [Security](#security)
  * [Clustering](#clustering)
  * [Staging](#staging)

* [Testing](#testing)
  * [TDD with Sinon and Proxyquire](#tdd-with-sinon-and-proxyquire)
  * [BDD with Cucumber](#bdd-with-cucumber)
  * [Integration Testing with Mocha](#integration-testing-with-mocha)
  
* [Best Practices](#best-practices)

* [References](#references)

## Introduction

As Python and other languages, Node.js can be used to develop either desktop/console tools or highly scalable network applications.
**This guide will focus on Node.js usage to develop network applications**.
Since developments make use of Javacript Node.js, we recommend reading the language specific section in this [guide](../../frontend/javascript/README.md).

### Purpose

The purpose of this guide is to share our knowledge in the development of network apps in Node.js, which allow building fast, scalable applications in a reliable way.

Covered areas include:

* Use of Frameworks: They ease applications' development providing several tools and design patterns.

* DevOps: Techniques and good practices focused on easy deploy, maintenance and monitoring the health of our Node.js apps

* Automated Testing with TDD & BDD: Series of advices in order to implement this testing philosophy in our developments.

### Challenges

The two main challenges to be resolved by a developer to program begins with Node.js are:

* Asynchrony: Although there are several ways to manage the async flow as libraries (i.e. [async](https://www.npmjs.com/package/async), [co](https://www.npmjs.com/package/co)), promises ([q](https://www.npmjs.com/package/q), [mpromise](https://www.npmjs.com/package/mpromise), ...) or conventions ([CPS](https://en.wikipedia.org/wiki/Continuation-passing_style)), they should be handled properly and avoiding excessive nesting callbacks ([Pyramid of Doom](http://tritarget.org/blog/2012/11/28/the-pyramid-of-doom-a-javascript-style-trap/)) and the excessive reliance on a particular library. A major problem arises when we are trying to follow the execution flow of our code and the error handling. Initially it is difficult to adopt this way of work and can be perceived as a loss of control over it. However, when mastered, it is a powerful tool that allows us to make better use of resources.

* Duality between Application and Server: When working with Node.js, you should understand that it is slightly different from those other familiar programming languages ​​like Java and PHP. Developing web applications using those languages usually involves the need of an application server, like Apache or Tomcat. Although this is usually shielded by frameworks, some DevOps aspects are even more important to be taken care of: application logs, error handling or profiling application issues ports, performance metrics...

## Frameworks

Node frameworks are layers on the top of http and use middleware plugins to interact with requests and responses.
Middlewares are a powerful but simple concept: a function or unit's output becomes the input for the next one. In this section we will discuss three main frameworks that we are using on productive projects. Some of them are placed in microservice environments, RESTful APIs and web servers. Finally, we provide a comparison in order to help choosing a tool for future projects with Node.js.

Each of the mentioned frameworks can be browsed in separate sections: 

  * [Express](./expressjs.md)
  * [Hapi](./hapijs.md)
  * [Restify](./restify.md)

### Comparison

#### When to use Restify instead of Express

1. Restify lets you build "strict" API services that are both maintainable and observable.

2. Restify provides automatic DTrace support for all your handlers, provided that they are running on a DTrace supported platform.

3. Restify is lighter than Express

#### When not to use Restify instead of Express

1. Express is appropriate in browser applications, supporting lots of functionality like rendering or templating. Restify does not support that features.

2. Express works better when a large number of queries are expected.

#### Conclusion Restify vs Express

If you need a framework that grants absolute control over interactions with HTTP and full observability into applications' latency and characteristics, **Restify** is your framework.

If, on the other hand, you do not need absolute control over these interactions or do not care at all, or if a large number of queries is expected, **Express** is a better option.

You can inspect a perfomance comparison between Hapi, Express and Restify in the following [link](https://raygun.io/blog/2015/03/node-performance-hapi-express-js-restify/)

## DevOps

This topic address topics related with DevOps, like staging, logging, security and clustering. Due to the dual nature of Node.js, these topics are important for developers and sysadmins.

### Logging

Log traces are essential for developers in order to have control over developed code. Although the most common way to log in Nodejs is to use *console.log*, it is not considered a good practice at all. Instead of spreading *console.log* statements and commenting them out when they are not needed, it is way better to use a logging library.

For example, in BEEVA we are using [Morgan](https://www.npmjs.com/package/morgan).

#### [Morgan](https://www.npmjs.com/package/morgan)

Morgan is a HTTP request logger middleware for node.js that uses apache style logs.

Output example:
´´´bash
127.0.0.1 - frank [10/Jan/2016:13:55:36 -0700] "GET /favicon.ico HTTP/1.0" 200 2326
127.0.0.1 - frank [10/Jan/2016:13:55:36 -0700] "GET /index.html HTTP/1.0" 200 2326
´´´  
example of use:
```javascript
import morgan from 'morgan';

morgan('combined', { skip: (req, res) => { return res.statusCode < 400 } });
```

#### [Bunyan](https://www.npmjs.com/package/bunyan) for the business logic logs.
Bunyan is a simple and fast JSON business library for node.js services. It is used for business logic logs.

Server logs should be structured, and JSON is an adequate format. A log record is one line of *JSON.stringify*'d output.

Output example:
´´´bash
{"name":"myserver","hostname":"banana.local","pid":123,"req":{"method":"GET","url":"/path?q=1#anchor","headers":{"x-hi":"Mom","connection":"close"}},"level":3,"msg":"start request","time":"2012-02-03T19:02:46.178Z","v":0}

´´´
Note: Take care choosing the content to add to every log. Although messages are fully customizable, logging the whole http request object of a full error stack could impact performance. 

Example of use:

```javascript
import bunyan from 'bunyan';
const log = bunyan.createLogger({name: "myapp"});
log.info("hi");
```
*Features*

- Elegant log method API
- Extensible stream system for controlling where log records go (to a stream, to a file, log file rotation, etc.)
- Bunyan CLI for pretty-printing and Bunyan logs filtering
- Easy inclusion of log source location (file, line, function) with *src: true*
- Lightweight specialization of Logger instances with *log.child*
- Custom rendering of logged objects with "serializers"
- Runtime log snooping via *Dtrace* support
- Support for browserify.


### Security

Even when developing non public applications, it is really important to pay attention to security aspects:

* Authentication, user an session management.
* Configuration Management i.e. Security HTTP Headers
* Data Validation on client side
* Database injections,...
* Security Transmission
* Denial Of Service
* Error Handling

All these areas are deeply covered in the security and hardening [section of this repository](../../it_security/security_hardening/README.md).

In this section, some references and middlewares that are easy to include as first protection are described.

#### [Lusca](https://www.npmjs.com/package/lusca)

Lusca is a Web application security middleware for Express. **It requires express-session**.

```javascript
  import express from 'express';
  const app = express();
  import session from 'express-session';
  import lusca from 'lusca';

//this or other session management will be required
app.use(session({
	secret: 'abc',
	resave: true,
	saveUninitialized: true
}));

app.use(lusca({
    csrf: true,
    csp: { /* ... */},
    xframe: 'SAMEORIGIN',
    p3p: 'ABCDEF',
    hsts: {maxAge: 31536000, includeSubDomains: true, preload: true},
    xssProtection: true
}));
```

You can opt into methods one by one:

```javascript
  app.use(lusca.csrf());
  app.use(lusca.csp({ /* ... */}));
  app.use(lusca.xframe('SAMEORIGIN'));
  app.use(lusca.p3p('ABCDEF'));
  app.use(lusca.hsts({ maxAge: 31536000 }));
  app.use(lusca.xssProtection(true));
```

#### [Helmet](https://www.npmjs.com/package/helmet)

Helmet can help protecting your app from some well-known web vulnerabilities by setting HTTP headers appropriately.
To use it in your application with Express:

```javascript
import express from 'express';
import helmet from 'helmet';

const app = express();
    app.use(helmet());
```

Helmet is a collection of 9 smaller middleware functions that set security-related HTTP headers.
> - **contentSecurityPolicy** for setting Content Security Policy to help prevent cross-site scripting attacks and other cross-site injections.
> - **hidePoweredBy** to remove the X-Powered-By header
> - **hpkp** for HTTP Public Key Pinning to prevent man-in-the-middle attacks with forged certificates.
> - **hsts** for HTTP Strict Transport Security that enforces secure (HTTP over SSL/TLS) connections to the server.
> - **ieNoOpen** sets X-Download-Options for IE8+
> - **noCache** to disable client-side caching
> - **noSniff** to keep clients from sniffing the MIME type
> - **frameguard** to prevent clickjacking
> - **xssFilter** adds some small XSS protections in most recent web browsers.

Running app.use(helmet()) will include 6 of the previous 9, leaving out *contentSecurityPolicy*, *hpkp*, and *noCache*.

You can also use each module individually:

```javascript
  app.use(helmet.noCache());
  app.use(helmet.frameguard());
```

### Clustering

Using the [Node.js Cluster API](https://nodejs.org/dist/latest-v4.x/docs/api/cluster.html) could boost your application's performance. It is useful but must be used with care. Remember that this API is stable only in versions greater than v.4

There are two ways of implement this api:

* **PM2 Interface** It adds support automatically and it is implemented easy and fast.

* **Manually in our code** It is more flexible because threads can share resources and messages amongst them.

It is recommended to add a thread per core, and in case of extreme stability require, one less for the management.

It is useful to add a mechanism to respawn threads and count the number of reboots to kill the app in case of unrecoverable problems, for the process management could restart the app.

### Staging

As with any other server some profiling options are supported. The most usual way to use them is through a property file.

* Manually through merging files with lodash.

*config.json* managed by SysOps
```javascript

const config = {
    app : {
        name : 'Config Backend',
        url : 'http://192.168.0.77',
        port : 3002
    },
    db: {
    	ip: '192.168.0.78',
    	port: 6379
    }
}

module.exports = config;

````

*index.js* managed by Developers

```javascript
var config = {
    app : {
        name : 'Config Backend',
        url : 'http://127.0.0.1',
        port : 3001
    },
    db: {
    	ip: '127.0.0.1',
    	port: 6379
    }
}
````

*index.js* Logic to merge (same file):

```javascript

// ...
var systemConfig='/external/path/to/config.js'; // i.e. /var/properties/project-name/config.js

// First option: manual
if(process.env.CONFIG && fs.existsSync(process.env.CONFIG)){
    console.log('Cargada la configuración manual de la ruta: '+process.env.CONFIG);
    customConfig=require(process.env.CONFIG);
// Second option: external properties file
} else if(fs.existsSync(systemConfig)){
    console.log('Cargada la configuración estándar SISTEMAS de la ruta: '+systemConfig);
    customConfig=require(systemConfig);
// Third option: local config file
} else if(fs.existsSync(envConfig)){
    console.log('Cargada la configuración local');
    customConfig=require(envConfig);
}
// ...

// Merging the files overriding local config with the externals
config=_.merge(
    config,
    customConfig
);
````

* Through packages as [dotenv](https://github.com/motdotla/dotenv)

## Testing

In this section we discuss a way of implementing TDD and BDD in your Node.js developments but if you want to go deeper, please visit the [testing section of this repository](../../qa_testing/testing/README.md).

### TDD with Sinon and Proxyquire

#### Focusing

Test-driven development (TDD) is one of the main agile development techniques. The strengths of TDD lies in increased quality of code, faster development resulting in greater programming confidence, and improved early bug detection.

#### Structure for TDD

```
my-application/
	test/
		unit-test/
			routes-test/
                routes-test-file1.js # routes-unit-test files
                routes-test-file2.js # to test functions of
                routes-test-fileN.js # your application
            controllers-test/
                controllers-test-file1.js # controllers-unit-test files
                controllers-test-file2.js # to test functions of
                controllers-test-fileN.js # your application
            models-test/
                models-test-file1.js # models-unit-test files
                models-test-file2.js # to test functions and methods of
                models-test-fileN.js # your application

```

#### Dependencies

##### Installation

Sinon.js is available as a npm module, it should be install globally with:

``` shell
$ npm install sinon
```

Proxyquire.js is available as a npm module, it should be install globally with:

``` shell
$ npm install proxyquire
```

Chai.js is also required and can be installed locally as a development dependency with:

``` shell
$ npm install --save-dev chai
```

#### Develop and run test

##### Describes and it functions

Tests Suites are grouped into describe functions, responsible of describing suite, and a function that contains inside the 'it' functions to include every single unit test case. Each 'it' function also contains the description of the single unit test and the function to test one component of your application (model, controller or route). If the complexity of file functions is greater probably will be necessary to use nested describe functions.

An example of a TDD test structure is the following:

```javascript
var chai       = require('chai');
var expect     = chai.expect;
var sinon      = require('sinon');
var proxyquire = require('proxyquire');

var model = require('../lib/daos/model1');
var controller = require('../lib/controllers/controller1');

var object = {
    counter : 1,
    id : "1234567890123456789012345",
    description : "New unit test for NodeJs"
};

var expectedResult = {
    ID : '1234567890123456789012345',
    DESCRIPTION : 'New unit test for NodeJs'
};

var error = new Error('ERROR');

var appMock =  {
    logger : {
        debug : sinon.spy()
    }
};

var controllerMock = proxyquire('../lib/controllers/controller1',
    {'../app':appMock});

describe('UNIT Test for CONTROLLER', function() {

    it('must exists', function () {
        var result = controller;
        expect(result).to.be.an('object');
        expect(result).to.include.keys(['createDE', 'store']);
    });

    it('Test should call all functions on createDE method', function () {
        var stub = sinon.stub(model.db, 'createDE');
        var callback = sinon.spy();

        controllerMock.createDE(callback);

        sinon.assert.calledOnce(stub);
        stub.restore();
    });

    it("Test should call all functions on save method - OK", function (done) {
        var args = {"ID": object.id, "DESCRIPTION": object.description};
        var stub = sinon.stub(model.db, 'save');
        stub.yields(null, expectedResult);

        controllerMock.store(args,function(err,data) {
            expect(data).to.be.an('Object');
            expect(data.ID).to.be.an('String');
            expect(data.ID).to.be.equal(object.id);
            expect(data.DESCRIPTION).to.be.an('String');
            expect(data.DESCRIPTION).to.be.equal(object.description);
            done();
        });
        stub.restore();
    });

    it("Test should call all functions on save method - Error", function (done) {
        var args = {"ID": object.id, "DESCRIPTION": object.description};
        var stub = sinon.stub(model.db, 'save');
        stub.yields(null, error);

        controllerMock.store(args,function(err,data) {
            expect(err).to.be.an('Object');
            expect(data).to.be.null;
            done();
        });
        stub.restore();
    });
});
```

#### Use cases

- To implement the different validation options you need to import *assert* and *expect* libraries, like this:

```javascript
var expect = require('chai').expect;
var assert = require('chai').assert;
```

And use them in the response of your functions like this:

```javascript
expect(err).to.be.null;
expect(data).to.be.string;
```

- This Test uses different mocks for functions and methods. For example:
	- Proxyquire. This library override methods of a module behave like the original. The original method invokes the app.js file. The test mock this invocation.

        ```javascript
        var controllerMock = proxyquire('../lib/controllers/controller1',
        					{'../app':appMock});
        ```

	- Sinon. *Stubs* allow us to pre-define the output method. It allows to use different successful (Ok) or Error outputs.

        ```javascript
        var stub = sinon.stub(model.db, 'save');
        stub.yields(null, expectedResult);
        ```

        or

        ```javascript
        var stub = sinon.stub(model.db, 'save');
        stub.yields(null, error);
        ```

	- Sinon. *Assert* allows to perform validations over how many times a method was called in tests.

		```javascript
        sinon.assert.calledOnce(stub);
        ```

- It is considered a good practice to use a callback function called *done()*, inside the *it* unit case function to try all the validations and to finish the case. And example is the following:

```javascript
controllerMock.store(args,function(err,data) {
    expect(data).to.be.an('Object');
    expect(data.ID).to.be.an('String');
    expect(data.ID).to.be.equal(object.id);
    expect(data.DESCRIPTION).to.be.an('String');
    expect(data.DESCRIPTION).to.be.equal(object.description);
    done();
});
```

- Another good practice is to use a *restore()* function before finishing the test:

```javascript
stub.restore();
```

#### Recommendations and some tips and tricks

> - Directory structure in tests has to mimic the one in the app code.
> - It is necessary to implement an it testcase for each function in the original file (model, controller, route).

#### Hooks

Hooks are functions that can be used to prepare and clean the environment before and after each test suite is executed. 

The following example uses hooks to create mocks used in different test, before and after the execution of test suite case:

```javascript
before(function(done) {
	var stub = sinon.stub(model.db, 'save');
	stub.yields(null, expectedResult);
});

after(function(done) {
   sinon.assert.calledOnce(stub);
});
```

You can get more information about Mocha and Chai in detail from both [Sinon](http://sinonjs.org/) and [Proxyquire](https://www.npmjs.com/package/proxyquire)

### BDD with Cucumber

#### Structure for BDD

```
my-application/
	test/
		acceptance-test/
			features/
				step_definitions/
					stepdefinition-file1 # methods, helpers and
					stepdefinition-file2 # variables for describing
					stepdefinition-fileN # step definitions
				support/
					hooks.js # hooks functions for clean environment
					world.js # file with all properties and funcions to be used in step definitions
				feature-file1.feature # feature files with scenarios
				feature-file2.feature # and steps for all user histories
				feature-fileN.feature # defined in you application
			npm-debug.log # npm
			my-application.log # application acceptance-test logging file
		mocks/
			mocks-file1.js # methods and funcions
			mocks-file2.js # for mocking during unit
			mocks-fileN.js # tests in your application
		unit-test/
			unit-test-file1.js # unit-test files for testing
			unit-test-file2.js # functions and methods of
			unit-test-fileN.js # your application
```

#### Dependencies

##### Installation

Cucumber.js is available as an npm module. Install globally with:

``` shell
$ npm install -g cucumber
```

Install as a development dependency of your application with:

``` shell
$ npm install --save-dev cucumber
```

##### Develop and run test

To check code and fields, you will also probably need:

* chai
* json-schema

#### Features

The acceptance-test features with user histories must be packed and stored in files with *.feature* extension inside a *feature* folder. Those Features are written using Gherkin language:

``` gherkin
# features/feature-file1.feature

Feature: Example feature
	As a user of cucumber.js
	I want to have documentation on cucumber
	So that I can concentrate on building awesome applications

	Scenario: Reading documentation
		Given I am on the Cucumber.js GitHub repository
		When I go to the README file
		Then I should see "Usage" as the page title
```

It is not the purpose of this guide how to describe correct features in Gherkin, but there are a set of recommendations:

1. Use *Background* and *Scenario Outline* if it's posible.
2. Do not write large feature files. You can pack these features in more files. For example, if you have 24 scenarios for testing two different application param values, you can choose:
	* createnotification.feature (24 Scenarios). **NOT GOOD**
	* createnotification_app1.feature (the first 12 Scenarios) and createnotification_app2.feature (the remaining 12 Scenarios). **BETTER**
	* createnotification_app1_ok.feature (the first 2 Scenarios for app1), createnotification_app1_errors.feature (the remaining 10 Scenarios for app1), createnotification_app2_ok.feature (the first 2 Scenarios for app2) and createnotification_app2_errors.feature (the remaining 10 Scenarios for app2). **BEST**

You can check the official cucumber github repository for a beggining guide [Feature-Introduction](https://github.com/cucumber/cucumber/wiki/Feature-Introduction)

You can also review more examples about how to describe Features using Gherkin in this [link](http://docs.behat.org/en/v2.5/guides/1.gherkin.html)


#### Step Definitions

Step definitions are defined in javascript files under *my.application/features/step_definitions* folder. This step definitions are

Best practices:
1. Do not write redundant or very similar Step definitions. You can use the same step definition for all of them.
2. Do not write ambiguous or very similar Step definitions. You can group them in an unique step.
3. Group step definitions by functionality, and use only one for common step definitions. Example:

```
my-application/
	test/
		acceptance-test/
			features/
				step_definitions/
					createApp_stepdefinitions.js
					getApp_stepdefinition.js
					common_stepdefinitions.js
				createApp_ok.feature
				createApp_error.feature
				getApp_ok.feature
				getApp_error.feature

```

4. Group *Given*, *When* and *Then* step definitions in the same file section. Example:

``` gherkin
	Given(/^step 1 definition$/, function (callback) {
		//step code
	});

	Given(/^step 2 definition$/, function (callback) {
		//step code
	});

	When(/^step 3 definition$/, function (callback) {
		//step code
	});

	When(/^step 4 definition$/, function (callback) {
		//step code
	});

	Then(/^step 5 definition$/, function (callback) {
		//step code
	});

	Then(/^step 6 definition$/, function (callback) {
		//step code
	});

```

Here is a simple example:

``` javascript
var expect = require('chai').expect;

module.exports = function () {
	this.World = World = require("../support/world.js").World;
	var 	Given = this.Given,
		When = this.When,
		Then = this.Then;

	Given(/^the following data for creating a notification:$/, function (table, callback) {
		this.setData("notificationData", table.hashes()[0]);
		callback();
	});

	When(/^I try to create a notification$/, function (callback) {
		World.tryToCreateNotification.call(this, callback);
	});

	Then(/^the response must be "([^"]*)"$/, World.responseMustBe);

	Then(/^I receive all required info for creating notification$/, function (callback) {
		World.checkCreateNotificationResponseData.call(this, callback);
	});

	Then(/^the notification was created and stored$/, function (callback) {
		World.checkNotificationExists.call(this, callback);
	});
};
```
You can also review more examples how to describe Step definitions in this [link]
(https://github.com/cucumber/cucumber/wiki/Step-Definitions)

#### Support Files

Support files allows you to setup the environment in which steps will be run, and define step definitions. The most important support file is the *World* function, but you may need more functions for testing.

The secondary files you need to develop and store are the hooks functions. These two with other acceptance-test functions files must be stored in support subfolder.

#### World function

*World* is a constructor function with utility properties, intended to be used in step definitions. World function file should have this desired structure:

1. Require section for imports.

2. World function declaration.

3. Functions for setting, getting and cleaning data of every scenario.

4. Validation functions for checking JSON objects in responses.

5. Server properties and constants for making requests.

6. Functions for starting and stopping a mock server for acceptance-tests.

7. Request functions to call server endpoints you wish to call.

8. Client-side response methods for check if you receive the expected responses.

9. Test description function where you can prepare the request to server endpoints.

This is one valid example for this structure:

```javascript
var	request = require('request'),//1
	zombie = require('zombie'),
	expect = require('chai').expect,
	async = require('async'),
	server = require('server'),
	extend = require('extend');

var	World = function World(callback) {//2
	var sharedData = {};

	this.setData = function (field, data) {//3
		sharedData[field] = data;
	};

	this.getData = function (field) {//3
		return sharedData[field];
	};

	this.clearData = function (field) {//3
		if (field) {
			delete sharedData[field];
		} else {
			shardData = {};
		}
	};

	this.validations = {
		validationOne: function (data) {//4
			expect(data).to.be.ok;
		}
	};

	var SERVER_URL = this.SERVER_URL = "http://localhost:3000/my-application";//5
	var VERSION = this.VERSION = "v1";//5

	var applicationUrls = this.urls = {//5
		endpoint: SERVER_URL + VERSION + '/{applicationId}'
	};

	var config = this.config = {//5
		//API properties
		name: 'my-application',
		port: 3000,
		version: "0.0.1",
		apiversion: "v1",
		//Store properties
		store: {
			type: "mongodb",
			url: "mongodb://localhost:27017/my-application_test",
			collection: "notifications",
			options: {}
		},
		//Logging properties
		log: {
			fileName: './my-application.log',
			console: false
		}
	};

	this.startServer = function () {//6
		return server.start(config);
	};

	this.stopServer = function () {//6
		server.stop();
	};

	logger.init(config.log);

	this.requestFunction = function (param1, param2, ..., paramN, callback) {//7
		request.post({
			url: url,
			json: json,
			headers: {
			}
		}, callback);
	};

	callback();
};

World.responseMustBe = function (expectedStatus, callback) {//8
	var statusCode = this.getData('response').statusCode;
	expect(statusCode).to.be.equal(Number(expectedStatus));
	callback();
};

World.followingRequest = function (items, callback) {//9
	async.eachSeries(items, function (item, callback) {
		this.requestFunction(item.param1, item.param2, ..., item.paramN, function (error, response, body) {
			expect(error).to.be.null;
			expect(response.statusCode).to.equal(201);
			callback();
		}.bind(this));
	}.bind(this), function (error) {
		expect(error).to.not.exist;
		callback();
	}.bind(this));
};

exports.World = World;
```

#### Hooks

Hooks are functions that can be used to prepare and clean the environment before and after each scenario is executed. Hooks can use callbacks, return promises, or can be synchronous. The first argument into hooks is always the current scenario.

There are four different Hook function types:

1. Scenario hooks: will be run before/after the first/last step of each scenario. They will run in the same order they are registered.

2. Step hooks: will be run before/after every step of each scenario. They do not work in scenarios with backgrounds.

3. Tagged hooks: will be run before/after certain scenarios. You have to use tags to select a subset of scenarios to run with this kind of hooks.

4. Global hooks: will be run once before any scenario is run.

Best practices about hooks follow:

* Pack all hooks in a single file.
* Store this file with *World* function file.
* Use a little set of hooks.

The following example are hooks for cleaning data repository (in mongoDB) before every scenario, and start/stop server in every Scenario:

```javascript
var expect = require('chai').expect,
	MongoClient = require('mongodb').MongoClient;

var hooks = function () {
	var Before = this.Before,
		After = this.After,
		Around = this.Around;

	Before(function (done) {
		var that = this;
		this.startServer().then(function () {
			MongoClient.connect(that.config.store.url, function (error, db) {
				db.collection(that.config.store.collection).remove(function (error) {
					db.close();
					done();
				});
			});
		}).fail(function (error) {
			expect(error).to.be.null;
			done();
		});
	});

	After(function (done) {
		var that = this;
		this.stopServer();
		done();
	});
};

module.exports = hooks;
```

### Integration Testing with Mocha

#### Focusing
Integration testing is where end-to-end tests are written, verifying the state of the app/UI along the way.

#### Structure for Integration Testing

```
my-application/
	test/
		integration-test/
			lib/
				app-test.js # your application
			models-test/
				models-test-file1.js # models-integration-test files
				models-test-file2.js # to test functions and methods of
				models-test-fileN.js # your application

```

#### Dependencies

##### Installation

*Mocha.js* are available as a npm module, it should be installed globally with:

``` shell
$ npm install -g mocha
```

And locally inside your project as a development dependency for your application with:
``` shell
$ npm install --save-dev mocha
```

It is also necessary to install *Chai.js* locally as a development dependency with:
``` shell
$ npm install --save-dev chai
```

#### Develop an run test

##### Describes and it functions
The purpose of integration testing is to verify functional, performance, and reliability requirements placed on major design items. These "design items", i.e., assemblages (or unit groups), are exercised through their interfaces using black box testing, success and error cases being simulated via appropriate parameter and data inputs. 

Simulated usage of shared data areas and inter-process communication is tested and individual subsystems are exercised through their input interface. Test cases are implemented to test whether all components within assemblages interact correctly, for example across procedure calls or process activations. This is done after testing individual modules, i.e., unit testing. The overall idea is a "building block" approach, in which verified assemblages are added to a verified base which is then used to support the integration testing of further assemblages.

A structure example is the following:

```javascript
var assert = require('chai').assert;
var pg = require('pg');
var supertest = require('supertest');
var commons = require('commons');
var commonsUtils = commons.utils;

var app = require('../../../lib/app');
var config = require('../../../config/env');
var models = require('../model/object');

function checkQueues(client, data, done) {
    data.FEC_INI = commonsUtils.getTodayDate();
    data.FEC_FIN = commonsUtils.getTodayDate();
    checkQueue(client, config.queue.name, data, done);
}

function checkQueue(client, queueName, object, callback) {
    //Check message in next queue
    client.receiveMessage({qname: queueName}, function (err, message) {
        if (err) {
            callback(err);
        } else {
            if (message && message.id && message.message) {
                var data = JSON.parse(message.message);
                if (data.type === 'object') {
                    delete data.ID;
                    delete object.ID;
                    assert.deepEqual(object, data);
                    callback();
                } else
                    callback("Data is wrong type");
            } else
                callback('Empty message in next queue');
        }
    });
}

function clearCounter(data, callback) {
    pg.connect(config.postgres.url, function (err, db, done) {
        if (err) {
            callback(err);
        } else {
            db.query("delete from " + config.postgres.tables.counter + " where DES_OBJECT = \'" + data.DES_OBJECT + "\' and COD_OBJECT = \'" + data.COD_OBJECT + "\';", function (err, result) {
                done();
                if (err) {
                    callback(err);
                } else {
                    callback();
                }
            });
        }
    });
}

function init(callback) {
    commons.redisMQ.connect(config.redis, function (err, client) {
        if (err)
            callback(err);
        else {
            client.deleteQueue({qname: config.queue.name}, function (err, data) {
                client.createQueue({qname: config.queue.name}, function (err, data) {
                    if (err)
                        callback(err);
                    else
                        callback(null, client);
                });
            });
        }
    });
}

describe('INTEGRATION TEST - object', function () {
    this.timeout(0);

    var client, request;
    before('Creating Redis client, deleting queue, clear database, creating HTTP client', function (done) {
        init(function (err, cl) {
            if (err)
                done(err);
            else {
                client = cl;
                request = supertest('http://' + config.server.host);
                app.start(config.server.port);
                setTimeout(done, 5000);
            }
        });
    });

    describe('Testing Execute.', function () {

        beforeEach('Clearing counter in database', function (done) {
            clearCounter(models.data, done);
        });

        it('Should execute ok', function (done) {
            request.post('/api/execute')
                .set('Accept', 'application/json')
                .set('x-unique-id', '5115bcce-59bf-4948-9441-4bb1f2e2d388')
                .set('Content-Type', 'application/json')
                .send(models.args)
                .end(function (err, res) {
                    if (err)
                        done(err);
                    else {
                        assert.equal(res.status, 200);
                        setTimeout(function () {
                            checkQueues(client, models.data, done);
                        }, 10000);
                    }
                });
        });

        it('Should fail validate', function (done) {
            request.post('/api/execute')
                .set('Accept', 'application/json')
                .set('x-unique-id', '5115bcce-59bf-4948-9441-4bb1f2e2d388')
                .set('Content-Type', 'application/json')
                .send(models.argsError)
                .end(function (err, res) {
                    if (err)
                        done(err);
                    else {
                        assert.equal(res.status, 500);
                        done();
                    }
                });
        });
    });
});
```

#### Use cases

- To implement different validation options you need to import the *assert* and *expect* libraries, you can do this with:

    ```javascript
var assert = require('chai').assert;
    ```

And use them in your functions' responses like this:

    ``` javascript
assert.equal(res.status, 200);
    ```

- It is good practice to use a callback function *done()*, inside the 'it' integration case function to try all the validations and to finish the case. And example is the following:

```javascript
.end(function (err, res) {
    if (err)
        done(err);
    else {
        assert.equal(res.status, 200);
        setTimeout(function () {
            checkQueues(client, models.data, done);
        }, 10000);
    }
});
```

- To implement a route file test simulating an http call with get, post or other methods, you need the *supertest* library, which you can install as development dependency with:

```javascript
$ npm install --save-dev supertest
```

- You can import it with:

```javascript
var supertest = require('supertest');
var request = supertest('http://' + config.server.host);
```

- And you can use this library to try a url with post http method in an it test case function like this example:

```javascript
it('Should execute ok', function (done) {
    request.post('/api/execute')
        .set('Accept', 'application/json')
        .set('x-unique-id', '5115bcce-59bf-4948-9441-4bb1f2e2d388')
        .set('Content-Type', 'application/json')
        .send(models.args)
        .end(function (err, res) {
            if (err)
                done(err);
            else {
                assert.equal(res.status, 200);
                setTimeout(function () {
                    checkQueues(client, models.data, done);
                }, 10000);
            }
        });
});
```

- Also it is a good practice to implement test cases with wrong data to try the error exceptions like this example:

```javascript
it('Should fail validate', function (done) {
    request.post('/api/execute')
        .set('Accept', 'application/json')
        .set('x-unique-id', '5115bcce-59bf-4948-9441-4bb1f2e2d388')
        .set('Content-Type', 'application/json')
        .send(models.argsError)
        .end(function (err, res) {
            if (err)
                done(err);
            else {
                assert.equal(res.status, 500);
                done();
            }
        });
});
```

#### Recommendations and some tips and tricks

> - To model tests you need to create a test environment with different information about start port, database name, etc... because the tests execution can not interrupt or save data in a execution environment. Example: *config.server.port*
> - Use a mock file to get some fake data to store and delete in a test database to try the model functions of crud operations.

You can get more information about Mocha and Chai in detail from both [Mocha](https://mochajs.org/) and [Chai](http://chaijs.com/)

## Best Practices

This brief section is intended to give some easy and quick tips to remember during any Node.js development.

* Modularize developments as far as possible.
* Use [Javascript patterns](../../frontend/general/javascript/patterns) as much possible.
* Use *Strict* mode. With this flag you can opt in to use a restricted variant of JavaScript. It eliminates some silent errors and will throw them all the time.
* Use tools for Static code analysis. Use either JSLint, JSHint or ESLint. Static code analysis can catch a lot of potential problems with your code early on.
* No eval, or friends. Eval is not the only one you should avoid, in the background each one of the following expressions use eval: *setInterval(String, 2)*, *setTimeout(String, 2)* and *new Function(String)*. Why should you avoid eval? It can open up your code for injections attacks and is slow (as it will run the interpreter/compiler).
* Use a framework that helps you to structure the project.
* Never use deprecated versions of Express (for example 2.x and 3.x are no longer maintained). Security and performance issues in these versions will not be fixed.
* Complete your developments with automated testing.
* Always use a CVS like GIT, SVN, and follow its best practices like GitFlow, Trunk, Branching,...

* Avoid usage of *console.log()* in your code.
* Always use configuration files instead of variables for ports, ips of other machines, ...
* Implement different logs for application and for Node.js.
* Use ES6 new features i.e. arrow function, to make the Scope safety and your code more concise and clarity. Check the examples in the Javascript section of this guide: [Arrow Functions](https://github.com/beeva/beeva-best-practices/tree/master/frontend/general/javascript/es6#arrow_functions).
* Use domains facing try-catch blocks for error handling.
* In public servers add a safety middleware as [helmet](https://www.npmjs.com/package/helmet) or [lusca](https://www.npmjs.com/package/lusca).

* Include and maintain [package.json](https://docs.npmjs.com/files/package.json) file with the version number (using *$npm init*).
* Mark the package as private: true to its release.
* For production applications control the version number of your units (according to importance of the project set to minor or patch).
* Define and test entry points in the distribution file [package.json](https://docs.npmjs.com/files/package.json#scripts).
* Use *DevDependencies* and *Dependencies* sections of [package.json](https://docs.npmjs.com/files/package.json#dependencies).
* Use [retire](https://www.npmjs.com/package/retire) to verify outdated or unsafe dependencies.
* Do not install units in the global development environment.
* Before deployments delete the *node_modules* folder and check file dependencies [package.json](https://docs.npmjs.com/files/package.json).

* Use tools like [PM2](https://www.npmjs.com/package/pm2) or [forever](https://www.npmjs.com/package/forever) as a tool for application restart.

* [Use LTS versions of Node.js for Production](https://nodejs.org/en/blog/community/node-v5/), since 4.2.* all even versions are *LTS* and odd like 5.3.* are *Stable with latest features*.
* Install node and npm interpreters localy through [NVN](https://github.com/creationix/nvm) without using sudo.
* Clear the local cache after each update NPM version: *$npm cache clean*

Another interesting point thinking on how web applications should be written is following the [The Twelve-Factor application manifesto](http://12factor.net/).

* One codebase tracked in revision control, many deploys.
* Explicitly declare and isolate dependencies.
* Store config in the environment.
* Treat backing services as attached resources.
* Strictly separate build and run stages.
* Execute the app as one or more stateless processes.
* Export services via port binding.
* Scale out via the process model.
* Maximize robustness with fast startup and graceful shutdown.
* Keep development, staging, and production as similar as possible.
* Treat logs as event streams.
* Run admin/management tasks as one-off processes.

## References

Node.js and Best Practices
* [Node.js Oficial WebSite](http://www.nodejs.org)
* [Node.js design patterns](https://blog.risingstack.com/fundamental-node-js-design-patterns/)
* [Risingstack Best Practices](https://github.com/RisingStack/node-style-guide)
* [ES6 & callback-promise Best Practices](https://blog.risingstack.com/how-to-become-a-better-node-js-developer-in-2016)
* [Heroku Best Practices](https://devcenter.heroku.com/articles/node-best-practices)

Cheatsheets
* [Overapi Cheatsheet](http://overapi.com/nodejs)
* [NPM Cheatsheet](http://browsenpm.org/help)

Frameworks
* [Express Framework](http://expressjs.com)
* [Express Performance best practices](http://expressjs.com/en/advanced/best-practice-performance.html)
* [Express Security best practices](http://expressjs.com/en/advanced/best-practice-security.html)
* [Hapi Framework](http://hapijs.com)
* [Restify Framework](http://restify.com)

DevOps
* [package.json full example](http://browsenpm.org/package.json)
* [Joyent Production Best Practices](https://www.joyent.com/developers/node)

Security
* [OWASP Web Application Security Testing Cheat Sheet](https://www.owasp.org/index.php/Web_Application_Security_Testing_Cheat_Sheet)
* [Node.js Security Checklist](https://blog.risingstack.com/node-js-security-checklist)

___

[BEEVA](https://www.beeva.com) | Technology and innovative solutions for companies
