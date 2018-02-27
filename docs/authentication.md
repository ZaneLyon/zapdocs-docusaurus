---
id: authentication
title: Authentication
sidebar_label: Authentication
---

Most applications require some sort of authentication - and Zapier provides a handful of methods for helping your users authenticate with your application. Zapier will provide some of the core behaviors, but you'll likely need to handle the rest.

> Hint: You can access the data tied to your authentication via the `bundle.authData` property in any method called in your app. Exceptions exist in OAuth and Session auth. Please see them below.

### Basic

Useful if your app requires two pieces of information to authentication: `username` and `password` which only the end user can provide. By default, Zapier will do the standard Basic authentication base64 header encoding for you (via an automatically registered middleware).

> Example App: check out https://github.com/zapier/zapier-platform-example-app-basic-auth for a working example app for basic auth.

> Note: if you do the common API Key pattern like `Authorization: Basic APIKEYHERE:x` you should look at the "Custom" authentication method instead.

```javascript
[insert-file:./snippets/basic-auth.js]
```

### Custom

This is what most "API Key" driven apps should default to using. You'll likely provide some custom `beforeRequest` middleware or a `requestTemplate` to complete the authentication by adding/computing needed headers.

> Example App: check out https://github.com/zapier/zapier-platform-example-app-custom-auth for a working example app for custom auth.

```javascript
[insert-file:./snippets/custom-auth.js]
```

### Digest

Very similar to the "Basic" authentication method above, but uses digest authentication instead of Basic authentication.

```javascript
[insert-file:./snippets/digest-auth.js]
```

### Session

Probably the most "powerful" mechanism for authentication - it gives you the ability to exchange some user provided data for some authentication data (IE: username & password for a session key).

> Example App: check out https://github.com/zapier/zapier-platform-example-app-session-auth for a working example app for session auth.

```javascript
[insert-file:./snippets/session-auth.js]
```

> Note - For Session auth, `authentication.sessionConfig.perform` will have the provided fields in `bundle.inputData` instead of `bundle.authData` because `bundle.authData` will only have "previously existing" values, which will be empty the first time the Zap runs.

### OAuth2

Zapier's OAuth2 implementation is based on the `authorization_code` flow, similar to [GitHub](http://developer.github.com/v3/oauth/) and [Facebook](https://developers.facebook.com/docs/authentication/server-side/).

> Example App: check out https://github.com/zapier/zapier-platform-example-app-oauth2 for a working example app for oauth2.

It looks like this:

  1. Zapier sends the user to the authorization URL defined by your App
  2. Once authorized, your website sends the user to the `redirect_uri` Zapier provided (`zapier describe` to find out what it is)
  3. Zapier makes a call on the backend to your API to exchange the `code` for an `access_token`
  4. Zapier remembers the `access_token` and makes calls on behalf of the user
  5. (Optionally) Zapier can refresh the token if it expires

You are required to define the authorization URL and the API call to fetch the access token. You'll also likely want to set your `CLIENT_ID` and `CLIENT_SECRET` as environment variables:

```bash
# setting the environment variables on Zapier.com
$ zapier env 1.0.0 CLIENT_ID 1234
$ zapier env 1.0.0 CLIENT_SECRET abcd

# and when running tests locally, don't forget to define them!
$ CLIENT_ID=1234 CLIENT_SECRET=abcd zapier test
```

Your auth definition would look something like this:

```javascript
[insert-file:./snippets/oauth2.js]
```

> Note - For OAuth, `authentication.oauth2Config.authorizeUrl`, `authentication.oauth2Config.getAccessToken`, and `authentication.oauth2Config.refreshAccessToken`  will have the provided fields in `bundle.inputData` instead of `bundle.authData` because `bundle.authData` will only have "previously existing" values, which will be empty the first time the Zap runs.
