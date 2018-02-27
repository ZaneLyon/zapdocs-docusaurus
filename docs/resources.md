---
id: resources
title: Resources
sidebar_label: Resources
---

A `resource` is a representation (as a JavaScript object) of one of the REST resources of your API. Say you have a `/recipes`
endpoint for working with recipes; you can define a recipe resource in your app that will tell Zapier how to do create,
read, and search operations on that resource.

```javascript
[insert-file:./snippets/resources.js]
```

The quickest way to create a resource is with the `zapier scaffold` command:

```bash
zapier scaffold resource "Recipe"
```

This will generate the resource file and add the necessary statements to the `index.js` file to import it.


### Resource Definition

A resource has a few basic properties. The first is the `key`, which allows Zapier to identify the resource on our backend.
The second is the `noun`, the user-friendly name of the resource that is presented to users throughout the Zapier UI.

> Example App: check out https://github.com/zapier/zapier-platform-example-app-resource for a working example app using resources.

After those, there is a set of optional properties that tell Zapier what methods can be performed on the resource.
The complete list of available methods can be found in the [Resource Schema Docs](https://zapier.github.io/zapier-platform-schema/build/schema.html#resourceschema).
For now, let's focus on two:

 * `list` - Tells Zapier how to fetch a set of this resource. This becomes a Trigger in the Zapier Editor.
 * `create` - Tells Zapier how to create a new instance of the resource. This becomes an Action in the Zapier Editor.

Here is a complete example of what the list method might look like

```javascript
[insert-file:./snippets/recipe-list.js]
```

The method is made up of two properties, a `display` and an `operation`. The `display` property ([schema](https://zapier.github.io/zapier-platform-schema/build/schema.html#basicdisplayschema)) holds the info needed to present the method as an available Trigger in the Zapier Editor. The `operation` ([schema](https://zapier.github.io/zapier-platform-schema/build/schema.html#resourceschema)) provides the implementation to make the API call.

Adding a create method looks very similar.

```javascript
[insert-file:./snippets/recipe-create.js]
```

Every method you define on a `resource` Zapier converts to the appropriate Trigger, Create, or Search. Our examples
above would result in an app with a New Recipe Trigger and an Add Recipe Create.

Note the keys for the Trigger, Create, Search, and Search or Create are automatically generated (in case you want to use them in a dynamic dropdown), like: `{resourceName}List`, `{resourceName}Create`, `{resourceName}Search`, and `{resourceName}SearchOrCreate`; in the examples above, `{resourceName}` would be `recipe`.
