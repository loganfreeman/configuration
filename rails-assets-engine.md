register custom engine
---
```ruby
module MithrilRails
  module Rails
    class Engine < ::Rails::Engine
      initializer "mithril_rails.setup_engine", :group => :all do |app|
        app.assets.register_engine '.msx', MithrilRails::MSX::Template
      end
    end
  end
end
```

define the custom template engine
---
```
require 'tilt'
require 'rails'
module MithrilRails
  module MSX

    class Template < Tilt::Template
      self.default_mime_type = 'application/javascript'

      def prepare
      end

      def evaluate(scope, locals, &block)
        @output ||= MSX::transform(data)
      end
    end
  end
end
```

Tranformer
---
```ruby
require 'execjs'
require 'mithril_rails/msx/template'
require 'rails'

module MithrilRails
  module MSX
    def self.context
      # lazily loaded during first request and reloaded every time when in dev or test
      unless @context && ::Rails.env.production?
        contents =
          # If execjs uses therubyracer, there is no 'global'. Make sure
          # we have it so JSX script can work properly.
          'var global = global || this;' +

          # search for transformer file using sprockets - allows user to override
          # this file in his own application
          File.read(::Rails.application.assets.resolve('MSXTransformer.js'))

        @context = ExecJS.compile(contents)
      end

      @context
    end

    def self.transform(code)
      result = context.call('JSXTransformer.transform', code)
      return result['code']
    end

  end
end
```
sass
---
```ruby
      config.assets.configure do |env|
        env.register_transformer 'text/sass', 'text/css',
          Sprockets::SassProcessor.new(importer: SassImporter, sass_config: app.config.sass)
        env.register_transformer 'text/scss', 'text/css',
          Sprockets::ScssProcessor.new(importer: SassImporter, sass_config: app.config.sass)
      end
```
