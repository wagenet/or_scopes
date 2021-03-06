OrScopes
========

OrScopes allows you easily to chain ActiveRecord Scopes with ORs instead of ANDs.


Examples
========

Consider the following setup:

    class Person < ActiveRecord::Base
        named_scope :men,      :conditions => { :sex => 'M' }
        named_scope :women,    :conditions => { :sex => 'F' }
        named_scope :children, :conditions => "age < 18"
        named_scope :named, lambda{|name|
          { :conditions => { :name => name } }
        }
    end

You call it with the names of a series of scopes as such:

    Person.or_scopes(:women, :children)

This returns a scope like this:

    Person.or_scopes(:women, :children).proxy_options
    # => {:conditions=>"(`people`.`sex` = 'F') OR (age < 18)"}

You can also call it with an array of arrays when the scope requires parameters:

    Person.or_scopes(:women, [:named, 'Sue']).proxy_options
    # => {:conditions=>"(`people`.`sex` = 'F') OR (`people`.`name` = 'Sue')"}

You can also chain it as you would a normal scope:

    Person.men.or_scopes(:children, [:named, 'Joe']).scope(:find)
    # => {:conditions=>"((age < 18) OR (`people`.`name` = 'Joe')) AND (`people`.`sex` = 'M')"}


ToDo
====

- Write tests


Copyright (c) 2009 Peter Wagenet, released under the MIT license
