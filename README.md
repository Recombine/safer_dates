# safer_dates

This library allows users to strictly enforce ISO 8601 date and datetime
parsing in Ruby.

This library is very new and has currently only been tested against Ruby 2.1.3
and Rails 4.1.5. It should not be considered production ready. Use at your own
risk.


## Usage

In your `Gemfile`:

```ruby
gem 'safer_dates'
```

or at the command line:

```sh
$ gem install safer_dates
```

In your ruby program you choose which functionality to enable by requiring different files. The options are:

```ruby
require 'safer_dates/enforcers/date_dot_parse.rb'
require 'safer_dates/enforcers/datetime_dot_parse.rb'
require 'safer_dates/enforcers/attribute_typecasting.rb'

require 'safer_dates/parsers/datetime_parser.rb'
require 'safer_dates/parsers/date_parser.rb'
```

## Examples

```ruby
require 'date'

> Date.parse('10/12/1999')
=> #<Date: 1999-12-10 ((2451523j,0s,0n),+0s,2299161j)>

> require 'safer_dates/enforcers/date_dot_parse.rb'
=> false

> Date.parse('10/12/1999')
=> # Throws error and suggests `Date.new(yyyy, mm, dd)`

> Date.parse('1999-01-01')
=> # Throws error and suggests `Date.new(yyyy, mm, dd)`.
   # despite the fact that this particular string was ISO 8601 compliant,
   # it still is a good idea to avoid the `Date.parse` method in general.

# and similar behavior for datetimes when using `require 'safer_dates/enforcers/datetime_dot_parse.rb'`
```

```ruby
# using Rails and/or activerecord

> class Foo < ActiveRecord::Base
>  # has attribute called `start_date` that maps to a database column of type date
> end
=> nil

> Foo.new(start_date: 'invalid')
=> #<Foo start_date: nil>

> Foo.new(start_date: '25/01/1999')
=> #<Foo start_date: "1999-01-25">

require 'safer_dates/enforcers/attribute_typecasting'

> Foo.new(start_date: 'invalid')
=> # Throws error

> Foo.new(start_date: '25/01/1999')
=> # Throws error

> Foo.new(start_date: '1999-01-25')
=> # Works

> Foo.new(start_date: Date.new(1999, 01, 25))
=> # Works
```

`DateParser` and `DateTimeParser` objects are provided for strict ISO 8601
parsing of strings to dates and datetimes. However it is generally preferable
just to use built-in Ruby methods for this if at all possible: 
`Date.new(yyyy, mm, dd)` and `DateTime.new(yyyy, mm, dd, hh, mm, ss)`

```ruby
require 'safer_dates/parsers/date_parser.rb'

> DateParser.p('10/01/1999')
=> # Throws error

> DateParser.p('1999-01-10')
=> # Works
```

```ruby
require 'safer_dates/parsers/datetime_parser.rb'
> DateTimeParser.p('10/01/1999 11:10:00')
=> # Throws error

> DateTimeParser.p('1999-10-01 11:10:00')
=> # Works
```


## Acknowledgments

The ISO 8601 regexes and date/datetime instantiation logic was originally taken
from rails/activerecord source code.

## License

The [MIT License](LICENSE.txt)
