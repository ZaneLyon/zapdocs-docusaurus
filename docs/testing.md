---
id: testing
title: Testing
sidebar_label: Testing
---

You can write unit tests for your Zapier app that run locally, outside of the zapier editor.
You can run these tests in a CI tool like [Travis](https://travis-ci.com/).

### Writing Unit Tests

We recommend using the [Mocha](https://mochajs.org/) testing framework. After running
`zapier init` you should find an example test to start from in the `test` directory.

```javascript
[insert-file:./snippets/mocha-test.js]
```

### Mocking Requests

While testing, it's useful to test your code without actually hitting any external services. [Nock](https://github.com/node-nock/nock) is a node.js utility that intercepts requests before they ever leave your computer. You can specify a response code, body, headers, and more. It works out of the box with `z.request` by setting up your `nock` before calling `appTester`.

```js
[insert-file:./snippets/mocha-mocked-test.js]
```

There's more info about nock and its usage in its [readme](https://github.com/node-nock/nock/blob/master/README.md).

### Running Unit Tests

To run all your tests do:

```bash
zapier test
```

> You can also go direct with `npm test` or `node_modules/mocha/bin/mocha`.

### Testing & Environment Variables

The best way to store sensitive values (like API keys, OAuth secrets, or passwords) is in an `.environment` file ([learn more](https://github.com/motdotla/dotenv#faq)). Then, you can include the following before your tests run:

```js
const zapier = require('zapier-platform-core');
zapier.tools.env.inject(); // inject() can take a filename; defaults to ".environment"

// now process.env has all the values in your .environment file
```

> Remember: don't add your secrets file to version control!

Additionally, you can provide them dynamically at runtime:

```bash
CLIENT_ID=1234 CLIENT_SECRET=abcd zapier test
```

Or, `export` them explicitly and place them into the environment:

```bash
export CLIENT_ID=1234
export CLIENT_SECRET=abcd
zapier test
```


### Viewing HTTP Logs in Unit Tests


When running a unit test via `zapier test`, `z.console` statements and detailed HTTP logs print to `stdout`:

```bash
zapier test
```

Sometimes you don't want that much logging, for example in a CI test. To suppress the detailed HTTP logs do:

```bash
zapier test --quiet
```

To also suppress the HTTP summary logs do:

```bash
zapier test --very-quiet
```

### Testing in Your CI

Whether you use Travis, Circle, Jenkins, or anything else, we aim to make it painless to test in an automated environment.

Behind the scenes `zapier test` is doing a pretty standard `npm test` with [mocha](https://www.npmjs.com/package/mocha) as the backend.

This makes it pretty straightforward to integrate into your testing interface. If you'd like to test with [Travis CI](https://travis-ci.com/) for example - the `.travis.yml` would look something like this:

```yaml
language: node_js
node_js:
  - "6.10.2"
before_script: npm install -g zapier-platform-cli
script: CLIENT_ID=1234 CLIENT_SECRET=abcd zapier test
```

You can substitute `zapier test` with `npm test`, or a direct call to `node_modules/mocha/bin/mocha`. Also, we generally recommend putting the environment variables into whatever configuration screen Jenkins or Travis provides!

As an alternative to reading the deploy key from root (the default location), you may set the `ZAPIER_DEPLOY_KEY` environment variable to run privileged commands without the human input needed for `zapier login`. We suggest encrypting your deploy key in whatever manner you CI provides (such as [these instructions](https://docs.travis-ci.com/user/environment-variables/#Defining-encrypted-variables-in-.travis.yml), for Travis).

