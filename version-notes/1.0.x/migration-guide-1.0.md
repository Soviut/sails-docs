# Migration guide:

## Security

New apps created with Sails 1.0 will contain a **config/security.js** file instead of individual **config/cors.js** and **config/csrf.js** files, but apps migrating from earlier versions don&rsquo;t can keep their existing files as long as they perform the following upgrades:

* Change `module.exports.cors` to `module.exports.security.cors` in `config/cors.js`
* Change CORS config settings names to match the newly documented names in http://sailsjs.com/documentation/reference/configuration/sails-config-security-cors
* Change `module.exports.csrf` to `module.exports.security.csrf` in `config/csrf.js`
* `sails.config.csrf.routesDisabled` is no longer supported -- instead, add `csrf: false` to any route in `config/routes.js` that you wish to be unprotected by CSRF, for example:

```
'POST /some-thing': { action: 'do-a-thing', csrf: false },
```

## Views

For maximum flexibility, Consolidate is no longer bundled within Sails.  If you are using a view engine besides EJS, you'll probably want to install Consolidate as a direct dependency of your app.  Then you can configure the view engine in `config/views.js` like so:

```javascript
'extension': 'swig',
'getRenderFn': function() {
  // Import `consolidate`.
  var cons = require('consolidate');
  // Return the rendering function for Swig.
  return cons.swig;
}
```

Adding custom configuration to your view engine is a lot easier in Sails 1.0:

```javascript
'extension': 'swig',
'getRenderFn': function() {
  // Import `consolidate`.
  var cons = require('consolidate');
  // Import `swig`.
  var swig = require('swig');
  // Configure `swig`.
  swig.setDefaults({tagControls: ['{?', '?}']});
  // Set the module that Consolidate uses for Swig.
  cons.requires.swig = swig;
  // Return the rendering function for Swig.
  return cons.swig;
}
```

## Pubsub

* Removed deprecated `backwardsCompatibilityFor0.9SocketClients` setting.
* Removed deprecated `.subscribers()` method.
* Removed deprecated "firehose" functionality.
* Removed support for 0.9.x socket client API.
* The following resourceful pubsub methods have been removed:
  * `.publishAdd()`
  * `.publishCreate()`
  * `.publishDestroy()`
  * `.publishRemove()`
  * `.publishUpdate()`
  * `.watch()`
  * `.unwatch()`
  * `.message()`
  In their place, you should use the new `.publish()` method, or the low-level [sails.sockets](http://sailsjs.com/documentation/reference/web-sockets/sails-sockets) methods.  Keep in mind that unlike `.message()`, `.publish()` does _not_ wrap your data in an envelope containing the record ID, so you'll need to include that as part of the data if it's important.

## Blueprints


## Express 4
 ### Middleware changes

## Policies

## Miscellaneous deprecated features removed in 1.0

* `req.validate()`
* `sails.config.dontFlattenConfig`
* `sails.getBaseUrl`
* `req.params.all()`

## Globals

## Responses
 * `.jsonx()` is deprecated -- if you haven't customized a response, just delete it.  Otherwise, replace `res.jsonx()` with `res.json()`.