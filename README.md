# express-mung [![Build Status](https://travis-ci.org/richardschneider/express-prefer.svg)](https://travis-ci.org/richardschneider/express-mung)

Middleware for express responses.

This package allows synchronous and asynchronous transformation of an express response.  This is a similar concept to the express middleware for a request but for a response.  Note that the middleware is executed in LIFO order.  It is implemented by monkey patching (hooking) the `res.end` or `res.json` methods.


## Getting started [![npm version](https://badge.fury.io/js/express-mung.svg)](https://badge.fury.io/js/express-mung)

    $ npm install express-mung --save

Then in your middleware

    var mung = require('express-mung');

    module.exports = mung.json(my_transform);

## Usage

Sample middleware (redact.js) to remove classified information.

````javascript
'use strict';
const mung = require('express-mung');

/* Remove any classified information from the response. */
function redact(body, req, res) {
    // ...
    return body;
}

exports = mung.json(redact);
````

then add to your `app.js` file (before the route handling middleware)
````javascript
app.use(require('./redact'))
````
and [*That's all folks!*](https://www.youtube.com/watch?v=gBzJGckMYO4)

See the mocha [tests](https://github.com/richardschneider/express-mung/tree/master/test) for some more examples.

## Reference

- `mung.json(fn)` transform the JSON body of the response.  `fn(json, req, res)` receives the JSON as an object, the `req` and `res`.  It returns the modified body. If `undefined` is returned (i.e. nothing) then the original JSON is assumed to be modified.  If `null` is returned, then a 204 No Content HTTP status is returned to client.

- `mung.jsonAsync(fn)` transform the JSON body of the response.  `fn(json, req, res)` receives the JSON as an object, the `req` and `res`.  It returns a promise to a modified body.  The promise returns an `object.`  If it is `null` then a 204 No Content is sent to the client.

- `mung.headers(fn)` transform the HTTP headers of the response.  `fn(req, res)` receives the `req` and `res`.  It should modify the header(s) and then return.

- `mung.headersAsync(fn)` transform the HTTP headers of the response.  `fn(req, res)` receives the `req` and `res`.  It returns a `promise` to modify the header(s).

**NOTE** when `mung.json*` receives a scalar value then the `content-type` is switched `text-plain`.

**NOTE** when `mung.json*` detects that a response has been sent, it will abort.

**NOTE** sending a response while in `mung.headers*` is **undefined behaviour** and will most likely result in an error.

## Exception handling

`mung` catches any exception (synchronous, asynchronous or Promise reject) and sends an HTTP 500 response with the exception message.  This is done by `mung.onError(err, req, res)`, feel free to redefine it to your needs.

# License
The MIT license

Copyright © 2015 Richard Schneider (makaretu@gmail.com)
