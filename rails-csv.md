export csv
---
```ruby
require "csv"

module FatFreeCRM
  class ExportCSV
    # CSV export. Based on to_csv Rails plugin by Ary Djmal
    # https://github.com/arydjmal/to_csv
    #----------------------------------------------------------------------------
    def self.from_array(items = [])
      return '' if items.empty?
      # Infer column types from the first item in the array
      klass = items.first.class
      columns = klass.columns.map(&:name).reject { |column| column =~ /password|token/ }
      columns << 'tags' if klass.taggable?
      CSV.generate do |csv|
        csv << columns.map { |column| klass.human_attribute_name(column) }
        items.each do |item|
          csv << columns.map do |column|
            if column == 'tags'
              item.tag_list.join(' ')
            else
              item.send(column)
            end
          end
        end
      end
    end
  end
end
```
csv renderer
---
```ruby
ActionController::Renderers.add :csv do |objects, options|
  filename = options[:filename] || controller_name || 'data'
  str = FatFreeCRM::ExportCSV.from_array(objects)
  send_data str, type: :csv,
                 disposition: "attachment; filename=#{filename}.csv"
end
```
