---
id: transpilers
title: Using Transpilers
sidebar_label: Using Transpilers
---

We do not yet support transpilers out of the box, but if you would like to use `babel` or similar, we recommend creating a custom wrapper on `zapier push` like this in your `package.json`:

```json
{
  "scripts": {
    "zapier-dev": "babel src --out-dir lib --watch",
    "zapier-push": "babel src --out-dir lib && zapier push"
  }
}
```

And then you can have your fancy ES7 code in `src/*` and a root `index.js` like this:

```javascript
module.exports = require('./lib');
```

And work with commands like this:

```bash
# watch and recompile
npm run zapier-dev

# tests should work fine
zapier test

# every build ensures a fresh build
npm run zapier-push
```

There are a lot of details left out - check out the full example app for a working setup.

> Example App: check out https://github.com/zapier/zapier-platform-example-app-babel for a working example app using Babel.
