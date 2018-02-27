---
id: upgrading
title: Upgrading the Zapier SDK
sidebar_label: Upgrading the Zapier SDK
---

The CLI version of the Platform has 3 public packages:

- [`zapier-platform-cli`](https://github.com/zapier/zapier-platform-cli): This is the `zapier` command. Should be installed with `npm install -g zapier-platform-cli` and is used to interact with your app and Zapier, but doesn't run in nor is included by your app.

- [`zapier-platform-core`](https://github.com/zapier/zapier-platform-core): This is what allows your app to interact with Zapier. Apps require this package in their `package.json` file, and a specific version by design, so you can keep using an older "core" version without having to upgrade your code, but still being able to update your app. Should be installed with `npm install` in your app. You should manually keep this version number in sync with your `zapier-platform-cli` global one, but it's not required for all upgrades.

- [`zapier-platform-schema`](https://github.com/zapier/zapier-platform-schema): This is a separate package for maintainability purposes. It's a dependency installed automatically by `zapier-platform-core`. It's basically the package that enforces app schemas to match what Zapier has in the backend.

### Upgrading Zapier Platform CLI

Check your version with `$ zapier --version` and update with `$ npm -g update zapier-platform-cli`.

You can also [look at the Changelog](https://github.com/zapier/zapier-platform-cli/blob/master/CHANGELOG.md) to understand what changed between versions.

#### When to Update CLI

You should keep your `zapier-platform-cli` global package up-to-date to get the latest features and bug fixes for interacting with your app, but it's not mandatory. You should be able to use any publicly available `zapier-platform-cli` version.

### Upgrading Zapier Platform Core

Open your app's `package.json` file and update the number for the `zapier-platform-core` entry to what the latest public version is. You can see it on the right side of the [NPM registry page](https://www.npmjs.com/package/zapier-platform-core). After that you should be able to run `npm update` and get the latest goodies!

#### When To Update Core

You should keep your `zapier-platform-core` package up-to-date to get the latest features and bug fixes for your app when interacting with Zapier, but it's not mandatory. You should be able to use any publicly available `zapier-platform-core` version.

While not always required, it's also recommended you use the same `zapier-platform-cli` global package as the app's `zapier-platform-core` one, to ensure maximum compatibility.

