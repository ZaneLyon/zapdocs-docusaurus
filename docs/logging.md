---
id: logging
title: Logging
sidebar_label: Logging
---

There are two types of logs for a Zapier app, console logs and HTTP logs. The console logs are created by your app through the use of the `z.console.log` method ([see below for details](#console-logging)). The HTTP logs are created automatically by Zapier whenever your app makes HTTP requests (as long as you use `z.request([url], options)` or shorthand request objects).

To view the logs for your application, use the `zapier logs` command. There are two types of logs, `http` (logged automatically by Zapier on HTTP requests) and `console` (manual logs via `z.console.log()` statements).

For advanced logging options including only displaying the logs for a certain user or app version, look at the help for the logs command:

```bash
zapier help logs
```

### Console Logging

To manually print a log statement in your code, use `z.console.log`:

```javascript
z.console.log('Here are the input fields', bundle.inputData);
```

The `z.console` object has all the same methods and works just like the Node.js [`Console`](https://nodejs.org/docs/latest-v6.x/api/console.html) class - the only difference is we'll log to our distributed datastore and you can view them via `zapier logs` (more below).

### Viewing Console Logs

To see your `z.console.log` logs, do:

```bash
zapier logs --type=console
```

### HTTP Logging

If you are using the `z.request()` shortcut that we provide - HTTP logging is handled automatically for you. For example:

```javascript
z.request('http://57b20fb546b57d1100a3c405.mockapi.io/api/recipes')
  .then((res) => {
    // do whatever you like, this request is already getting logged! :-D
    return res;
  })
```

### Viewing HTTP Logs

To see the HTTP logs, do:

```bash
zapier logs --type=http
```
To see detailed http logs including headers, request and response bodies, etc, do:

```bash
zapier logs --type=http --detailed
```
