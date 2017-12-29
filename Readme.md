# fastify-bearer-auth

*fastify-bearer-auth* provides a simple request hook for the [Fastify][fastify]
web framework.

[fastify]: https://fastify.io/

## Example

```js
'use strict'

const fastify = require('fastify')
const bearerAuthPlugin = require('fastify-bearer-auth')
const keys = new Set(['a-super-secret-key', 'another-super-secret-key'])

fastify.register(bearerAuthPlugin, {keys})
fastify.get('/foo', (req, reply) => {
  reply({authenticated: true})
})

fastify.listen({port: 8000}, (err) => {
  if (err) {
    fastify.log.error(err.message)
    process.exit(1)
  }
  fastify.log.info('http://127.0.0.1:8000/foo')
})
```

## API

*fastify-bearer-auth* exports a standard [Fastify plugin][fplugin]. This allows
you to register the plugin within scoped paths. Therefore, you could have some
paths that are not protected by the plugin and others that are. See the *Fastify*
documentation and examples for more details.

When registering the plugin you must specify a configuration object that at
least has a `keys` property set to an object that supports a `has(key)` method.
It may also have a method `errorResponse(err)` and property `contentType`. If set,
the `errorResponse(err)` method must synchronously return the content body to be
sent to the client. If the content to be sent is anything other than
`application/json`, then the `contentType` property must be set. The default
config object is:

  ```js
  {
    keys: new Set(),
    contentType: undefined,
    errorResponse: (err) => {
      return {error: err.message}
    }
  }
  ```

Internally, the plugin registers a standard *Fastify* [preHandler hook][prehook]
which will inspect the request's headers for an `authorization` header with the
format `bearer key`. The `key` will be matched against the configured `keys`
object via the `has(key)` method. If the `authorization` header is missing,
malformed, or the `key` does not validate then a 401 response will be sent with
a `{error: message}` body; no further request processing will be performed.

[fplugin]: https://github.com/fastify/fastify/blob/master/docs/Plugins.md
[prehook]: https://github.com/fastify/fastify/blob/master/docs/Hooks.md

## License

[MIT License](http://jsumners.mit-license.org/)
