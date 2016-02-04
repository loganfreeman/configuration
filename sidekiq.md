Usage
---
add to Gemfile
```ruby
gem 'sidekiq'
```
create workder
```ruby
class PygmentsWorker
  include Sidekiq::Worker
  sidekiq_options queue: "high"
  # sidekiq_options retry: false
  
  def perform(snippet_id)
    snippet = Snippet.find(snippet_id)
    uri = URI.parse("http://pygments.appspot.com/")
    request = Net::HTTP.post_form(uri, lang: snippet.language, code: snippet.plain_code)
    snippet.update_attribute(:highlighted_code, request.body)
  end
end
```
use the worker in controller
```ruby
class SnippetsController < ApplicationController
  def show
    @snippet = Snippet.find(params[:id])
  end

  def new
    @snippet = Snippet.new
  end

  def create
    @snippet = Snippet.new(params[:snippet])
    if @snippet.save
      # PygmentsWorker.perform_in(1.hour, @snippet.id)
      PygmentsWorker.perform_async(@snippet.id)
      redirect_to @snippet
    else
      render :new
    end
  end
end
```
