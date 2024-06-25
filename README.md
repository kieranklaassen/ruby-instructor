# Ruby::Instructor

Ruby::Instructor is a gem that provides a wrapper around the OpenAI API, allowing you to easily integrate AI-powered responses into your Ruby applications. It uses ruby-openai to interact with the OpenAI API and tool_tailor to format the responses.

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'ruby-instructor'
```

And then execute:

```
$ bundle install
```

Or install it yourself as:

```
$ gem install ruby-instructor
```

## Supported Response Models

Ruby::Instructor supports various types of response models:

### PORO (Plain Old Ruby Object)

```ruby
# UserDetail class represents a user with a name and age.
class UserDetail
  attr_accessor :name, :age

  # @param name [String] the name of the user
  # @param age [Integer] the age of the user
  def initialize(name:, age:)
    @name = name
    @age = age
  end
end
```

### Rails ActiveRecord Model

```ruby
# == Schema Information
#
# Table name: users
#
#  id         :bigint           not null, primary key
#  name       :string           not null
#  age        :integer
#  created_at :datetime         not null
#  updated_at :datetime         not null
#
class User < ApplicationRecord
  def self.from_instructor(name:, age:)
    user = new(name: name)
    user.update_age(age)
    user
  end

  def update_age(age)
    self.age = age if age.present? && age.to_i > 0
  end
end
```

### Literal Gem

```ruby
class Person
  extend Literal::Properties

  prop :name, String
  prop :age, _Nilable(Integer)
end
```

### Attr JSON Gem

```ruby
class User < ActiveRecord::Base
  include AttrJson::Record

  attr_json :name, :string
  attr_json :age, :integer
end
```

## Usage

### Basic Usage

Use the `InstructorClient.new(OpenAI::Client)` to create a new instance of the `Instructor` class:

```ruby
client = InstructorClient.new(OpenAI::Client)

user = client.chat(
  parameters: {
    model: 'gpt-4',
    messages: [
        { role: 'user', content: 'Jason is 25 years old' }
    ]
  },
  response_model: UserDetail
)

puts user.name # => "Jason"
puts user.age  # => 25
```

### Using Custom Methods

You can use custom methods for creating response model instances:

```ruby
class User < ApplicationRecord
  def self.from_instructor(name:, age:)
    user = new(name: name)
    user.update_age(age)
    user
  end

  def update_age(age)
    self.age = age if age.present? && age.to_i > 0
  end
end

client = InstructorClient.new(OpenAI::Client)

user = client.chat(
  parameters: {
    model: 'gpt-4',
    messages: [
        { role: 'user', content: 'Emily is 30 years old' }
    ]
  },
  response_model: User,
  response_model_method: :from_instructor
)

puts user.name # => "Emily"
puts user.age  # => 30
```

### Configuring Default Custom Methods

You can configure the `InstructorClient` to use a custom method by default:

```ruby
client = InstructorClient.new(OpenAI::Client, response_model_method: :from_instructor)

user = client.chat(
  parameters: {
    model: 'gpt-4',
    messages: [
        { role: 'user', content: 'Alex is 28 years old' }
    ]
  },
  response_model: User
)

puts user.name # => "Alex"
puts user.age  # => 28
```

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake spec` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/kieranklaassen/ruby-instructor. This project is intended to be a safe, welcoming space for collaboration, and contributors are expected to adhere to the [code of conduct](https://github.com/kieranklaassen/ruby-instructor/blob/master/CODE_OF_CONDUCT.md).

## License

The gem is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).

## Code of Conduct

Everyone interacting in the Ruby::Instructor project's codebases, issue trackers, chat rooms and mailing lists is expected to follow the [code of conduct](https://github.com/kieranklaassen/ruby-instructor/blob/master/CODE_OF_CONDUCT.md).
