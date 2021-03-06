Generating seeds.rb From Existing Data
---
```ruby
# lib/tasks/export.rake
namespace :export do
  desc "Prints Country.all in a seeds.rb way."
  task :seeds_format => :environment do
    Country.order(:id).all.each do |country|
      puts "Country.create(#{country.serializable_hash.delete_if {|key, value| ['created_at','updated_at','id'].include?(key)}.to_s.gsub(/[{}]/,'')})"
    end
  end
end
```

staget the repository in a local repository
---
```ruby
  desc "Stage the repository in a local directory."
  task :stage => %w[create_stage] do
    Dir.chdir fetch(:rsync_stage) do
      update = %W[git fetch --quiet --all --prune]
      Kernel.system *update

      checkout = %W[git reset --hard origin/#{fetch(:branch)}]
      Kernel.system *checkout
    end
  end
```
yard
---
```ruby
if Gem.loaded_specs['yard']
  require 'yard'
  require 'yard/rake/yardoc_task'

  YARD::Rake::YardocTask.new do |t|
    t.after = ->{ Rake::Task['docs:build'].invoke }
    t.files = ['lib/**/*.rb']
  end
end
```
rake tasks that accept parameters
---
```ruby
  def rails_app_rake(task)
    require 'rails/version'
    system "cd spec/rails/rails-#{Rails::VERSION::STRING}; rake #{task}"
  end

  task :after_setup_hook do
    rails_app_rake "parallel:load_schema"
    rails_app_rake "parallel:create_cucumber_db"
    rails_app_rake "parallel:load_schema_cucumber_db"
  end
```
backup config yml
---
```ruby
require 'zip'
require 'colorize'

namespace :backup do
  desc "backup all config yml files"
  task :config => :environment do
    zipfile_path = Rails.root.join('tmp', 'config_yml.zip')

    File.unlink(zipfile_path) unless !File.exists?(zipfile_path)

    Zip::File.open(zipfile_path, Zip::File::CREATE) do |zipfile|
      Dir[Rails.root.join('config', '*.yml')].each do |f|
        #Make a one line info
        puts "#{f}: #{File.size(f)/1024}KB".green
        zipfile.add(File.basename(f), File.realpath(f))
      end
    end
  end
end

```
