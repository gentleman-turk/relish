![Relish Logo][logo]

_Better error messages for Hapi.js Joi validation_

[![NPM Version][version-img]][version-url]
[![Build Status][build-img]][build-url]
[![Coverage Status][coverage-img]][coverage-url]

## Introduction

[Joi](https://github.com/hapijs/joi) provides awesome schema validation, but the error messages returned are not user-friendly. This package returns a more user-friendly version of Joi's default response and allows for custom error messages.

This is taken from the original Relish package which appears to be abandoned.

**Default Joi Response**
```json
{
  "statusCode": 400,
  "error": "Bad Request",
  "message": "child \"data\" fails because [child \"name\" fails because [\"name\" is not allowed to be empty], child \"email\" fails because [\"email\" must be a valid email]]",
  "validation": {
    "source": "payload",
    "keys": [
      "data.name",
      "data.email"
    ]
  }
}
```

**Example Relish Response**
```json
{
  "statusCode": 400,
  "error": "Bad Request",
  "message": "\"name\" is not allowed to be empty, \"email\" must be a valid email",
  "validation": {
    "source": "payload",
    "errors": [
      {
        "key": "name",
        "path": "data.name",
        "message": "\"name\" is not allowed to be empty",
        "type": "any",
        "constraint": "required"
      },
      {
        "key": "email",
        "path": "data.email",
        "message": "\"email\" must be a valid email",
        "type": "string",
        "constraint": "email"
      }
    ]
  }
}
```

## Installation
```sh
npm install @condiments/relish --save
```

## Usage
First load and initialize the module

```js
// load the package and set custom message options
const Relish = require('@condiments/relish')({
  messages: {
    'data.name': 'Please enter your full name'
  }
});
```

Once initialized, this package exposes a custom `failAction` handler that can be used in your Hapi.js [Route Options][hapi-route-options].

```js
// call the failAction handler in your route options
server.route({
  method: 'POST',
  path: '/',
  config: {
    validate: {
      // set a custom failAction handler
      failAction: Relish.failAction,
      payload: {
        data: Joi.object({
          name: Joi.string().required(),
          email: Joi.string().email()
        })
      }
    }
  },
  handler: (request, h) => h.response()
});
```

#### Global Usage (alternative)
You can apply this module to all routes by setting the failAction in your server options.

```js
Hapi.server({
  // ... other server options

  routes: {
    validate: {
      failAction: Relish.failAction
    }
  }
});
```

## Version Compatibility
Below are the compatible versions of Relish for Hapi and Joi.

Relish Version | Hapi Version      | Joi Version
---------------|-------------------|------------
1.x            | <= 16.6.0         | <= 10.x
2.x            | >= 16.6.1, < 17.x | >= 11.x
3.x            | >= 17.x           | >= 11.x
4.x            | >= 18.x           | >= 16.x

## API Documentation
See the [API Documentation](https://github.com/gentleman-turk/relish/blob/v4.0.1/API.md) for reference.

<!-- Badge URLs -->
[logo]:images/relish.png
[build-img]:https://travis-ci.org/gentleman-turk/relish.svg?branch=master
[build-url]:https://travis-ci.org/gentleman-turk/relish
[coverage-img]:https://coveralls.io/repos/gentleman-turk/relish/badge.svg?branch=master&service=github
[coverage-url]:https://coveralls.io/github/gentleman-turk/relish?branch=master
[version-img]:https://badge.fury.io/js/%40condiments%2Frelish.svg
[version-url]:https://badge.fury.io/js/%40condiments%2Frelish
[style-img]:https://cdn.rawgit.com/feross/standard/master/badge.svg
[style-url]:http://standardjs.com/
[hapi-route-options]:http://hapijs.com/api#route-options
