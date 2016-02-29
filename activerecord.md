order record 
---
```ruby
        # If the records don't already have an order,
        # order them by primary key ascending.
        if !records.respond_to?(:arel) || records.arel.orders.blank?
          records.order("#{records.quoted_table_name}.#{records.quoted_primary_key} ASC")
        end
```
update column name
---
```ruby
  def do_remap(from, to)
    sql = "SELECT table_name, column_name
FROM information_schema.columns
WHERE table_schema='public' and (data_type like 'char%' or data_type like 'text%') and is_updatable = 'YES'"

    cnn = ActiveRecord::Base.connection.raw_connection

    results = cnn.async_exec(sql).to_a

    results.each do |result|
      table_name = result["table_name"]
      column_name = result["column_name"]
      puts "Remapping #{table_name} #{column_name}"
      begin
        result = cnn.async_exec("UPDATE #{table_name}
                        SET #{column_name} = replace(#{column_name}, $1, $2)
                        WHERE NOT #{column_name} IS NULL
                          AND #{column_name} <> replace(#{column_name}, $1, $2)", [from, to])
        puts "#{result.cmd_tuples} rows affected!"
      rescue => ex
        puts "Error: #{ex}"
      end
    end
  end
 ```
