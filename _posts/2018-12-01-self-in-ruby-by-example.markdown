---
layout: post
title: "Who owns what in Ruby"
tags:
  - Ruby
---

```ruby
class Person
  self #=> Person
  self.class #=> Class
  @class_instance_variable = "Person's instance variable (not shared with subclasses)"

  @@class_variable = "Person's class variable (shared with subclasses)"

  def self.persons_method
    self #=> Person
    self.class #=> Class
    @class_instance_variable = "Sets Person's instance variable! Not thread safe"
    @@class_variable = "Sets Person's class variable. tsk tsk"
  end

  def bobs_method
    "Bob, an instance of Person, responds to this"
    self #=> #<Person:0x00007f81b687a728>
    self.class #=> Person
    @bobs_instance_variable = "Bob owns this, no other Person will"
  end

  attr_reader :bobs_instance_variable # read's Bob's instance variable

  def set_all_the_things
    @bobs_instance_variable = "Bob owns this. No other person will"
    @@class_variable = "Person's class variable"
  end
end
```
