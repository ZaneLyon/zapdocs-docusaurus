---
id: http
title: Making HTTP Requests
sidebar_label: Making HTTP Requests
---

There are two primary ways to make HTTP requests in the Zapier platform:

1. **Shorthand HTTP Requests** - these are simple object literals that make it easy to define simple requests.
2. **Manual HTTP Requests** - you use `z.request([url], options)` to make the requests and control the response. Use this when you need to change options for certain requests (for all requests, use middleware).

There are also a few helper constructs you can use to reduce boilerplate:

1. `requestTemplate` which is an shorthand HTTP request that will be merged with every request.
2. `beforeRequest` middleware which is an array of functions to mutate a request before it is sent.
3. `afterResponse` middleware which is an array of functions to mutate a response before it is completed.

> Note: you can install any HTTP client you like - but this is greatly discouraged as you lose [automatic HTTP logging](#http-logging) and middleware.

### Shorthand HTTP Requests

For simple HTTP requests that do not require special pre or post processing, you can specify the HTTP options as an object literal in your app definition.

This features:

1. Lazy `{{curly}}` replacement.
2. JSON de-serialization.
3. Automatic non-2xx error raising.

```javascript
[insert-file:./snippets/shorthand-request.js]
```

In the url above, `{{bundle.authData.subdomain}}` is automatically replaced with the live value from the bundle. If the call returns a non 2xx return code, an error is automatically raised. The response body is automatically parsed as JSON and returned.

An error will be raised if the response is not valid JSON, so _do not use shorthand HTTP requests with non-JSON responses_.

### Manual HTTP Requests

When you need to do custom processing of the response, or need to process non-JSON responses, you can make manual HTTP requests. This approach does not perform any magic - no status code checking, no automatic JSON parsing. Use this method when you need more control. Manual requests do perform lazy `{{curly}}` replacement.

To make a manual HTTP request, use the `request` method of the `z` object:

```javascript
[insert-file:./snippets/manual-request.js]
```

#### POST and PUT Requests

To POST or PUT data to your API you can do this:

```javascript
[insert-file:./snippets/put.js]
```

> Note: you need to call `z.JSON.stringify()` before setting the `body`.

### Using HTTP middleware

If you need to process all HTTP requests in a certain way, you may be able to use one of utility HTTP middleware functions.

> Example App: check out https://github.com/zapier/zapier-platform-example-app-middleware for a working example app using HTTP middleware.

Try putting them in your app definition:

```javascript
[insert-file:./snippets/middleware.js]
```

A `beforeRequest` middleware function takes a request options object, and returns a (possibly mutated) request object. An `afterResponse` middleware function takes a response object, and returns a (possibly mutated) response object. Middleware functions are executed in the order specified in the app definition, and each subsequent middleware receives the request or response object returned by the previous middleware.

Middleware functions can be asynchronous - just return a promise from the middleware function.

The second argument for middleware is the `z` object, but it does *not* include `z.request()` as using that would easily create infinite loops.

### HTTP Request Options

Shorthand requests and manual `z.request([url], options)` calls support the following HTTP `options`:

* `url`: HTTP url, you can provide it both `z.request(url, options)` or `z.request({url: url, ...})`.
* `method`: HTTP method, default is `GET`.
* `headers`: request headers object, format `{'header-key': 'header-value'}`.
* `params`: URL query params object, format `{'query-key': 'query-value'}`.
* `body`: request body, can be a string, buffer, readable stream or plain object. When it is an object/array and the `Content-Type` header is `application/x-www-form-urlencoded` the body will be transformed to query string parameters, otherwise we'll set the header to `application/json; charset=utf-8` and JSON encode the body. Default is `null`.
* `json`: shortcut object/array/etc. you want to JSON encode into body. Default is `null`.
* `form`: shortcut object. you want to form encode into body. Default is `null`.
* `raw`: set this to stream the response instead of consuming it immediately. Default is `false`.
* `redirect`: set to `manual` to extract redirect headers, `error` to reject redirect, default is `follow`.
* `follow`: maximum redirect count, set to `0` to not follow redirects. default is `20`.
* `compress`: support gzip/deflate content encoding. Set to `false` to disable. Default is `true`.
* `agent`: Node.js `http.Agent` instance, allows custom proxy, certificate etc. Default is `null`.
* `timeout`: request / response timeout in ms. Set to `0` to disable (OS limit still applies), timeout reset on `redirect`. Default is `0` (disabled).
* `size`: maximum response body size in bytes. Set to `0`` to disable. Default is `0` (disabled).

```javascript
z.request({
  url: 'http://example.com',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  // only provide body, json or form...
  body: {hello: 'world'}, // or '{"hello": "world"}' or 'hello=world'
  json: {hello: 'world'},
  form: {hello: 'world'},
  // access node-fetch style response.body
  raw: false,
  redirect: 'follow',
  follow: 20,
  compress: true,
  agent: null,
  timeout: 0,
  size: 0,
})
```

### HTTP Response Object

The response object returned by `z.request([url], options)` supports the following fields and methods:

* `status`: The response status code, i.e. `200`, `404`, etc.
* `content`: The response content as a String. For Buffer, try `options.raw = true`.
* `json`: The response content as an object (or `undefined`). If `options.raw = true` - is a promise.
* `body`: A stream available only if you provide `options.raw = true`.
* `headers`: Response headers object. The header keys are all lower case.
* `getHeader(key)`: Retrieve response header, case insensitive: `response.getHeader('My-Header')`
* `throwForStatus()`: Throw error if final `response.status > 300`. Will throw `z.error.RefreshAuthError` if 401.
* `request`: The original request options object (see above).

```javascript
z.request({
  // ..
}).then((response) => {
  // a bunch of examples lines for cherry picking
  response.status;
  response.headers['Content-Type'];
  response.getHeader('content-type');
  response.request; // original request options
  response.throwForStatus();
  // if options.raw === false (default)...
  JSON.parse(response.content);
  response.json;
  // if options.raw === true...
  response.buffer().then(buf => buf.toString());
  response.text().then(content => content);
  response.json().then(json => json);
  response.body.pipe(otherStream);
});
```
