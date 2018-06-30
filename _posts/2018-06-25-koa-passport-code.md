---
title: koa-passport code inside 
date: 2018-06-25 09:00
categories: blog
tags: [nodejs, web]
---

```js
//project router index.js:entrance of the passport lib
router.post('/auth/login', async (ctx) => {
    //[1]prepare the function 
    return passport.authenticate('local', (err, user, info, status) => {
    //...
    //[2]authenticate implication
    })(ctx);
}
```

### [1]prepare the function
```js
//passport/lib/authenticator.js:136
/**
 * Middleware that will authenticate a request using the given `strategy` name,
 * with optional `options` and `callback`.
 *
 * Examples:
 *
 *     passport.authenticate('local', { successRedirect: '/', failureRedirect: '/login' })(req, res);
 *
 *     passport.authenticate('local', function(err, user) {
 *       if (!user) { return res.redirect('/login'); }
 *       res.end('Authenticated!');
 *     })(req, res);
 *
 *     passport.authenticate('basic', { session: false })(req, res);
 *
 *     app.get('/auth/twitter', passport.authenticate('twitter'), function(req, res) {
 *       // request will be redirected to Twitter
 *     });
 *     app.get('/auth/twitter/callback', passport.authenticate('twitter'), function(req, res) {
 *       res.json(req.user);
 *     });
 *
 * @param {String} strategy='local'
 * @param {Object} options
 * @param {Function} callback
 * @return {Function} middleware
 * @api public
 */
Authenticator.prototype.authenticate = function(strategy, options, callback) {
  return this._framework.authenticate(this, strategy, options, callback);
};
//koa-passport/lib/framework/koa.js:65
/**
 * Passport's authenticate middleware for Koa.
 *
 * @param {String|Array} name
 * @param {Object} options
 * @param {GeneratorFunction} callback
 * @return {GeneratorFunction}
 * @api private
 */
//|--------------Authenticator,local,function,undefinde
function authenticate(passport, name, options, callback) {
  // normalize arguments
  if (typeof options === 'function') {
    callback = options
    options  = {}
  }
  options = options || {}

  if (callback) {
    // When the callback is set, neither `next`, `res.redirect` or `res.end`
    // are called. That is, a workaround to catch the `callback` is required.
    // The `passportAuthenticate()` method below will therefore set
    // `callback.resolve` and `callback.reject`. Then, once the authentication
    // finishes, the modified callback calls the original one and afterwards
    // triggers either `callback.resolve` or `callback.reject` to inform
    // `passportAuthenticate()` that we are ready.
    const _callback = callback
    callback = function(err, user, info, status) {
      try {
        Promise.resolve(_callback(err, user, info, status))
               .then(() => callback.resolve(false))
               .catch(err => callback.reject(err))
      } catch (err) {
        callback.reject(err)
      }
    }
  }
  //const _authenticate = require('passport/lib/middleware/authenticate')
  const middleware = promisify(_authenticate(passport, name, options, callback))

}
```
### [2]authenticate implication
```js
//koa-passport/lib/framework/koa.js:105
function authenticate(passport, name, options, callback) {
    //...
    return function passportAuthenticate(ctx, next) {
        const p = new Promise((resolve, reject) => {
            // call the connect middleware
            middleware(req, res).then(resolve, reject)
        })
    }
}
////passport/lib/middleware/authenticate.js:94
module.exports = function authenticate(passport, name, options, callback) {
    //...
    return function authenticate(req, res, next) {

        (function attempt(i) {
            var layer = name[i];
            // If no more strategies exist in the chain, authentication has failed.
            if (!layer) { return allFailed(); }
    
            // Get the strategy, which will be used as prototype from which to create
            // a new instance.  Action functions will then be bound to the strategy
            // within the context of the HTTP request/response pair.
            var prototype = passport._strategy(layer);
    
            var strategy = Object.create(prototype);

            strategy.success = function(user, info) {
            }
            strategy.fail = function(challenge, status) {
            }
            strategy.redirect = function(url, status) {
            }
            strategy.pass = function() {
                next();
            };
            strategy.error = function(err) {
                if (callback) {return callback(err);}
                next(err);
            };
            // ----- END STRATEGY AUGMENTATION -----
            //[3]
            strategy.authenticate(req, options);
        })(0); // attempt
    }

}
```

### [3]strategy.authenticate
```js
//passport-local/lib/strategy.js:70
/**
 * Authenticate request based on the contents of a form submission.
 *
 * @param {Object} req
 * @api protected
 */
Strategy.prototype.authenticate = function(req, options) {
}

```