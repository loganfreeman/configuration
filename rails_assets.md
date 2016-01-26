Asset Pipeline
---
> In previous versions of Rails, all assets were located in subdirectories of public such as images, javascripts and stylesheets. With the asset pipeline, the preferred location for these assets is now the app/assets directory. Files in this directory are served by the Sprockets middleware.

> Assets can still be placed in the public hierarchy. Any assets under public will be served as static files by the application or web server when config.serve_static_files is set to true. You should use app/assets for files that must undergo some pre-processing before they are served.

> In production, Rails precompiles these files to public/assets by default. The precompiled copies are then served as static assets by the web server. The files in app/assets are never served directly in production.

> In development mode, or if the asset pipeline is disabled, when these files are requested they are processed by the processors provided by the coffee-script and sass gems and then sent back to the browser as JavaScript and CSS respectively. When asset pipelining is enabled, these files are preprocessed and placed in the public/assets directory for serving by either the Rails app or web server.

In development mode, assets are served as separate files in the order they are specified in the manifest file.

This manifest app/assets/javascripts/application.js:
```
//= require core
//= require projects
//= require tickets
```
would generate this HTML:
```
<script src="/assets/core.js?body=1"></script>
<script src="/assets/projects.js?body=1"></script>
<script src="/assets/tickets.js?body=1"></script>
```

```ruby
RAILS_ENV=production bin/rake assets:precompile
```
The default matcher for compiling files includes application.js, application.css and all non-JS/CSS files (this will include all image assets automatically) from app/assets folders including your gems:
```ruby
[ Proc.new { |filename, path| path =~ /app\/assets/ && !%w(.js .css).include?(File.extname(filename)) },
/application.(css|js)$/ ]
```
