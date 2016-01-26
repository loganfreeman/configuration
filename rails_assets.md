Asset Pipeline
---
> In previous versions of Rails, all assets were located in subdirectories of public such as images, javascripts and stylesheets. With the asset pipeline, the preferred location for these assets is now the app/assets directory. Files in this directory are served by the Sprockets middleware.

> Assets can still be placed in the public hierarchy. Any assets under public will be served as static files by the application or web server when config.serve_static_files is set to true. You should use app/assets for files that must undergo some pre-processing before they are served.

> In production, Rails precompiles these files to public/assets by default. The precompiled copies are then served as static assets by the web server. The files in app/assets are never served directly in production.

> In development mode, or if the asset pipeline is disabled, when these files are requested they are processed by the processors provided by the coffee-script and sass gems and then sent back to the browser as JavaScript and CSS respectively. When asset pipelining is enabled, these files are preprocessed and placed in the public/assets directory for serving by either the Rails app or web server.
