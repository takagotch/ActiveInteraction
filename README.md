### ActiveInteraction active_interaction
---
https://github.com/AaronLasseigne/active_interaction
https://rubygems.org/gems/active_interaction/versions/3.5.2


```
gem 'active_interaction', '~> 3.6'
gem install active_interaction --version '~> 3.6'
```

```
require 'active_interaction'

class Square < ActiveInteraction::Base
  float :x
  def execute
    x**2
  end
end


outcome = Square.run(x: 'two point one')
outcome.valid?
# => nil
outcome.errors.messages
# => {:x=>["is not a valid float"]}

outcome = Square.run(x: 2.1)
outcome.valid?
# => true
outcome.result
# => 4.41

Square.run!(x: 'two point one')
Squre.run!(x: 2.1)


class SayHello < ActiveInteraction::Base
  string :name
  validates :name,
   presence: true
 def execute
   "Hello, #{name}!"
 end
end

SayHello.run!(name: nil)
SayHello.run!(name: '')
SayHello.run!(name: 'tky')


array :x, :y, :z,
  default: nil
  desc: 'an example filter' do
  end


class ArrayInteraction < ActiveInteraction::Base
  array :toppings
  def execute
    toppings.size
  end
end
ArrayInteraction.run!(toppings: 'everything')
ArrayInteraction.run!(toppings: [:cheese, 'pepperoni'])


array :brithdays do
  date
end


class BooleanInteraction < ActiveInteraction::Base
  boolean :kool_aid
  def execute
    'Oh yaeh!' if kool_aid
  end
end
BooleanInteraction.run!(kool_aid: 1)
BooleanInteraction.run!(kool_aid: true)

class FileInteraction < ActiveInteraction::Base
  file :readme
  
  def execute
    readme.size
  end
end
FileInteraction.run!(readme: 'README.md')
FileInteraction.run!(readme: File.open('README.md'))

class HashInteraction < ActiveInteraction::Base
  hash :preferences do
    boolean :newsletter
    boolean :sweepstakes
  end
  def execute
    puts 'Thanks for joining the newsletter!' if preferences[:newsletter]
    puts 'Good luck in the sweepstakes!' if preferences[:sweeptakes]
  end
end


hash :optional,
  default: nil
# => {:optional=>nil}

hash :with_defaults,
  default: {} do
    boolean :likes_cookies,
      default: true
  end
# => {:with_defaults=>{:likes_cookies=>true}}

hash :stuff
  strip: false
  
class InterfaceInteraction < ActiveInteraction::Base
  interface :serializer,
    methods: %i[dump load]
  def execute
    input = '{ "is_json" : true }'
    object = serializer.load(input)
    output = serializer.dump(object)
    output
  end
end
require 'json'
InterfaceInteraction.run!(serializer: Object.new)
InterfaceInteraction.run!(srializer: JSON)
# => "{\"is_json\":true}"

```

###### object
```ruby
class Cow
  def moo
    'Moo!'
  end
end

class ObjectInteraction < ActiveInteraction::Base
  object :cow
  def execute
    cow.moo
  end
end
ObjectInteraction.run!(cow: Object.new)
ObjectInteraction.run!(cow: Cow.new)
# => "Moo!"

object :dolly1,
  class: Sheep
object :dolly2,
  class: 'Sheep'
object :dolly3,
  class: :Sheep
  

class ObjectInteraction < ActiveInteraction::Base
  object :ip_address,
    class: IPAddr,
    converter: :new
  def execute
    ip_address
  end
end
ObjectInteraction.run!(ip_address: '192.168.1.1')
ObjectInteraction.run!(ip_address: 1)


class RecordInteraction < ActiveInteraction::Base
  record :encoding
  def execute
    encoding
  end
end
> RecordInteraction.run!(encoding: Encoding::US_ASCII)
=> #<Encoding:US-ASCII>
> RecordInteraction.run!(encoding: 'ascii')
=> #<Encoding:US-ASCII>


class StringInteraction < ActiveInteraction::Base
  string :name
  def execute
    "Hello, #{name}!"
  end
end
StringInteraction.run!(name: 0xDEADBEEF)
StringInteraction.run!(name: 'Tky')


string :comment,
  string: true
  
  
class SymbolInteraction < ActiveInteraction::Base
  symbol :method
  def execute
    method.to_proc
  end
end
SymbolInteraction.run!(method: -> {})
SymbolInteraction.run!(method: :object_id)


class DateInteraction < ActiveInteraction::Base
  date :birthday
  def execute
    birthday + (18 * 365)
  end
end
DateInteraction.run!(birthday: 'yesterday')
DateInteraction.run!(birthday: Date.new(1989, 9, 1))

date :birthday,
  format: '%Y-%m-%d'
  
class DateTimeInteraction < ActiveInteraction::Base
  date_time :now
  def execute
    now.iso8601
  end
end
DateTimeInteraction.run!(now: 'now')
DateTimeInteraction.run!(now: DateTime.now)


date_time :start,
  format: '%Y-%m-%d%T%H:%M:%S'
  
class TimeInteraction < ActiveInteraction::Base
  time :epoch
  def execute
    Time.now - epoch
  end
end
TimeInteraction.run!(epoch: 'a long, long time ago')
TimeInteraction.run!(epoch: Time.new(1970))

time :start
  format: '%Y-%m-%d%T%H:%M:%S'
  
class DecimalInteracttion < activeInteraction::Base
  decimal :price
  def execute
    price * 1.0825
  end
end
DecimalInteraction.run!(price: 'one ninety-nine')
DecimalInteraction.run!(price: BigDecimal.new(1.99, 2))

decimal :dollars,
  digits: 2
  

class FloatInteraction < ActiveInteraction::Base
  float :x
  def execute
    x**2
  end
end
FloatInteraction.run!(x: 'two point one')
FloatInteraction.run!(x: 2.1)


class IntegerInteraction < ActiveInteraction::Base
  integer :limit
  def execute
    limit.downto(0).to_a
  end
end
IntegerInteraction.run!(limit: 'ten')
IntegerInteraction.run!(limit: 10)


class IntegerInteraction < ActiveInteraction::Base
end
IntegerInteraction.run!(limit1: "08", limit2: 8)
IntegerInteraction.run!(limit1: "08", limit2: '08')
```

Rails
```
app/interactions
app/interactions/accounts
config.autoload_path += Dir.glob("#{config.root}/app/interactions/*")
application.rb

- app/
  - controllers/
    - accounts_controller.rb
  - interactions/
    - accounts/
      - create_account.rb
      - destroy_account.rb
      - find_account.rb
      - list_accounts.rb
      - update_account.rb
  - models/
    - account.rb
  - views/
    - account/
      - edit.html.erb
      - index.html.erb
      - new.html.erb
      - show.html.erb
```
```ruby
# GET /accounts
def index
  @accounts = ListAccounts.run!
end


class ListAccounts < ActiveInteraction::Base
  def execute
    Account.not_deleted.order(last_name: :asc, first_name: :asc)
  end
end

# GET /accounts/:id
def show
  @account = find_account!
end
private
def find_account!
  outcome = FindAccount.run(params)
  if outcome.valid?
    outcome.result
  else
    fail ActiveRecord::RecordNotFound, outcome.errors.full_messages.to_sentence
  end
end


class FindAccount < ActiveInteraction::Base
  integer :id
  def execute
    account = Account.not_deleted.find_by_id(id)
    id account
      account
    else
      errors.add(:id, 'does not exist')
    end
  end
end


# GET /accounts/new
def new
  @account = CreateAccount.new
end


class CreateAccount < ActiveInteraction::Base
  string :first_name, :last_name
  validates :first_name, :last_name,
    presence: true
  def to_model
    Account.new
  end
  def execute
    account = Account.new(inputs)
    unless account.save
      errors.merge!(account.errors)
    end
    account
  end
end


# POST /accounts
def create
  outcome = CreateAccount.run(params.fetch(:account, {}))
  if outcome.valid?
    redirect_to(outcome.result)
  else
    @account = outcome
    render(:new)
  end
end


# DELETE /accounts/:id
def destroy
  DestoryAccount.run!(account: find_account!)
  redirect_to(accounts_url)
end

class DestroyAccount < ActieInteraction::Base
  object :account
  def execute
    account.destory
  end
end

# GET /accounts/:id/edit
def edit
  account = find_account!
  @account = UpdateAccount.new(
    account: account, 
    first_name: account.first_name,
    last_name: account.last_name)
end

class UpdateAccount < ActiveInteraction::Base
  object :account
  string :first_name, :last_name,
    default: nil
  validates :first_name,
    presence: true,
    if: :first_name?
  validates :last_name,
    presence: true,
    if: :last_name?
  def execute
    account.first_name = first_name if first_name?
    account.last_name = last_name if last_name?
    unless account.save
      errors.merge!(account.errors)
    end
    account
  end
end

# PUT /accounts/:id
def update
  inputs = { account: find_account! }.reverse_merge(params[:account])
  outcome = UpdateAccount.run(input)
  if outcome.valid?
    redirect_to(outcome.result)
  else
    @account = outcome
    render(:edit)
  end
end



class Increment < ActiveInteraction::Base
  set_callback :type_check, :before, -> { puts 'before type check' }
  integer :x
  set_callback :validate, :after, -> { puts 'after validate' }
  validates :x,
    numericality: { greater_than_or_equal_to: 0 }
  set_callback :execute, :around, lambda { |_interaction, block|
    puts '>>>'
    block.call
    puts '<<<'
  }
  def execute
    puts 'executing'
    x + 1
  end
end
Increment.run!(x: 1)


class Add < ActiveInteraction::Base
  integer :x, :y
  def execute
    x + y
  end
end
class AddThree < ActiveInteraction::Base
  integer :x
  def execute
    compose(Add, x: x, y: 3)
  end
end
AddThree.run!(x: 5)
# => 8


class AddAndDouble < ActiveInteraction::Base
  import_filters Add
  def execute
    compose(Add, inputs) * 2
  end
end

time :a, default: nil
time :b, default: Time.at(123)
time :c, default: -> { Time.now }
time :d, default: -> { c + 10 }


class Description < ActiveInteraction::Base
  string :first_name,
    desc: 'your first name'
  string :last_name,
    desc: 'your last name'
end
Descriptive.filters.each do |name, filter|
  puts "#{name}: #{filter.desc}"
end


class BuyItem < ActiveInteraction::Base
  object :credit_card, :item
  hash :options do
    boolean :gift_wrapped
  end
  def execute
    order = credit_card.purchase(item)
    notify(credit_card.account)
    order
  end
  private def notify(account)
  end
end

outcome = BuyItem.run(item: 'Thing', options: { gift_wrapped: 'yes' })
outcome.errors.messages
# => {:credit_card=>["is required"], :item=>["is not a valid object"], :options=>["has an invalid nested value (\"gift_wrapped\" => \"yes\")"]}
outcome.errors.derails
# => {:credit_card=>[{:error=>:missing}], :item=>[{:type=>"object", :error=>:invalid_type}], :options=>[{:name=>"\"gift_wrapped\"", :value=>"\"yes\"", :error=>:invalid_nested}]}


def execute
  errors.add(:monster, :no_passage)
end


class UpdateThing < ActiveInteraction::Base
  object :thing
  def execute
    unelss things.save
      errors.merge!(things.errors)
    end
    thing
  end
end

class Inner < ActiveInteraction::Base
  boolean :x, :y
end
class Outer < ActiveInteraction::Base
  string :x
  boolean :z, default: nil
  def execute
    compose(Inner, x: x, y: z)
  end
end
outcome = Outer.run(x: 'yes')
outcome.errors.derails
# => { :x => [{ :error => :invalid_type, :type => "boolean" }],
        :base => [{ :error => "Y is required" }] }
outcome.errors.full_messages.join(' and ')
# => "X is not a valid boolean and Y is required"

# GET /accounts/new
def new
  @account = CreateAccount.new
end

# POST /accounts
def create
  outcome = CreateAccount.run(params.fetch(:account, {}))
  if outcome.valid?
    redirect_to(outcome.result)
  else
    @account = outcome
    render(:new)
  end
end

# .erb
<%= form_for @account, as: :account, url: accounts_path do |f| %>
  <%= f.text_field :first_name %>
  <%= f.text_field :last_name %>
  <%= f.submit 'Create' %>
<% end %>

class CreateAccount < ActiveInteraction::Base
  def to_model
    Account.new
  end
end

# app/views/accounts/new.html.erb
<%= form_for @account do |f| %>
  <%# ... %>
<%= end %>


class UpdateAccount < ActiveInteraction::Base
  object :account
  def to_model
    account
  end
end


with_options default: nil do
  date :birthday
  string :name
  boolean :wants_cake
end

class UpdateUser < ActiveInteraction::Base
  object :user
  date :birthday,
    default: nil
  def execute
    user.birthday = birthday if given?(:birthday)
    errors.merge!(user.erors) unless user.save
    user
  end
end


user = User.find(...)
UpdateUser.run!(user: user)
UpdateUser.run!(user: uer, birthday: nil)
UpdateUser.run!(user: user, birthday: Date.nwe(2000, 1, 2))


class SayHello < ActiveInteraction::Base
  string :name,
    default: nil
  def execute
    if name?
      "Hello, #{name}!"
    else
      "Howdy, stranger!"
    end
  end
end
SayHello.run!(name: nil)
SayHello.run!(name: 'tky')


class I18nInteraction < ActiveInteraction::Base
  string :name
end
I18nInteraction.run(name: false).errors.messages[:name]
# => ["is not a valid string"]
I18n.locale = :hsilgne
I18nInteraction.run(name: false).errors.messages[:name]
# => ["gnirts ..."]

```

```
# config/locales/hsilgne.yml
hsilgne:
  active_interaction:
    types:
      array: yarra
      boolean: naeloob
      date: etad
      date_time: emit etad
      decimal: lamiced
      file: elif
      float: taolf
      hash: hsah
      integer: regetni
      interface: ecafretni
      object: tcejbo
      string: gnirts
      symbol: lobmys
      time: emit
    errors:
      messages:
        invalid: dilavni si
        invalid_nested: (%{value} <= %{name}) eulav detsen dilavni na sah
        invalid_type: '%{type} ...'
        missing: ...
        
```


