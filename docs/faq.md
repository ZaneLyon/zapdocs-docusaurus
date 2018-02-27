---
id: faq
title: FAQs
sidebar_label: FAQs
---

### Why doesn't Zapier support newer versions of Node.js?

We run your code on AWS Lambda, which only supports a few [versions](https://docs.aws.amazon.com/lambda/latest/dg/current-supported-versions.html) of Node (the latest of which is `LAMBDA_VERSION`. As that updates, so too will we.


### Does Zapier support XML (SOAP) APIs?

Not natively, but it can! Users have reported that the following `npm` modules are compatible with the CLI Platform:

* [pixl-xml](https://github.com/jhuckaby/pixl-xml)
* [xml2js](https://github.com/Leonidas-from-XIV/node-xml2js)
* [fast-xml-parser](https://github.com/NaturalIntelligence/fast-xml-parser)

```javascript
[insert-file:./snippets/xml.js]
```

### Is it possible to iterate over pages in a polling trigger?

Yes, though there are caveats. Your entire function only gets 30 seconds to run. HTTP requests are costly, so paging through a list may time out (which you should avoid at all costs).

```javascript
[insert-file:./snippets/paging-poll.js]
```

If you need to do more requests conditionally based on the results of an HTTP call (such as getting "next url" param, using `async/await` with a transpiler is the way to go. If you go this route, only page as far as you need to. Keep an eye on the polling [guidelines](https://zapier.com/developer/documentation/v2/deduplication/), namely the part about only iterating until you hit items that have probably been seen in a previous poll.

```javascript
[insert-file:./snippets/async-polling.js]
```

### How do I manually set the Node.js version to run my app with?

Update your `zapier-platform-core` dependency in `package.json`.  Each major version ties to a specific version of Node.js. You can find the mapping [here](https://github.com/zapier/zapier-platform-cli/blob/master/src/version-store.js). We only support the version(s) supported by [AWS Lambda](https://docs.aws.amazon.com/lambda/latest/dg/current-supported-versions.html).

### How do search-powered fields relate to dynamic dropdowns and why are they both required together?

To understand search-powered fields, we have to have a good understanding of dynamic dropdowns.

When users are selecting specific resources (for instance, a Google Sheet), it's important they're able to select the exact sheet they want. Instead of referencing the sheet by name (which may change), we match via `id` instead. Rather than directing the user copy and paste an id for every item they might encounter, there is the notion of a **dynamic dropdown**. A dropdown is a trigger that returns a list of resources. It can pull double duty and use its results to power another trigger, search, or action in the same app.  It provides a list of ids with labels that show the item's name:

![](https://cdn.zapier.com/storage/photos/fb56bdc2aab91504be0e51800bec4d64.png)

The field's value reaches your app as an id. You define this connection with the `dynamic` property, which is a string: `trigger_key.id_key.label_key`. This approach works great if the user setting up the Zap always wants the Zap to use the same spreadsheet. They specify the id during setup and the Zap runs happily.

**Search fields** take this connection a step further. Rather than set the spreadsheet id at setup, the user could precede the action with a search field to make the id dynamic. For instance, let's say you have a different spreadsheet for every day of the week. You could build the following zap:

1. Some Trigger
2. Calculate what day of the week it is today (Code)
3. Find the spreadsheet that matches the day from Step 2
4. Update the spreadsheet (with the id from step 3) with some data

If the connection between steps 3 and 4 is a common one, you can indicate that in your field by specifying `search` as a `search_key.id_key`. When paired **with a dynamic dropdown**, this will add a button to the editor that will add the search step to the user's Zap and map the id field correctly.

![](https://cdn.zapier.com/storage/photos/d263fd3a56cf8108cb89195163e7c9aa.png)

This is paired most often with "update" actions, where a required parameter will be a resource id.

<a id="paging"></a>
### What's the deal with pagination? When is it used and how does it work?

Paging is **only used when a trigger is part of a dynamic dropdown**. Depending on how many items exist and how many are returned in the first poll, it's possible that the resource the user is looking for isn't in the initial poll. If they hit the "see more" button, we'll increment the value of `bundle.meta.page` and poll again.

Paging is a lot like a regular trigger except the range of items returned is dynamic. The most common example of this is when you can pass a `start` parameter:

```javascript
const getList = (z, bundle) => {
  const promise = z.request({
    url: 'http://example.com/api/list.json',
    params: {
      limit: 100,
      offset: 100 * bundle.meta.page
    }
  });
  return promise.then((response) => response.json);
};
```

Lastly, you need to set `canPaginate` to `true` in your polling definition (per the [schema](https://github.com/zapier/zapier-platform-schema/blob/master/docs/build/schema.md#basicpollingoperationschema)).

<a id="dedup"></a>
### How does deduplication work?

Each time a polling Zap runs, Zapier needs to decide which of the items in the response should trigger the zap. To do this, we compare the `id`s to all those we've seen before, trigger on new objects, and update the list of seen `id`s. When a Zap is turned on, we initialize the list of seen `id`s with a single poll. When it's turned off, we clear that list. For this reason, it's important that calls to a polling endpoint always return the newest items.

For example, the initial poll returns objects 4, 5, and 6 (where a higher `id` is newer). If a later poll increases the limit and returns objects 1-6, then 1, 2, and 3 will be (incorrectly) treated like new objects.

There's a more in-depth explanation [here](https://zapier.com/developer/documentation/v2/deduplication/).

### Why are my triggers complaining if I don't provide an explicit `id` field? I didn't have to do that in the Web Builder!

For deduplication to work, we need to be able to identify and use a unique field. For WB apps, we guessed if `id` wasn't present. In order to ensure we don't guess wrong, we now require that the developers send us an `id` field. If your objects have a differently-named unique field, feel free to adapt this snippet and ensure this test passes:

```js
// ...
let items = z.JSON.parse(response.content).items;
items.forEach(item => {
  item.id = item.contactId;
})

return items;
```

