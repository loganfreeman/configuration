```ruby
      # Returns a CSS response that hides all elements on the page and
      # displays the exception
      def css_exception_response(exception)
        message   = "\n#{exception.class.name}: #{exception.message}"
        backtrace = "\n  #{exception.backtrace.first}"

        body = <<-CSS
          html {
            padding: 18px 36px;
          }

          head {
            display: block;
          }

          body {
            margin: 0;
            padding: 0;
          }

          body > * {
            display: none !important;
          }

          head:after, body:before, body:after {
            display: block !important;
          }

          head:after {
            font-family: sans-serif;
            font-size: large;
            font-weight: bold;
            content: "Error compiling CSS asset";
          }

          body:before, body:after {
            font-family: monospace;
            white-space: pre-wrap;
          }

          body:before {
            font-weight: bold;
            content: "#{escape_css_content(message)}";
          }

          body:after {
            content: "#{escape_css_content(backtrace)}";
          }
        CSS

        [ 200, { "Content-Type" => "text/css; charset=utf-8", "Content-Length" => body.bytesize.to_s }, [ body ] ]
      end
```
