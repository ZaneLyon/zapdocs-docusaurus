---
id: fields
title: Fields
sidebar_label: Fields
---

On each trigger, search, or create in the `operation` directive - you can provide an array of objects as fields under the `inputFields`. Fields are what your users would see in the main Zapier user interface. For example, you might have a "create contact" action with fields like "First name", "Last name", "Email", etc.

You can find more details on each and every field option at [Field Schema](https://zapier.github.io/zapier-platform-schema/build/schema.html#fieldschema).

Those fields have various options you can provide, here is a succinct example:

```javascript
[insert-file:./snippets/fields.js]
```

### Custom/Dynamic Fields

In some cases, it might be necessary to provide fields that are dynamically generated - especially for custom fields. This is a common pattern for CRMs, form software, databases and more. Basically - you can provide a function instead of a field and we'll evaluate that function - merging the dynamic fields with the static fields.

> You should see `bundle.inputData` partially filled in as users provide data - even in field retrieval. This allows you to build hierarchical relationships into fields (EG: only show issues from the previously selected project).

```javascript
[insert-file:./snippets/custom-fields.js]
```

Additionally, if there is a field that affects the generation of dynamic fields, you can set the `altersDynamicFields: true` property. This informs the Zapier UI that whenver the value of that field changes, fields need to be recomputed. An example could be a static dropdown of "dessert type" that will change whether the function that generates dynamic fields includes a field "with sprinkles."

```javascript
[insert-file:./snippets/alters-dynamic-fields.js]
```

### Dynamic Dropdowns

Sometimes, API endpoints require clients to specify a parent object in order to create or access the child resources. Imagine having to specify a company id in order to get a list of employees for that company. Since people don't speak in auto-incremented ID's, it is necessary that Zapier offer a simple way to select that parent using human readable handles.

Our solution is to present users a dropdown that is populated by making a live API call to fetch a list of parent objects. We call these special dropdowns "dynamic dropdowns."

To define one, you can provide the `dynamic` property on your field to specify the trigger that should be used to populate the options for the dropdown. The value for the property is a dot-separated concatenation of a trigger's key, the field to use for the value, and the field to use for the label.

```javascript
[insert-file:./snippets/dynamic-dropdowns.js]
```

In the UI, users will see something like this:

![screenshot of dynamic dropdown in Zap Editor](https://cdn.zapier.com/storage/photos/dd31fa761e0cf9d0abc9b50438f95210.png)

> Dynamic dropdowns are one of the few fields that automatically invalidate Zapier's field cache, so it is not necessary to set `altersDynamicFields` to true for these fields.

### Search-Powered Fields

For fields that take id of another object to create a relationship between the two (EG: a project id for a ticket), you can specify the `search` property on the field to indicate that Zapier needs to prompt the user to setup a Search step to populate the value for this field. Similar to dynamic dropdowns, the value for this property is a dot-separated concatenation of a search's key and the field to use for the value.

```javascript
[insert-file:./snippets/search-field.js]
```

**NOTE:** This has to be combined with the `dynamic` property to give the user a guided experience when setting up a Zap.

If you don't define a trigger for the `dynamic` property, the search connector won't show.

### Computed Fields

In OAuth and Session Auth, you might want to store fields in `bundle.authData` (other than `access_token`, `refresh_token` — for OAuth —, or `sessionKey` — for Session Auth), that you don't want the user to fill in.

For those situations, you need a computed field. It's just like another field, but with a `computed: true` property (don't forget to also make it `required: false`). You can see examples in the [OAuth](#oauth2) or [Session Auth](#session) example sections.
