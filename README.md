#Elixir Quick Reference
A quick reference for the Elixir programming Language and standard library.

Elixir Website: http://elixir-lang.org/<br>
Elixir Documentation: http://elixir-lang.org/docs.html<br>
Elixir Source: https://github.com/elixir-lang/elixir<br>
Try Elixir Online: http://try-elixir.herokuapp.com/<br>
This Document: https://github.com/itsgreggreg/elixir_quick_reference<br>

## Basic Types

####Integer
Can be specified in base 10, hex, or binary. All are stored as base 10.

 ```elixir
 > 1234567 == 1_234_567                    # true
 > 0xcafe  == 0xCAFE                       # true
 > 0b10101 == 65793                        # true
 > 0xCafe  == 0b1100101011111110           # true
 > 0xCafe  == 51_966                       # true
 > Integer.to_string(51_966, 16) == "CAFE" # true
 > Integer.to_string(0xcafe) == "51996"    # true
 ```

####Float
64bit double precision. Can be specified with an exponent. Cannot begin or end with `.`.

```elixira
> 1.2345
> 0.001 == 1.0e-3 # true
> .001            # syntax error!
```

####Atom
Constants whose name is their value. <br>
Are named in this format:
<!-- atom-name-regex :[_0-9a-zA-z]+[@_a-zA-Z0-9]+[!?]{0,1} -->
![Atom Naming](https://rawgit.com/itsgreggreg/elixir_quick_reference/c01ab798730a3154f8ecf8ab0d714ae22e9fa54b/images/atom-naming.png)<br>
`:!` and `:@` are also allowed.<br>
To use other characters you must quote the atom.<br>
TODO: Note which characters can be used when quoted.<br>
Stored in a global table once used and never de-allocated so avoid programmatic creation.<br>

```elixir
> :something
> :_some_thing
> :allowed?
> :Some@Thing@12345
> :"Üñîçødé and Spaces"
> Atom.to_string(:Yay!)  # "Yay!"
> :123                   # syntax error!
```

####Boolean
 `true` and `false` are just syntactic sugar for `:true` and `:false` and not a special type.
 
```elixir
> true  == :true     # true
> false == :false    # true
> is_boolean(:true)  # true
> is_atom(false)     # true
> is_boolean(:True)  # false!
```

####Binary
A binary is a sequence of bytes enclosed in `<< >>` and separated with `,`.<br>
By default each number is 8 bits though size can be specified with:<br>
`::size(n)`, `::n`, `::utf8`, `::utf16`, `::utf32` or `::float`<br>
If the number of bits in a binary is not divisible by 8, it is considered a bitstring.<br>
Binaries are concatenated with `<>`.
```elixir
> <<0,1,2,3>>
> <<100>> == <<100::size(8)>>        # true
> <<4::float>> == <<64, 16, 0, 0, 0, 0, 0, 0>>  # true
> <<65::utf32>> == <<0, 0, 0, 65>>   # true
> <<0::2, 1::2>> == <<1::4>>         # true
> <<1,2>> <> <<3,4>> == <<1,2,3,4>>  # true
> is_binary(<<1,2,3,4>>)             # true
> is_binary(<<1::size(4)>>)          # false!, num of bits not devisible by 8
> is_bitstring(<<1::size(4)>>)       # true
```

####String
Strings are UTF-8 encoded binaries. They are enclosed in double quotes(`"`).<br>
They can span multiple lines and contain interpolations.<br>
Interpolations are enclosed in `#{}` and can contain any expression.<br>
Strings, being binaries, are concatenated with `<>`.

```elixir
> "This is a string."
> "This is an #{ Atom.to_string(:interpolated) } string."
> "Where is " <> "my other half?"
> "multi\nline" == "multi
line"                                    # true
> <<69,108,105,120,105,114>> == "Elixir" # true
> String.length("🎩")               # 1
> byte_size("🎩")                   # 4
> is_binary("any string")           # true
> String.valid?("こんにちは")         # true
> String.valid?("hello" <> <<255>>) # false!
> String.valid?(<<4>>)              # true
> String.printable?(<<4>>)          # false! 4 is a valid UTF-8 codepoint, but is not printable.
```
##### Escape Sequences
characters                | whitespace          | control sequences
--------------------------|---------------------|----------------------
`\"` – double quote       | `\b` – backspace    | `\a` – bell/alert
`\'` – single quote       | `\f` - form feed    | `\d` - delete
`\\` – single backslash   | `\n` – newline      | `\e` - escape
                          | `\s` – space        | `\r` – carriage return
                          | `\t` - tab          | `\0` - null byte
                          | `\v` – vertical tab | 

`\x...` - character with hexadecimal representation. <br>
`\x{...}` - character with hexadecimal representation with one or more hexadecimal digits.<br>
```elixir
> "\x3f" == "?"      # true
> "\x{266B}" == "♫" # true
> "\x{2660}" == "♠" # true
```



#### Regular Expression
Inherited from Erlang's `re` module and are Perl compatible.<br>
Written literally with the `~r` [Sigil](#sigils) and can span multiple lines.<br>
Can have a number of modifiers specified directly after the pattern.<br>
Many functions take a captures option that limits captures.

```elixir
> Regex.compile!("caf[eé]") == ~r/caf[eé]/ # true
> Regex.match?(~r/caf[eé]/, "café")        # true
> Regex.regex?(~r"caf[eé]")                # true
> Regex.regex?("caf[eé]")                  # false! string not compiled regex
> Regex.run(~r/hat: (.*)/, "hat: 🎩", [capture: :all_but_first]) == ["🎩"]  # true
```

## Collection Types

####List
Simple linked lists that can be of any size and can have elements of any type.<br>
They are enclosed in `[ ]` and elements are comma separated.<br>
Concatenated with `++` and subtracted with `--`.<br>
Can be constructed with the cons operator `|`.<br>
Best for sequential access, fastest when elements are added and subtracted from the head.<br>
Instead of building a list by adding to its tail, add to the head and reverse the list.

```elixir
> [1, 2, 3.4, "a", "b", :c, [:d]]
> [ 1 | [2 | [3]]] == [1, 2, 3]   # true
> [1, 2, 3.4] ++ ["a", "b", :c]   # [1, 2, 3.4, "a", "b", :c]
> [1, 2, 3.4, "a", "b", :c, [:d]] -- [2, "a", "c"]  # [1, 3.4, "b", :c, [:d]]
> hd [1, 2, 3]               # 1
> tl [1, 2, 3]               # [2, 3]
> length [:a, :b, :c, :d]    # 4
> Enum.reverse [:a, :b, :c]  # [:c, :b, :a]
> Enum.member? [:a, :b], :b  # true
```

#### Charlist
 - `?` operator
 - Same [Escape Sequences](#escape-sequences) as String.
 - TODO

#### Tuple
Can be of any size and have elements of any type.<br>
Elements are stored contiguously in memory.<br>
Enclosed in `{ }` and elements are comma separated.<br>
Fast for index-based access, slow for a large number of elements.<br>

```elixir
> { :a, 1, {:b}, [2]}
> put_elem({:a}, 0, :b) # {:b}
> elem({:a, :b, :c}, 1) # b
> Tuple.delete_at({:a, :b, :c}, 1) # {:a, :c}
> Tuple.insert_at({:a, :c}, 1, :b) # {:a, :b, :c}
> Tuple.to_list({:a, :b, :c})      # [:a, :b, :c]
```

#### Keyword List
A `List` of 2 element `Tuples` whose first element is an `Atom`.<br>
They have a special syntactic variant where each `Tuple`s `{ }` are ommitted and the key `Atom` has it's colon on the right side.<br>
Since they are just `List`s they guarantee order as specified, can have multiple elements with the same key and are fastest when accessed at the head.<br>
Elements can be accessed with `[:key]` notation. The first Element with a matching `:key` will be returned.<br>
Since they are lists they are concatenated with `++` and subtracted with `--`.<br>
Keyword Lists are only equal if all elements are equal and in the same order.

```elixir
> [{:a, "one"}, {:b, 2}] == [a: "one", b: 2]   # true
> [a: 1] ++ [a: 2, b: 3] == [a: 1, a: 2, b: 3] # true
> [a: 1, b: 2] == [b: 2, a: 1]         # false! elements are in different order
> [a: 1, a: 2][:a] == 1                # true
> Keyword.keys([a: 1, b: 2])           # [:a, :b]
> Keyword.get_values([a: 1, a: 2], :a) # [1, 2]
> Keyword.keyword?([{:a,1}, {:b,2}])   # true
> Keyword.keyword?([{:a,1}, {"b",2}])  # false! "b" is not an Atom
> Keyword.delete([a: 1, b: 2], :a)     # [b: 2]
```

#### Map
Key - Value store where Keys and Values are of any type.<br>
Cannot have multiple values for the same key and are unordered.<br>
`Map`s are enclosed in `%{ }`, elements are comma seperated, and elemets have the form: key `=>` value.<br>
If all keys are `Atom`s, the `=>` can be omitted and the `Atom`'s `:` must be on the right.<br>
Values are accessed with `[key]` notation.<br>
Maps can be accessed with `.key` notation if key is an `Atom`.<br>
Maps can be updated by enclosing them in `%{}` and using the cons `|` operator.<br>
Maps can be of any size and are fastest for key based lookup.

```elixir
> %{:a => 1, 1 => ["list"], [2,3,4] => {"a", "b"}}
> %{:a => 1, :b => 2} == %{a: 1, b: 2}              # true
> %{a: "one", b: "two", a: 1} == %{a: 1, b: "two"}  # true
> %{a: "one", b: "two"} == %{b: "two", a: "one"}    # ture
> %{a: "one", b: "two"}[:b]                         # "two"
> %{a: "one", b: "two"}.b                           # "two"
> %{a: "one", a: 1} == %{a: 1}                      # true
> %{:a => "one", "a" => "two"}."a" == "two"         # false! watchout
> Map.keys( %{a: 1, b: 2} ) == [:a, :b]             # true
> %{ %{a: 1, b: 2, c: 3} | :a => 4, b: 5 }          # %{a: 4, b: 5, c: 3}
> Map.merge( %{a: 1, b: 2}, %{a: 4, c: 3} )         # %{a: 4, b: 2, c: 3}
> Map.put( %{a: 1}, :b, 2 ) == %{a: 1, b: 2}        # true
> Kernel.put_in # TODO
```

#### Range
Used to generate an enumeration between start and end integers.
That's it.<br>

```elixir
> 5..10
> Range.new(5, 10) == 5..10 	# true
> Range.range?(5..10)           # true
> Enum.each(5..10, fn(n) -> n*n end) # prints all the squares
```

#### Streams
 - TODO

## Sytnax

#### Variables
Are declared and initialized upon use.<br>
Are named in the following format:
<!-- [_a-zA-z]+[_a-zA-z0-9]*[!?]? -->
![variable naming](https://raw.githubusercontent.com/itsgreggreg/elixir_quick_reference/07100882977780d0a91b6fddb55ef4f5cb31172d/images/variable-naming.png)<br>
Can hold any data structure and can be assigned more than once.

```elixir
> something = :anything
> something = ["a", "list", "of", "strings"
> _yeeHaw1234! = %{:a => :b}
```

#### Operators
##### Standard infix
 - Equality `==`, `!=`
 - Strict equality `===` and `!==` do not coerce Floats to Integers. `1 === 1.0 #false`
 - Comparison `>`, `<`, `>=`, `<=`
 - Logic, short-circuiting `&&` and `||`
 - Math `+`, `-`, `*`, `/`

##### Standard postfix
 - negation `!`. `!true === false`

##### Pipe
`|>`<br>
Takes the result of a statement on it's left and passes as the first argument to the function on it's right.<br>
The statement on the left can be on the preceeding line of code.
```elixir
> [1,2,3] |> hd |> Integer.to_string |> IO.inspect # "1"
# ⇣ doesn't work in iex
hd([1,2,3])
|> Integer.to_string
|> IO.inspect  # "1"
```

##### String Match
`=~` takes a string on the left and on the right either a string or a regular expression.<br>
If the string on the right is a substring of left, `true` is returned.<br>
If the regular expression on the right matches the string on the left, `true` is returned.<br>
Otherwise `false` is returned.

```elixir
> "abcd" =~ ~r/c(d)/ # true
> "abcd" =~ ~r/e/    # false
> "abcd" =~ "bc"     # true
> "abcd" =~ "ad"     # false
```

##### Ternary
Elixir has no ternary opperator. The same effect though can be achieved with the `if` macro.
```elixir
> a = if true, do: "True!", else: "False!"
> a == "True!"  # true
```

##### Comments
`#` indicates that itself and anything after it until a new line is a comment. That is all.

##### Semicolons
Semicolons can be used to terminate statements but in practice are rarely if ever used.<br>
The only required usage is to put more than one statement on the same line. `a = 1; b = 2`<br>
This is considered bad style and placing them on seperate lines is much prefered.

##### Do, End
To call a macro with a single line of code you must structure it like so
```elixir
#      ⇣   ⇣         ⇣ no end keyword
if true, do: "True!"
```

The equivalent in multiple lines of code is
```elixir
#       ⇣ ⇣ no comma or colon
if true do
  "True!"
end
```

Both of these are syntactic sugar for
```elixir
if(true, [{:do, "True!"}, {:else, "False!"}])
```


#### Truthiness
`nil` and `false` are falsy.<br>
Everything else is truthy.

```elixir
> !!nil  # false
> !!0    # true
```

## Modules
Modules organize code under a namespace.<br>
They can be meta-programmed but are defined at compile time and cannot be changed after, only replaced .<br>
Named according to the following format:<br>
![Module Naming](https://rawgit.com/itsgreggreg/elixir_quick_reference/75b339df6f6592dd123a8afad0449faae7bd36cc/images/module-naming.png)

#### Declaration
```elixir
defmodule MyModule do
end
```

#### Module Functions
Names must begin with `a-z`.<br>
Names can contain `a-Z`, `A-Z`, `0-9` and `_`.<br>
May end with `?` or `!`.<br>

```elixir
defmodule MyModule do
  def my_function do
    IO.puts("Hello from my function")
  end
end
```

`def` is actually a macro, and like calling any macro, [do ... end](#do-end) can be written as a one liner:
```elixir
defmodule MyModule do
  def my_function, do: IO.puts("Hello from my function")
end
```

Inside of the defining module, functions may be called by name. Outside they must be called with the defining Module's name and a `.`. Eg: `IO.puts()`
```elixir
defmodule MyModule do
  def function1 do
    IO.puts "func 1"
  end
  def function2 do
    function1
    IO.puts "funct 2"
  end
end

> MyModule.function2
```

Arguments are passed to functions positionally and can have default arguments.<br>
Arguments can be of any Type.
```elixir
defmodule MyModule do
  def greet(greeting, who \\ "earthlings") do
    IO.puts("#{greeting} #{who}")
  end
end

> MyModule.printer("'sup", "y'all?")  # "'sup y'all?"
> MyModule.printer("greetings")       # "greetings earthlings"
```

Module functions can be defined multiple times to support different configurations of arguments.
```elixir
defmodule MyModule do
  def greet() do
    greet("hello", "you")
  end
  def greet(greeting, who) do
    IO.puts("#{greeting} #{who}")
  end
end

> MyModule.printer("hello")  # "hello"
> MyModule.printer([1,2,3])  # [1,2,3]
> MyModule.printer()         # "nothing passed"
```

They can also be defined multiple times to Pattern Match on arguments passed.
```elixir
def is_it_the_number_2?(2) do
  true
end
def is_it_the_number_2(value) do
  false
end
```

You can ignore arguments with `_` and our previous example is better written as
```elixir
def is_it_the_number_2?(2) do
  true
end
#                      ⇣ underscore ignores argument
def is_it_the_number_2(_) do
  false
end
```

Module function definitions can have [Guards](#guards).
```elixir
def square(n) when is_number(n), do: n * n
def square(_), do: raise "not a number"
```

#### Working with other modules
 - import
 - require
 - use
 - alias

#### Attributes
 - inlined by compiler
 - @external_resource

#### Documentation
Elixir has documentation built in and you can document your modules and functions with Attributes. <br>
`@moduledoc` describes your module. <br>
`@doc` describes module functions.
```elixir
defmodule MathUtils do
  @moduledoc """
  Random math related functions
  """
  
  @doc "Squares the given number."
  def square(n), do: n*n
end
```

#### Introspection
 - `__info__(:functions)`

## Exceptions

## Control Flow
 - if / else
 - unless / else
 - cond ... do
 - case
 - try/catch/throw

## Guards

## Anonymous Functions

## Comprehensions
  - Multiple lists
  - Multiple Filters
  - Into

## Sigils
Sigils create structures out of text passed to them.<br>
They take the general form `~type{ content }` and can be delimited by `{}`, `[]`, `()`, `//`, `||`, `""`, or `''`.<br>
Built in sigils:

Creates           | With Interpolation | Without Interpolation
------------------|:------------------:|:---------------------:|
String            | s                  | S
Charlist          | c                  | C
List of words     | w                  | W
Regular Expression| r                  | R

```elixir
> a = "Yay!"
> ~s|Quotes #{a} 'used' "willy nilly.|   # "Quotes Yay! 'used' \"willy nilly."
> ~S|Not "interpolated" #{a}|            # "Not \"interpolated\" \#{a}"
> ~c|charlist "with" 'quotes' #{a}|      # 'charlist "with" \'quotes\' Yay!'
> ~w|a list of words #{a} |              # ["a", "list", "of", "words", "Yay!"]
> ~W|a list of words #{a} |              # ["a", "list", "of", "words", "\#{a}"]
```

You can also define custom Sigils:
```elixir
defmodule MySigil do
  def sigil_i(string, 'd') do
    for num <- String.split(string), do: String.to_integer(num) * 2
  end
  def sigil_i(string, _opts) do
    for num <- String.split(string), do: String.to_integer(num)
  end
end
> import MySigil
> ~i|1 2 3 4 5|   # [1, 2, 3, 4, 5]
> ~i|1 2 3 4 5|d  # [2, 4, 6, 8, 10]
```

## Metaprogramming
 - macros
 - quote
 - unquote
 - var!

## Processes

## Structs
  - %ModuleName{}
  - implement Map behaviour
  - Pattern matching on structs
  - @derive

## Pattern Matching
 - `<< size::8, rest::binary>> = <<3,0,25,1,1,2,1,6,4,3>>`
 - `<< data::size(size)-unit(16)-binary, rest::binary>> = rest`

## Working with Files

## Erlang Interoperability
Erlang modules can be called by prepnding them with a colon.
```elixir
:crypto.hash(:sha, "Elixir is the beez knees") 
|> :crypto.bytes_to_integer 
|> Integer.to_string(16) # "62A3326DEDE3EE38C9C85ED6EC87FD888A130D24"
```

## iex
 - `-S mix`
 - `h Moule` \ `h function`
 - `c filename`
 - Tab completion

## Mix
Task runner, build tool, testing harness.<br>
Run `mix help` from the command line for more details.<br>

#### Applications
`mix new PATH` generates the boilerplate for an elixir application.<br>
Run `mix help new` for more details.

#### Tasks
Mix tasks are modules under the `Mix.Task` namespace that `use Mix.Task` and implement a `run/1` function.<br>
If you want your task to show up when `mix help` is run you must include a `@shortdoc`.<br>
`@moduledoc` gets displayed when `mix help sometask` is run.
A base mix task file looks like:
```elixir
defmodule Mix.Tasks.MyTask do
  use Mix.Task
  @shortdoc "Boilerplate for a task"
  @moduledoc """
  This task just echos out the options passed to it.
  """
  def run(opts) do
    IO.inspect(opts)
  end
end
```

Elixir has a built in option parser that you should use for such a task:<br>
http://elixir-lang.org/docs/stable/elixir/OptionParser.html

## Tests
Testing is built into the elixir platform via [ExUnit](http://elixir-lang.org/docs/stable/ex_unit/).<br>
You can define tests in two ways.<br>
If you are in a Mix Application, you put tests in files under the `test` directory.<br>
These tests will be run with `mix test`.<br>
You can also place tests at the end of any elixir file and they will be run on compilation.<br>
Test boilerplate looks like:
```elixir
# ⇣ not needed if in a mix project, setup for you in test_helper.exs
ExUnit.start

defmodule MyModuleTest do
  use ExUnit.Case

  test "the truth" do
    assert 1 + 1 == 2
  end
end
```
