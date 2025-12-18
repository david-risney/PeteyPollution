# Petey Pollution

This was the front end to a CTF challenge I created. I had so much fun writing it, I thought to preserve at least the
client side part here. Its missing the server side component, so to solve just try to get an `alert()` to open from
the page.

# Solution

## Hints

* HTML injection shouldn't be necessary. Notice that the CSP is fairly strict but does allow 'strict-dynamic' scripts.
* The searchParamsToObject method seems overly complex in that it let's you choose sub-properties to fill in. There are some interesting shared properties that all objects have.
* You should read up on the __proto__ property in JavaScript.

## Walkthrough

There shouldn't be a way to inject any HTML or script directly. The CSP is fairly strict including nonces for script. But it
does allow strict-dynamic so if any of the allowed script injects additional script, that additional script doesn't need
the nonce or any other CSP restrictions. And you can see that there is a "debug-only" code block that will inject a script tag.

But just reading that block it looks like the if condition can never be satisfied. There is commented out code that sets up the
object used in the condition, but that's mostly a red herring. Really you need to use the page's `searchParamsToObject` method 
to perform prototype pollution. searchParamsToObject expects to get strings from the query of a URL like 'a.b=value' and it
will create an object with property 'a', that is an object with property 'b' that is a string 'value'. By default JS objects have
a property `__proto__` which is that object's prototype object. In this case that prototype object is Object the root prototype
object for everything. So by doing __proto__.debugScriptSrc.href=... we can add a debugScriptSrc.href property to everything.
Then you can set its value to a data URL that contains JavaScript to run an alert.
