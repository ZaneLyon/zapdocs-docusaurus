---
id: bundle
title: The Bundle Object
sidebar_label: The Bundle Object
---

This object holds the user's auth details and the data for the API requests.

> The `bundle` object is passed into your functions as the second argument - IE: `perform: (z, bundle) => {}`.

### `bundle.authData`

`bundle.authData` is user-provided authentication data, like `api_key` or `access_token`. [Read more on authentication.](#authentication)

### `bundle.inputData`

`bundle.inputData` is user-provided data for this particular run of the trigger/search/create, as defined by the inputFields. For example:

```javascript
{
  createdBy: 'his name is Bobby Flay'
  style: 'he cooks mediterranean'
}
```

### `bundle.inputDataRaw`

`bundle.inputDataRaw` is kind of like `inputData`, but before rendering `{{curlies}}`:

```javascript
{
  createdBy: 'his name is {{123__chef_name}}'
  style: 'he cooks {{456__style}}'
}
```

> "curlies" are data mapped in from previous steps. They take the form `{{NODE_ID__key_name}}`. You'll usually want to use `bundle.inputData` instead.

### `bundle.meta`

`bundle.meta` is extra information useful for doing advanced behaviors depending on what the user is doing. It has the following options:

| key | default | description |
| --- | --- | --- |
| frontend | `false` | if true, this run was initiated manually via the Zap editor |
| prefill | `false` | if true, this poll is being used to populate a dynamic dropdown |
| hydrate | `true`  | if true, the results of this run will be hydrated (false if we're in the middle of hydrating already) |
| test_poll | `false` | if true, the poll was triggered by a user testing their account (via [clicking "test"](https://cdn.zapier.com/storage/photos/5c94c304ce11b02c073a973466a7b846.png) on the auth |
| standard_poll| `true`  | the opposite of `test_poll` |
| first_poll | `false` | if true, the results of this poll will be used to initialize the deduplication list rather than trigger a zap. See: [deduplication](#dedup) |
| limit | `-1` | the number of items to fetch. `-1` indicates there's no limit (which will almost always be the case) |
| page | `0` | used in [paging](#paging) to uniquely identify which page of results should be returned |

**`bundle.meta.zap.id` is only available in the `performSubscribe` and `performUnsubscribe` methods**

The user's Zap ID is available during the [subscribe and unsubscribe](https://zapier.github.io/zapier-platform-schema/build/schema.html#basichookoperationschema) methods.

For example - you could do:

```javascript
const subscribeHook = (z, bundle) => {

  const options = {
    url: 'http://57b20fb546b57d1100a3c405.mockapi.io/api/hooks',
    method: 'POST',
    body: {
      url: bundle.targetUrl, // bundle.targetUrl has the Hook URL this app should call
      zap_id: bundle.meta.zap.id,
    },
  };

  return z.request(options).then((response) => response.json);
};

module.exports = {
  // ... see our rest hook example for additional details: https://github.com/zapier/zapier-platform-example-app-rest-hooks/blob/master/triggers/recipe.js
  performSubscribe: subscribeHook,
  // ...
};
```
