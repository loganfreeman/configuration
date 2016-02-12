Refactoring spec
---
A typical antipattern is creating fat models with a lot of business logic and then wondering why tests are slow. It's because you are testing things which don't need to be tested (e.g. ActiveRecord) but mocked, and you're needlessly hitting the database.

Let's show this using a simple example:
```ruby
class PriceList < ActiveRecord::Base
  has_many :products

  def total_price
    products.sum(&:price)
  end
end
require 'spec_helper'

describe PriceList do

  let(:price_list){ create(:price_list) }

  describe '#total_price' do
    before do
      create(:product, price_list: price_list, price: 100)
      create(:product, price_list: price_list, price: 200)
    end
    it { expect(price_list.total_price).to eq(300) }
  end
end
```
Now let's try to refactor our model and extract that #total_price functionality to a separate class so we can test it in isolation:
```ruby
class PriceSheet
  def total_price(items)
    items.map(&:price).inject(:+)
  end
end
class PriceList < ActiveRecord::Base
  has_many :products

  def total_price
    PriceSheet.new.total_price(products)
  end
end
require File.expand_path("../../../lib/price_sheet.rb", __FILE__)

describe PriceSheet do

  describe '#total_price' do
    let(:item_one) { double(price: 200) }
    let(:item_two) { double(price: 100) }
    it { expect(PriceSheet.new.total_price([item_one, item_two])).to eq(300) }
  end
end
```
Test Doubles and Method Stubs
---
A test double is an object that stands in for another object in your system during a code example. Use the double method, passing in an optional identifier, to create one:
```ruby
book = double("book")
book = double("book", :title => "The RSpec Book")
```
A method stub is an implementation that returns a pre-determined value. Method stubs can be declared on test doubles or real objects using the same syntax. rspec-mocks supports 3 forms for declaring method stubs:
```ruby
allow(book).to receive(:title) { "The RSpec Book" }
allow(book).to receive(:title).and_return("The RSpec Book")
allow(book).to receive_messages(
    :title => "The RSpec Book",
    :subtitle => "Behaviour-Driven Development with RSpec, Cucumber, and Friends")
```
before vs let
---
Let variables don't exist until called into existence by the actual tests, so you won't waste time loading them for examples that don't use them. They're also memoized, so they're useful for encapsulating database objects, due to the cost of making a database request.
```ruby
let(:valid_user) { User.find_by_email(email) } # Let queries the database once, and then saves the valid_user object locally

before { @valid_user = User.find_by_email(email) } # Before queries the database before each spec. 
```
Test connection
---
```ruby
ActiveRecord::Base.establish_connection :adapter => 'sqlite3', :database => ':memory:'
```
