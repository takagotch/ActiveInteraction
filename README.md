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
```






























```

