# express-extendcsp

Express middleware for altering the Content-Security-Policy on the fly.
Mostly useful in development when you want to make sure that your
debugging and stylesheet injection techniques work, but without
compromsing the CSP you're running in production.

Examples I've seen so far:

* Webpack dev servers need `connect-src ws://*` to work in all scenarios
* Several CSS loaders need `style-src blob:` or `style-src data:` in
  development to be able to inject a stylesheet with proper source
  mapping in spite of
  https://bugs.chromium.org/p/chromium/issues/detail?id=649679#c19


### Usage

Put express-extendcsp in your middleware stack before the middleware
that sets your CSP, and pass it a config object with an `add` key,
specifying which directives to add to the `Content-Security-Policy`
header:

```js
require('express')()
    .use(require('express-extendcsp')({
        add: {
            connectSrc: 'ws://*',
            styleSrc: ['blob:', 'data:']
        }
    }))
    .use((req, res, next) => {
        res.setHeader(
            'Content-Security-Policy',
            "default-src 'self'; object-src 'none'"
        );
    });
```

Both camelCased and snake-cased directive names are supported, and you
can supply the tokens to add as either a string or an array of strings.
