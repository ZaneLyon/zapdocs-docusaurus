---
id: dehydration
title: Dehydration & File Stashing
sidebar_label: Dehydration & File Stashing
---

Dehydration, and its counterpart Hydration, is a tool that can lazily load data that might be otherwise expensive to retrieve aggressively.

* **Dehydration** - think of this as "make a pointer", you control the creation of pointers with `z.dehydrate(func, inputData)`
* **Hydration** - think of this as an automatic step that "consumes a pointer" and "returns some data", Zapier does this automatically behind the scenes

> This is very common when [Stashing Files](#stashing-files) - but that isn't their only use!

The method `z.dehydrate(func, inputData)` has two required arguments:

* `func` - the function to call to fetch the extra data. Can be any raw `function`, defined in the file doing the dehydration or imported from another part of your app. You must also register the function in the app's `hydrators` property
* `inputData` - this is an object that contains things like a `path` or `id` - whatever you need to load data on the other side

> **Why do I need to register my functions?** Because of how Javascript works with its module system, we need an explicit handle on the function that can be accessed from the App definition without trying to "automagically" (and sometimes incorrectly) infer code locations.

Here is an example that pulls in extra data for a movie:

```javascript
[insert-file:./snippets/dehydration.js]
```

And in future steps of the Zap - if Zapier encounters a pointer as returned by `z.dehydrate(func, inputData)` - Zapier will tie it back to your app and pull in the data lazily.

> **Why can't I just load the data immediately?** Isn't it easier? In some cases it can be - but imagine an API that returns 100 records when polling - doing 100x `GET /id.json` aggressive inline HTTP calls when 99% of the time Zapier doesn't _need_ the data yet is wasteful.


## Stashing Files

It can be expensive to download and stream files or they can require complex handshakes to authorize downloads - so we provide a helpful stash routine that will take any `String`, `Buffer` or `Stream` and return a URL file pointer suitable for returning from triggers, searches, creates, etc.

The interface `z.stashFile(bufferStringStream, [knownLength], [filename], [contentType])` takes a single required argument - the extra three arguments will be automatically populated in most cases. For example - a full example is this:

```javascript
const content = 'Hello world!';
z.stashFile(content, content.length, 'hello.txt', 'text/plain')
  .then(url => z.console.log(url));
// https://zapier-dev-files.s3.amazonaws.com/cli-platform/f75e2819-05e2-41d0-b70e-9f8272f9eebf
```

Most likely you'd want to stream from another URL - note the usage of `z.request({raw: true})`:

```javascript
const fileRequest = z.request({url: 'http://example.com/file.pdf', raw: true});
z.stashFile(fileRequest) // knownLength and filename will be sniffed from the request. contentType will be binary/octet-stream
  .then(url => z.console.log(url));
// https://zapier-dev-files.s3.amazonaws.com/cli-platform/74bc623c-d94d-4cac-81f1-f71d7d517bc7
```

> Note: you should only be using `z.stashFile()` in a hydration method - otherwise it can be very expensive to stash dozens of files in a polling call - for example!

See a full example with dehydration/hydration wired in correctly:

```javascript
[insert-file:./snippets/stash-file.js]
```

> Example App: check out https://github.com/zapier/zapier-platform-example-app-files for a working example app using files.
