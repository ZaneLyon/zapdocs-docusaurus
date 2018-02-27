---
id: errors
title: Handling Errors
sidebar_label: Handling Errors 
---

APIs are not always available. Users do not always input data correctly to
formulate valid requests. Thus, it is a good idea to write apps defensively and
plan for 4xx and 5xx responses from APIs. Without proper handling, errors often
have incomprehensible messages for end users, or possibly go uncaught.

Zapier provides a couple tools to help with error handling. First is the `afterResponse`
middleware ([docs](#using-http-middleware)), which provides a hook for processing
all responses from HTTP calls. The other tool is the collection of errors in
`z.errors` ([docs](#zerrors)), which control the behavior of Zaps when
various kinds of errors occur.

### General Errors

Errors due to a misconfiguration in a user's Zap should be handled in your app
by throwing a standard JavaScript `Error` with a user-friendly message.
Typically, this will be prettifying 4xx responses or API's that return errors as
200s with a payload that describes the error.

Example: `throw new Error('Your error message.');`

A couple best practices to keep in mind:

  * Elaborate on terse messages. "not_authenticated" -> "Your API Key is invalid. Please reconnect your account."
  * If the error calls out a specific field, surface that information to the user. "Invalid Request" -> "contact name is invalid"
  * If the error provides details about why a field is invalid, add that in too! "contact name is invalid" -> "contact name is too long"

Note that if a Zap raises too many error messages it will be automatically
turned off, so only use these if the scenario is truly an error that needs to
be fixed.

### Halting Execution

Any operation can be interrupted or "halted" (not success, not error, but
stopped for some specific reason) with a `HaltedError`. You might find yourself
using this error in cases where a required pre-condition is not met. For instance,
in a create to add an email address to a list where duplicates are not allowed,
you would want to throw a `HaltedError` if the Zap attempted to add a duplicate.
This would indicate failure, but it would be treated as a soft failure.

Unlike throwing `Error`, a Zap will never by turned off when this error is thrown
(even if it is raised more often than not).

Example: `throw new z.errors.HaltedError('Your reason.');`

### Stale Authentication Credentials

For apps that require manual refresh of authorization on a regular basis, Zapier
provides a mechanism to notify users of expired credentials. With the
`ExpiredAuthError`, the current operation is interrupted, the Zap is turned off
(to prevent more calls with expired credentials), and a predefined email is sent
out informing the user to refresh the credentials.

Example: `throw new z.errors.ExpiredAuthError('Your message.');`

For apps that use OAuth2 + refresh or Session Auth, you can use the
`RefreshAuthError`. This will signal Zapier to refresh the credentials and then
repeat the failed operation.

Example: `throw new z.errors.RefreshAuthError();`

