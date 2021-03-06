Migration
---
```ruby
class CreateSettings < ActiveRecord::Migration
  def self.up
    create_table :settings, force: true do |t|
      t.string :name, limit: 32, null: false, default: ""
      t.text :value
      t.text :default_value
      t.timestamps
    end
    add_index :settings, :name
  end

  def self.down
    drop_table :settings
  end
end
```
Setting class
---
```ruby
class Setting < ActiveRecord::Base
  serialize :value

  # Use class variables for cache and yaml settings.
  cattr_accessor :cache, :yaml_settings
  @@cache = @@yaml_settings = {}.with_indifferent_access

  class << self
    # Cache should be cleared before each request.
    def clear_cache!
      @@cache = {}.with_indifferent_access
    end

    #-------------------------------------------------------------------
    def method_missing(method, *args)
      super
    rescue NoMethodError
      method_name = method.to_s
      if method_name.last == "="
        self[method_name.sub("=", "")] = args.first
      else
        self[method_name]
      end
    end

    # Get setting value (from database or loaded YAML files)
    #-------------------------------------------------------------------
    def [](name)
      # Return value if cached
      return cache[name] if cache.key?(name)
      # Check database
      if database_and_table_exists?
        if setting = find_by_name(name.to_s)
          unless setting.value.nil?
            return cache[name] = setting.value
          end
        end
      end
      # Check YAML settings
      if yaml_settings.key?(name)
        return cache[name] = yaml_settings[name]
      end
    end

    # Set setting value
    #-------------------------------------------------------------------
    def []=(name, value)
      return nil unless database_and_table_exists?
      setting = find_by_name(name.to_s) || new(name: name)
      setting.value = value
      setting.save
      cache[name] = value
    end

    # Unrolls [ :one, :two ] settings array into [[ "One", :one ], [ "Two", :two ]]
    # picking symbol translations from locale. If setting is not a symbol but
    # string it gets copied without translation.
    #-------------------------------------------------------------------
    def unroll(setting)
      send(setting).map { |key| [key.is_a?(Symbol) ? I18n.t(key) : key, key.to_sym] }
    end

    def database_and_table_exists?
      # Returns false if table or database is unavailable.
      # Catches all database-related errors, so that Setting will return nil
      # instead of crashing the entire application.
      table_exists? rescue false
    end

    # Loads settings from YAML files
    def load_settings_from_yaml(file)
      settings = YAML.load(ERB.new(File.read(file)).result)
      @@yaml_settings.deep_merge!(settings)
    end
  end

  ActiveSupport.run_load_hooks(:fat_free_crm_setting, self)
end
```
User Preference
---
```ruby
class Preference < ActiveRecord::Base
  belongs_to :user

  #-------------------------------------------------------------------
  def [](name)
    # Following line is to preserve AR relationships
    return super(name) if name.to_s == "user_id" # get the value of belongs_to

    return cached_prefs[name.to_s] if cached_prefs.key?(name.to_s)
    cached_prefs[name.to_s] = if user.present? && pref = Preference.find_by_name_and_user_id(name.to_s, user.id)
                                Marshal.load(Base64.decode64(pref.value))
    end
  end

  #-------------------------------------------------------------------
  def []=(name, value)
    return super(name, value) if name.to_s == "user_id" # set the value of belongs_to

    encoded = Base64.encode64(Marshal.dump(value))
    if pref = Preference.find_by(name: name.to_s, user_id: user.id)
      pref.update_attribute(:value, encoded)
    else
      Preference.create(user: user, name: name.to_s, value: encoded)
    end
    cached_prefs[name.to_s] = value
  end

  def cached_prefs
    @cached_prefs ||= {}
  end

  ActiveSupport.run_load_hooks(:fat_free_crm_preference, self)
end
```
