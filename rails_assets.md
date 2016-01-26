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
If you have other manifests or individual stylesheets and JavaScript files to include, you can add them to the precompile array in config/initializers/assets.rb:
```ruby
Rails.application.config.assets.precompile += ['admin.js', 'admin.css', 'swfObject.js']
```
#### Far-future Expires Header
Precompiled assets exist on the file system and are served directly by your web server. They do not have far-future headers by default, so to get the benefit of fingerprinting you'll have to update your server configuration to add those headers.
For Apache
```
# The Expires* directives requires the Apache module
# `mod_expires` to be enabled.
<Location /assets/>
  # Use of ETag is discouraged when Last-Modified is present
  Header unset ETag
  FileETag None
  # RFC says only cache for 1 year
  ExpiresActive On
  ExpiresDefault "access plus 1 year"
</Location>
```
For nginx
```
location ~ ^/assets/ {
  expires 1y;
  add_header Cache-Control public;
 
  add_header ETag "";
  break;
}
```
