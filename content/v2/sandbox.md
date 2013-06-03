---
title: Sandbox Testing | Gengo API
---

# Sandbox Testing

The sandbox is a testing area for your API code.

## What’s different about the sandbox?
In the sandbox:

* You can add free credits to your account for testing.
* You can trigger the "completion" of your translation jobs (i.e. be a faux translator) so that you can test how your code responds.
* Toggling between job states (for example, from pending to reviewable) _does not_ trigger callbacks. To test callbacks in the sandbox, click the __Send Callback__ button.
* Additional controls are present, allowing you to manually trigger job status changes and other actions, so that you can test your callbacks.

__Important!__ The sandbox jobs database may occasionally be wiped clean to prepare it for new features, so you should not rely on the sandbox for permanent storage.

## How to use the sandbox

1. Read the <a href="/">developer documentation</a>.
2. Create a <a href="http://sandbox.mygengo.com">sandbox account</a>.
3. Log in and visit your Account API Keys page to generate a set of sandbox API keys.
4. Test your app, using http://api.sandbox.mygengo.com/v2/ as the base call URL.
5. Ready to deploy? 
	1. Head  to the live <a href="http://gengo.com">gengo.com</a> site. 
	2. Create a full user account.
	3. Generate a set of live API keys (and remember to paste them into your app!).

