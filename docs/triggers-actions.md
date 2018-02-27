---
id: triggers-actions-searches
title: Triggers, Creates, & Searches
sidebar_label: Triggers, Creates, & Searches
---

Triggers, Searches, and Creates are the way an app defines what it is able to do. Triggers read
data into Zapier (i.e. watch for new recipes). Searches locate individual records (find recipe by title). Creates create
new records in your system (add a recipe to the catalog).

The definition for each of these follows the same structure. Here is an example of a trigger:

```javascript
const recipeListRequest = {
  url: 'http://example.com/recipes'
};

const App = {
  //...
  triggers: {
    new_recipe: {
      key: 'new_recipe', // uniquely identifies the trigger
      noun: 'Recipe', // user-friendly word that is used to refer to the resource
      // `display` controls the presentation in the Zapier Editor
      display: {
        label: 'New Recipe',
        description: 'Triggers when a new recipe is added.'
      },
      // `operation` implements the API call used to fetch the data
      operation: {
        perform: recipeListRequest
      }
    },
    another_trigger: {
      // Another trigger definition...
    }
  }
};

```

You can find more details on the definition for each by looking at the [Trigger Schema](https://zapier.github.io/zapier-platform-schema/build/schema.html#triggerschema),
[Search Schema](https://zapier.github.io/zapier-platform-schema/build/schema.html#searchschema), and [Create Schema](https://zapier.github.io/zapier-platform-schema/build/schema.html#createschema).

> Example App: check out https://github.com/zapier/zapier-platform-example-app-trigger for a working example app using triggers.

> Example App: check out https://github.com/zapier/zapier-platform-example-app-rest-hooks for a working example app using REST hook triggers.

> Example App: check out https://github.com/zapier/zapier-platform-example-app-search for a working example app using searches.

> Example App: check out https://github.com/zapier/zapier-platform-example-app-create for a working example app using creates.

### Return Types

Each of the 3 types of function expects a certain type of object. As of core `v1.0.11`, there are automated checks to let you know when you're trying to pass the wrong type back. There's more info in each relevant `post_X` section of the [v2 docs](https://zapier.com/developer/documentation/v2/scripting/#available-methods). For reference, each expects:

| Method | Return Type | Notes |
| --- | --- | --- |
| Trigger | Array | 0 or more objects that will be passed to the [deduper](https://zapier.com/developer/documentation/v2/deduplication/) |
| Search | Array | 0 or more objects. If len > 0, put the best match first |
| Action | Object | Can also be [Object] |
