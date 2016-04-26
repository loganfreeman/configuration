connect
---
```js
    // The actual grunt server settings
    connect: {
      options: {
        port: port,
        // Change this to '0.0.0.0' to access the server from outside.
        hostname: 'localhost',
        livereload: 35729
      },
      proxies: [
        {
          context: '/rest',
          host: 'localhost',
          port: 8080,
          https: false,
          xforward: false,
          rewrite: {
            '^/rest': '/changingcontext/rest'
          },
          headers: {
            'x-custom-added-header': 'custom'
          },
          hideHeaders: ['x-removed-header']
        }
      ],
      livereload: {
        options: {
          open: true,
          middleware: function (connect, options) {
            var middlewares = [require('grunt-connect-proxy/lib/utils').proxyRequest, connect.static('.tmp'),
              connect().use(
                '/bower_components',
                connect.static('./bower_components')
              ),
              connect().use(
                '/external_components',
                connect.static('./external_components')
              ),
              connect.static(appConfig.app)]

            return middlewares
          }
        }
      }
    }
```

add the configureProxies task before the connect task

```js
    grunt.task.run([
      'clean:server',
      'wiredep',
      'concurrent:server',
      'autoprefixer',
      'configureProxies',
      'connect:livereload',
      'watch'
    ])
  })
```
