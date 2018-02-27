---
id: getting-started
title: Getting Started
sidebar_label: Getting Started
---

### What is an App?

A CLI App is an implementation of your app's API. You build a Node.js application
that exports a single object ([JSON Schema](https://zapier.github.io/zapier-platform-schema/build/schema.html#appschema)) and upload it to Zapier.
Zapier introspects that definition to find out what your app is capable of and
what options to present end users in the Zap Editor.

For those not familiar with Zapier terminology, here is how concepts in the CLI
map to the end user experience:

 * [Authentication](authentication.md), (usually) which lets us know what credentials to ask users
   for. This is used during the "Connect Accounts" section of the Zap Editor.
 * [Triggers](triggers-actions.md), which read data *from* your API. These have their own section in the Zap Editor.
 * [Creates](triggers-actions.md), which send data *to* your API to create new records. These are listed under "Actions" in the Zap Editor.
 * [Searches](triggers-actions.md), which find specific records *in* your system. These are also listed under "Actions" in the Zap Editor.
 * [Resources](resources.md), which define an object type in your API (say a contact) and the operations available to perform on it. These are automatically extracted into Triggers, Searches, and Creates.

### How does the CLI Platform Work?

Zapier takes the App you upload and sends it over to Amazon Web Service's Lambda.
We then make calls to execute the operations your App defines as we execute Zaps.
Your App takes the input data we provide (if any), makes the necessary HTTP calls,
and returns the relevant data, which gets fed back into Zapier.

### CLI vs the Web Builder Platform

From a user perspective, both the CLI and the existing web builder platform offer the same experience. The biggest difference is how they're developed. The CLI takes a much more code-first approach, allowing you to develop your Zapier app just like you would any other programming project. The web builder, on the other hand, is much better for folks who want to make an app with minimal coding involved. Both will continue to coexist, so pick whichever fits your needs best!

### Requirements

All Zapier CLI apps are run using Node.js `LAMBDA_VERSION`.

You can develop using any version of Node you'd like, but your eventual code must be compatible with `LAMBDA_VERSION`. If you're using features not yet available in `LAMBDA_VERSION`, you can transpile your code to a compatible format with [Babel](https://babeljs.io/) (or similar).

To ensure stability for our users, we strongly encourage you run tests on `LAMBDA_VERSION` sometime before your code reaches users. This can be done multiple ways.

Firstly, by using a CI tool (like [Travis CI](https://travis-ci.org/) or [Circle CI](https://circleci.com/), which are free for open source projects). We provide a sample [.travis.yml](https://github.com/zapier/zapier-platform-example-app-minimal/blob/master/.travis.yml) file in our template apps to get you started.

Alternatively, you can change your local node version with tools such as [nvm](https://github.com/creationix/nvm#installation) or [n](https://github.com/tj/n#installation).
Then you can either swap to that version with `nvm use LAMBDA_VERSION`, or do `nvm exec LAMBDA_VERSION zapier test` so you can run tests without having to switch versions while developing.


### Quick Setup Guide

> Be sure to check the [Requirements](#requirements) before you start! Also, we recommend the [Tutorial](https://github.com/zapier/zapier-platform-cli/wiki/Tutorial) for a more thorough introduction.

First up is installing the CLI and setting up your auth to create a working "Zapier Example" application. It will be private to you and visible in your live [Zap editor](https://zapier.com/app/editor).

```bash
# install the CLI globally
npm install -g zapier-platform-cli

# setup auth to Zapier's platform with a deploy key
zapier login
```

Your Zapier CLI should be installed and ready to go at this point. Next up, we'll create our first app!

```bash
# create a directory with the minimum required files
zapier init example-app

# move into the new directory
cd example-app

# install all the libraries needed for your app
npm install
```

> Note: there are plenty of templates & example apps to choose from! [View all Example Apps here.](#example-apps)

You should now have a working local app. You can run several local commands to try it out.

```bash
# run the local tests
# the same as npm test, but adds some extra things to the environment
zapier test
```

Next, you'll probably want to upload app to Zapier itself so you can start testing live.

```bash
# push your app to Zapier
zapier push
```

> Go check out our [full CLI reference documentation](http://zapier.github.io/zapier-platform-cli/cli.html) to see all the other commands!


### Tutorial

For a full tutorial, head over to our [wiki](https://github.com/zapier/zapier-platform-cli/wiki/Tutorial) for a comprehensive walkthrough for creating your first app. If this isn't your first rodeo, read on!


