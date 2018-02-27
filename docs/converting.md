---
id: converting
title: Converting a Web Builder App
sidebar_label: Converting a Web Builder App
---

If you have an existing Web Builder app on [Zapier Developer Platform](https://zapier.com/developer/builder/) you can use it as a template to kickstart your local application.

```bash
# Convert an existing Web Builder app to a CLI app in the my-app directory
# App ID 1234 is from URL https://zapier.com/developer/builder/app/1234/development
zapier convert 1234 my-app
```

Your CLI app will be created and you can continue working on it.

> Since v3.3.0, `zapier convert` has been improved a lot. But this is still in an alpha state - you'll likely have to edit the code to make it work.

> Note - there is no way to convert a CLI app to a Web Builder app and we do not plan on implementing this.
