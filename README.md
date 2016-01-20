#Elixir Quick Reference
A quick reference for the Elixir programming Language and standard library.

Elixir Website: http://elixir-lang.org/<br>
Elixir Documentation: http://elixir-lang.org/docs.html<br>
Elixir Source: https://github.com/elixir-lang/elixir<br>
Try Elixir Online: http://try-elixir.herokuapp.com/<br>
Elixir Regex editor/testor: http://www.elixre.uk/<br>
This Document: https://github.com/itsgreggreg/elixir_quick_reference<br>

<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
# Table of Contents

- [Basic Types](#basic-types)
  - [Integer](#integer)
  - [Float](#float)
  - [Atom](#atom)
  - [Boolean](#boolean)
  - [Nil](#nil)
  - [Binary](#binary)
  - [String](#string)
    - [Escape Sequences](#escape-sequences)
  - [Regular Expression](#regular-expression)
- [Collection Types](#collection-types)
  - [List](#list)
  - [Charlist](#charlist)
  - [Tuple](#tuple)
  - [Keyword List](#keyword-list)
  - [Map](#map)
  - [Struct](#struct)
  - [Range](#range)
  - [Streams](#streams)
- [Syntax](#syntax)
  - [Variables](#variables)
  - [Operators](#operators)
    - [Standard infix](#standard-infix)
    - [Standard prefix](#standard-prefix)
    - [= (match)](#-match)
    - [^ (pin)](#-pin)
    - [|> (pipe)](#-pipe)
    - [=~ (string match)](#-string-match)
    - [? (codepoint)](#-codepoint)
    - [& (capture)](#-capture)
    - [Ternary](#ternary)
    - [in](#in)
  - [Comments](#comments)
  - [Semicolons](#semicolons)
  - [Do, End](#do-end)
  - [Pattern Matching](#pattern-matching)
    - [Binaries](#binaries)
  - [Reserved Words](#reserved-words)
- [Truthiness](#truthiness)
- [Sorting](#sorting)
- [Modules](#modules)
  - [Declaration](#declaration)
  - [Module Functions](#module-functions)
  - [Private Functions](#private-functions)
  - [Working with other modules](#working-with-other-modules)
  - [Attributes](#attributes)
  - [Documentation](#documentation)
  - [Introspection](#introspection)
- [Errors](#errors)
  - [Raise](#raise)
  - [Custom Error](#custom-error)
  - [Rescue](#rescue)
- [Control Flow](#control-flow)
  - [if/unless](#ifunless)
  - [case](#case)
  - [cond](#cond)
  - [throw/catch](#throwcatch)
- [Guards](#guards)
- [Anonymous Functions](#anonymous-functions)
- [Comprehensions](#comprehensions)
- [Sigils](#sigils)
- [Metaprogramming](#metaprogramming)
- [Processes](#processes)
- [Structs](#structs)
- [Working with Files](#working-with-files)
- [Erlang Interoperability](#erlang-interoperability)
- [IEx](#iex)
- [Mix](#mix)
  - [Applications](#applications)
  - [Tasks](#tasks)
- [Tests](#tests)
- [Style Guide](#style-guide)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->


## Basic Types

### Integer
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

### Float
64bit double precision. Can be specified with an exponent. Cannot begin or end with `.`.

```elixir
> 1.2345
> 0.001 == 1.0e-3 # true
> .001            # syntax error!
```

### Atom
Constants whose name is their value. <br>
Are named in this format:
<!-- :[_a-zA-z][a-zA-Z0-9@_]*[!?]{0,1} -->
![Atom Naming](https://rawgit.com/itsgreggreg/elixir_quick_reference/80954a5e9bd06d5a009857a97bfad640d3cbd31a/images/atom-naming.png)<br>
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

### Boolean
 `true` and `false` are just syntactic sugar for `:true` and `:false` and not a special type.

```elixir
> true  == :true     # true
> false == :false    # true
> is_boolean(:true)  # true
> is_atom(false)     # true
> is_boolean(:True)  # false!
```

### Nil
`nil` is syntactic sugar for `:nil` and is not a special type.

```elixir
> nil == :nil  # true
> is_atom(nil) # true
```

### Binary
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

### String
Strings are UTF-8 encoded binaries. They are enclosed in double quotes(`"`).<br>
They can span multiple lines and contain interpolations.<br>
Interpolations are enclosed in `#{}` and can contain any expression.<br>
Strings, being binaries, are concatenated with `<>`.

```elixir
> "This is a string."
> "☀★☂☻♞☯☭☢€→☎♫♎⇧☮♻⌘⌛☘☊♔♕♖☦♠♣♥♦♂♀"  # no problem :)
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
#### Escape Sequences
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



### Regular Expression
Inherited from Erlang's `re` module and are Perl compatible.<br>
Written literally with the `~r` [Sigil](#sigils) and can span multiple lines.<br>
Can have a number of modifiers specified directly after the pattern.<br>
Many functions take a captures option that limits captures.

**Modifiers**:
 - `u` enables unicode specific patterns like \p and changes escapes like \w, \W, \s and friends to also match on unicode. It expects valid unicode strings to be given on match
 - `i` ignore case
 - `s` dot matches newlines and also set newline to anycrlf.
 - `m` ^ and $ match the start and end of each line; use \A and \z to match the end or start of the string
 - `x` whitespace characters are ignored except when escaped and `#` delimits comments
 - `f` forces the unanchored pattern to match before or at the first newline, though the matched text may continue over the newline
r - inverts the “greediness” of the regexp

**To override newline treatment start the pattern with**:
 - `(*CR)` carriage return
 - `(*LF)` line feed
 - `(*CRLF)` carriage return, followed by linefeed
 - `(*ANYCRLF)` any of the three above
 - `(*ANY)` all Unicode newline sequences

```elixir
> Regex.compile!("caf[eé]") == ~r/caf[eé]/ # true
> Regex.match?(~r/caf[eé]/, "café")        # true
> Regex.regex?(~r"caf[eé]")                # true
> Regex.regex?("caf[eé]")                  # false! string not compiled regex
> Regex.run(~r/hat: (.*)/, "hat: 🎩", [capture: :all_but_first]) == ["🎩"]  # true
# Modifiers
> Regex.match?(~r/mr. bojangles/i, "Mr. Bojangles") # true
> Regex.compile!("mr. bojangles", "sxi")            # ~r/mr. bojangles/sxi
# Newline overrides
> ~r/(*ANY)some\npattern/
```

## Collection Types

### List
Simple linked lists that can be of any size and can have elements of any type.<br>
They are enclosed in `[ ]` and elements are comma separated.<br>
Concatenated with `++` and subtracted with `--`.<br>
Can be constructed with the cons operator `|`.<br>
Best for sequential access, fastest when elements are added and subtracted from the head.<br>
Instead of building a list by adding to its tail, add to the head and reverse the list.<br>
List implements the enumerable protocol so we use Enum for many common operations.

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
> Enum.join [:a, :b], "_"    # "a_b"
> Enum.at [:a, :b, :c], 1    # :b
```

### Charlist
A [List](#list) of UTF-8 codepoints.<br>
Other than syntax they are exactly the same as Lists and are not a unique class.<br>
Can span multiple lines and are delimited with single quotes `'`.<br>
Have the same [Escape Sequences](#escape-sequences) as String.<br>

```elixir
> 'char list'
> [108, 105, 115, 116] == 'list'  # true
> 'turbo' ++ 'pogo'               # 'turbopogo'
> 'char list' -- 'a l'            # 'christ'
> hd 'such list' == ?s            # true
> String.to_char_list "tacosalad" # 'tacosalad'
> List.to_string 'frijoles'       # "frijoles"
> [?Y, ?e, ?a, ?h] == 'Yeah'      # true
```

### Tuple
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

### Keyword List
A List of 2 element Tuples where each Tuple's first element is an Atom.<br>
This atom is refered to as the keyword, or key.<br>
Have a special concice syntax that omit's the Tuple's brackets and places the key's colon on the right.<br>
Being Lists they:
   - are order as specified
   - can have duplicate elements and multiple elements with the same key
   - are fastest when accessed at the head.
   - are concatenated with `++` and subtracted with `--`.<br>

Elements can be accessed with `[:key]` notation. The first Element with a matching `:key` will be returned.<br>
2 Keyword Lists are only equal if all elements are equal and in the same order.

```elixir
# Full Syntax
> [{:a, "one"}, {:b, 2}]
# Concice Syntax
> [a: "one", b: 2]
> [a: 1] ++ [a: 2, b: 3] == [a: 1, a: 2, b: 3] # true
> [a: 1, b: 2] == [b: 2, a: 1]         # false! elements are in different order
> [a: 1, a: 2][:a] == 1                # true
> Keyword.keys([a: 1, b: 2])           # [:a, :b]
> Keyword.get_values([a: 1, a: 2], :a) # [1, 2]
> Keyword.keyword?([{:a,1}, {:b,2}])   # true
> Keyword.keyword?([{:a,1}, {"b",2}])  # false! "b" is not an Atom
> Keyword.delete([a: 1, b: 2], :a)     # [b: 2]
```

### Map
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
> %{a: "one", b: "two"} == %{b: "two", a: "one"}    # true
> %{a: "one", b: "two"}[:b]                         # "two"
> %{a: "one", b: "two"}.b                           # "two"
> %{a: "one", a: 1} == %{a: 1}                      # true
> %{:a => "one", "a" => "two"}."a" == "two"         # false! watchout
> Map.keys( %{a: 1, b: 2} ) == [:a, :b]             # true
> %{ %{a: 1, b: 2, c: 3} | :a => 4, b: 5 }          # %{a: 4, b: 5, c: 3}
> Map.merge( %{a: 1, b: 2}, %{a: 4, c: 3} )         # %{a: 4, b: 2, c: 3}
> Map.put( %{a: 1}, :b, 2 ) == %{a: 1, b: 2}        # true
> Kernel.get_in # TODO
> Kernel.put_in # TODO
```

### Struct
Structs can be thought of as bare Maps with pre-defined keys, default values and where the keys must be atoms.<br>
Structs are defined at the top level of a Module and take the Module's name.<br>
Structs do not implement the Access or Enumerable protocol and can be considered bare Maps.<br>
Structs have a special field called `__struct__` that holds the name of the struct.

```elixir
defmodule City do
  defstruct name: "New Orleans", founded: 1718
end
nola = %City{}
chi =  %City{name: "Chicago", founded: 1833}
nola.name   # "New Orleans"
chi.founded # 1833
nola.__struct__ # City
```

### Range
Used to generate an enumeration between start and end integers.
That's it.<br>

```elixir
> 5..10
> Range.new(5, 10) == 5..10 	# true
> Range.range?(5..10)           # true
> Enum.each(5..10, fn(n) -> n*n end) # prints all the squares
```

### Streams
Lazy enumerables.<br>
Are created out of enumerables with functions in the `Stream` module.<br>
Elements are not computed until a method from the `Enum` module is called on them.<br>
```elixir
> a = Stream.cycle 'abc'
#Function<47.29647706/2 in Stream.unfold/2> # Infinate Stream created
> Enum.take a, 10                           # Enum.take computes the 10 elements
'abcabcabca'
```

With [Stream.unfold/2](http://elixir-lang.org/docs/stable/elixir/Stream.html#unfold/2) you can create an arbitrary stream.
```elixir
> s = Stream.unfold( 5, 
  fn 0 -> nil            # returning nil halts the stream
     n -> {n, n-1}       # return format {next-val, rest}
  end)
> Enum.to_list(s)
[5, 4, 3, 2, 1]
```

## Syntax

### Variables
Are declared and initialized upon use.<br>
Are named in the following format:
<!-- [_a-z][_a-zA-z0-9]*[!?]? -->
![variable naming](https://rawgit.com/itsgreggreg/elixir_quick_reference/50ffe1e533896247fbb6429b72f83cdbb1186a8a/images/variable-naming.png)<br>
Can hold any data structure and can be assigned more than once.

```elixir
> something = :anything
> something = ["a", "list", "of", "strings"]
> _yeeHaw1234! = %{:a => :b}
```

### Operators
#### Standard infix
 - Equality `==`, `!=`
 - Strict equality `===` and `!==` do not coerce Floats to Integers. `1 === 1.0 #false`
 - Comparison `>`, `<`, `>=`, `<=`
 - Logic, short-circuiting `&&` and `||`
 - Boolean only Logic, short-circuiting `and` and `or`. (Only left side must be boolean)
 - Math `+`, `-`, `*`, `/`

#### Standard prefix
 - Negation, any type `!`, `!1 == false`
 - Negation, boolean only `not`, `not is_atom(5) == true`

#### = (match)
left `=` right<br>
Performs a [Pattern Match](#pattern-matching). 

#### ^ (pin)
Used to pin the value of a variable in the left side of a [Pattern Match](#pattern-matching).
```elixir
a = "thirty hams"
{b, ^a} = {:i_need, "thirty hams"}            # `b` is set to `:i_need`
{^a, {^a}} = {"thirty hams", {"thirty hams"}} # nothing is set, but the match succedes
```

#### |> (pipe)
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

#### =~ (string match)
`=~`<br>
Takes a string on the left and on the right either a string or a regular expression.<br>
If the string on the right is a substring of left, `true` is returned.<br>
If the regular expression on the right matches the string on the left, `true` is returned.<br>
Otherwise `false` is returned.
```elixir
> "abcd" =~ ~r/c(d)/ # true
> "abcd" =~ ~r/e/    # false
> "abcd" =~ "bc"     # true
> "abcd" =~ "ad"     # false
```

#### ? (codepoint)
`?`<br>
Returns the UTF-8 codepoint of the character immediately to it's right.<br>
Can only take one character, accepts [Escape Sequences](#escape-sequences).<br>
**Remember** [Charlists](#charlist) are just lists of UTF-8 codepoints.
```elixir
> ?a   # 97
> ?♫   # 9835
> ?\s  # 32
> ??   # 63
> [?♀, ?!] == '♀!'  # true
```

#### & (capture)
 - TODO

#### Ternary
Elixir has no ternary opperator. The same effect though can be achieved with the `if` macro.
```elixir
> a = if true, do: "True!", else: "False!"
> a == "True!"  # true
```

#### in
left `in` right.<br>
Used to check if the **enumerable** on the right contains the data structure on the left.<br>
Right hand side must implement the Enumerable Protocol.

```elixir
> :b in [:a, :b, :c] # true
> [:c] in [1,3,[:c]] # true
> :ok in {:ok} # ERROR: protocol Enumerable not implemented for {:ok}
```

### Comments
`#` indicates that itself and anything after it until a new line is a comment. That is all.

### Semicolons
Semicolons can be used to terminate statements but in practice are rarely if ever used.<br>
The only required usage is to put more than one statement on the same line. `a = 1; b = 2`<br>
This is considered bad style and placing them on seperate lines is much prefered.

### Do, End
Blocks of code passed to macros start with `do` and end with `end`.
```elixir
if true do
  "True!"
end

if true do "True!" end

# inside a module
def somefunc() do
  IO.puts "multi line"
end

if true do
  "True!"
else
  "False!"
end
```

You can pass the block as a single line and without `end` with some extra puctuation.<br>
```elixir
#      ⇣   ⇣         ⇣ no end keyword
if true, do: "True!"
#      ⇣   ⇣       ⇣     ⇣          ⇣ no end keyword
if true, do: "True", else: "False!"
# inside a module
#             ⇣   ⇣                              ⇣ no end keyword
def someFunc(), do: IO.puts "look ma, one line!"
```

Syntactic sugar for
```elixir
if(true, [{:do, "True!"}, {:else, "False!"}])
def(someFunc(), [{:do, IO.puts "look ma, one line!"}])
```

### Pattern Matching
A match has 2 main parts, a **left** side and a **right** side.<br>
```elixir
#     ┌Left       ┌Right
# ┌───┴───┐   ┌───┴──────┐
  {:one, x} = {:one, :two}
#        ┌Right
#    ┌───┴──────┐  
case {:one, :two} do
#     ┌Left
# ┌───┴───┐
  {:one, x} -> IO.puts x
# ┌Left
  _         -> IO.puts "no other match"
end
```

The **right** side is a **data structure** of any kind.<br>
The **left** side attempts to **match** itself to the **data structure** on the right and **bind** any **variables** to **substructures**.

The simplest **match** has a lone **variable** on the **left** and will **match** anything:
```elixir
# in these examples `x` will be set to whatever is on the right
x = 1
x = [1,2,3,4]
x = {:any, "structure", %{:whatso => :ever}}
```

But you can place the **variables** inside a **structure** so you can **capture** a **substructure**:
```elixir
# `x` gets set to only the `substructure` it matches
{:one, x} = {:one, :two} # `x` is set to `:two` 
[1,2,n,4] = [1,2,3,4]    # `n` is set to `3`
[:one, p] = [:one, {:apple, :orange}] # `p` is set to `{:apple, :orange}`
```

There is also a special `_` **variable** that works exactly like other **variables** but tells elixir, "Make sure something is here, but I don't care exactly what it is.":
```elixir
# in all of these examples, `x` gets set to `:two`
{_, x} = {:one, :two}
{_, x} = {:three, :two}
[_,_,x,_] = [1,{2},:two,3]
```
    
If you place a **variable** on the **right**, it's **value** is used:
```elixir
#                          ┌Same as writing {"twenty hams"}
a = {"twenty hams"}        ⇣
{:i_have, {b}} = {:i_have, a} # `b` is set to "twenty hams"
```
   
In the previous example you are telling elixir: I want to **match** a **structure** that is a **tuple**, and this **tuple's** first element is going to be the atom **:i_have**. This **tuple's** second element is going to be a **tuple**. This **second tuple** is going to have one element and whatever it is I want you to bind it to the variable **b**.

If you want to use the **value** of a **variable** in your structure on the **left** you use the `^` operator:
```elixir
a = "thirty hams"
{b, ^a} = {:i_need, "thirty hams"}            # `b` is set to `:i_need`
{^a, {^a}} = {"thirty hams", {"thirty hams"}} # nothing is set, but the match succedes
```

#### Maps
Individual keys can be matched in Maps like so:
```elixir
nola = %{ name: "New Orleans", founded: 1718 }
%{name: city_name} = nola # city_name now equals "New Orleans"
%{name: _, founded: city_founded} = nola # Map must have both a name and a founded key
```
You can use the pin operator (^) to match on variables:
```elixir
field = "founded"
%{^field: city_founded} = nola # city_founded now equals 1718
```

#### Binaries
  - `<< size::8, rest::binary>> = <<3,0,25,1,1,2,1,6,4,3>>`
  - `<< data::size(size)-unit(16)-binary, rest::binary>> = rest`
  - TODO

#### Ranges
Ranges can be pattern matched if both their values are integers.
```elixir
min..max = 20..5000
min == 20    # true
max == 5000  # true
min..max == 1..10.0 # Thats an Argument Error
```

### Reserved words
These words are reserved for the language and cannot be used as variables, module or method names.

`nil`, `true`, `false`, `__MODULE__`,`__FILE__`,`__DIR__`,`__ENV__`,`__CALLER__`

## Truthiness
`:nil` and `:false` are falsy.<br>
`nil` and `false` are syntactic sugar for `:nil` and `:false`.<br>
Everything else is truthy.

```elixir
> !!nil  # false
> !!0    # true
> nil || :nil || false || :false || 1 # 1
```

## Sorting
Any types can be compared using comparison operators.<br>
`number < atom < reference < functions < port < pid < tuple < maps < list < bitstring`

```elixir
10000 < :five == true
"something" > &div/2 == true
```

## Modules
Modules organize code under a namespace.<br>
They can be meta-programmed but are defined at compile time and cannot be changed after, only replaced .<br>
Named according to the following format:<br>
<!-- [A-Z][_a-zA-Z0-9]*(?:\.[A-Z][_a-zA-Z0-9]*)* -->
![Module Naming](https://rawgit.com/itsgreggreg/elixir_quick_reference/75b339df6f6592dd123a8afad0449faae7bd36cc/images/module-naming.png)

### Declaration
```elixir
defmodule MyModule do
end
```

### Module Functions
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
#                         ⇣ indicates "earthlings" to be the default for who
  def greet(greeting, who \\ "earthlings") do
    IO.puts("#{greeting} #{who}")
  end
end

> MyModule.greet("'sup", "y'all?")  # "'sup y'all?"
> MyModule.greet("greetings")       # "greetings earthlings"
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

### Private Functions
To make a function private to a module use `defp` instead of `def`.

```elixir
defmodule ModA do
  defp hi, do: IO.puts "Hello from ModA"
  def say_hi, do: hi
end
ModA.say_hi
# Hello from ModA
ModA.hi
# ** (UndefinedFunctionError) undefined function ModA.hi/0 ModA.hi()
```

### Working with other modules
Inside of a module you can use one of the 4 directives to interact with other modules.
#### import
`import SomeModule` brings all modules and macros of SomeModule into the enclosing module so you can use them un-namespaced<br>
`import` can take either an `only:` or `except:` list in which you specify functions and macros to include.<br>
Alternatively `import SomeModule, only:` can take `:functions` or `:macros` to specify only those.
```elixir
def ModA do
  import ModB  # All Functions and Macros in ModB
  import ModB, except: [destroy_planet: 1] # All Functions and Macros except destroy_planet/1
  import ModB, only: :functions # All functions, no macros
  import ModB, only: [say_hi: 0, fibonacci, 1] # Only the specified functions or macros
end
```
 
#### require
`require SomeModule` allows you to use macros of SomeModule. It also makes sure that SomeModule is compiled before the enclosing module.
  
#### use
`use SomeModule` calls a macro on SomeModule called \_\_using\_\_ and nothing else. It is often used to perform setup for metaprogramming.

#### alias
`alias SomeVery.Long.ModuleName, as: SVLMN` is used simply to shorten a module name to cut down on typing.

### Attributes
Pieces of data that can be thought of as metadata or constants for a module.<br>
They are inlined by the compiler and cannot be changed at runtime.<br>
They can be set multiple times and the value used will be the value set when the function is defined.

```elixir
defmodule ModA do
  @name "April"
  def first, do: @name
  @name "O'Neal"
  def last, do: @name
end
```

TODO:
 - @external_resource
 - Better explanation of attributes in relation to metaprogramming

### Documentation
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

### Introspection
 - `__info__(:functions)`

## Errors
Only to be used in exceptional circumstances, not for control flow.<br>
Built in errors are listed here: http://elixir-lang.org/docs/v1.0/elixir/overview.html#exceptions_summary<br>

### Raise
`raise` a runtime error:
```elixir
> raise "not fabulous enough"
** (RuntimeError) not fabulous enough
```

`raise` a different error:
```elixir
> raise ArgumentError, "I'm done. We're talking in circles."
#** (ArgumentError) I'm done. We're talking in circles.
```

Some errors take specific options, and you must read the source to find them:
```elixir
> raise KeyError, key: "to car", term: "pocket"
#** (KeyError) key "to car" not found in: "pocket"
```

### Custom Error
``` elixir
defmodule LandWarWithRussiaError do
  defexception message: "Never."
end
> raise LandWarWithRussiaError
#** (LandWarWithRussiaError) Never.
```

### Rescue
```elixir
try do
  if false do
    raise "are we there yet?"
  else
    raise ArgumentError, "I'll pull this car around!"
  end
rescue
  e in RuntimeError  -> IO.puts "No!"
  e in ArgumentError -> IO.puts "That's it we're going home!"
end
```

**Remember** There are much better ways to control flow in Elixir than raising/rescuing errors.<br>
Errors should be reserved for truly exceptional situations.


## Control Flow
### if/unless
```elixir
if :something_truthy do
  IO.puts "something truthy happened"
else
  IO.puts "false or nil happened"
end

unless :something_truthy do
  IO.puts "nil or false happened"
else
 IO.puts "something truthy happened"
end
```

### case
`case` let's you pattern match on a value.<br>
If no cases match it throws a `MatchError`.
```elixir
case 137 do
  "137" -> IO.puts "I require 137 the number."
  137   -> IO.puts "Ahh much better."
  138   ->
    IO.puts "Blocks can start on the next line as well."
end
```

Like all pattern matches, `_` will match anything and can be used as a catchall:
```elixir
case {:ok, "everything went to plan"} do
  {:ok, message}    -> IO.puts message
  {:error, message} -> IO.puts "ERROR!: #{message}"
# ⇣catchall, otherwise you'll get an error if nothing matches
  _                 -> IO.puts "I match everything else!"
end
```

You can have [guards](#guards) on your cases:
```elixir
case 1_349 do
  n when is_integer n -> IO.puts "you gave me an integer"
  n when is_binary n  -> IO.puts "you gave me a binary"
  _                   -> IO.puts "you gave me neither an integer nor binary"
end
```

### cond
`cond` takes one or more conditions and runs the first truthy one it finds.<br>
Often used where imperative languages would use elseif.<br>
If no statements evaluate to true it throws a `MatchError`.
```elixir
cond do
  false -> IO.puts "I will never run"
  true  -> IO.puts "I will always run"
  1235  -> IO.puts "I would run if that dang true wasn't on top of me."
end
```

`true` is often used as a catch all:
```elixir
guess = 12
cond do
  guess == 10 -> IO.puts "You guessed 10!"
  guess == 46 -> IO.puts "You guessed 46!"
  true        -> 
    IO.puts "I give up."
end
```
### throw/catch
Inside of a `try` block you can `throw` any data type and pattern match on it inside a `catch` block.<br>
`catch` blocks work the same as [case](#case) blocks.<br>
`after` blocks will always run, throw or no throw.
```elixir
try do
  IO.puts "Inside a try block"
  throw [:hey, "Reggie"]
  IO.puts "if there is a throw before me, I'll never run."
catch
  x when is_number(x) -> IO.puts "!!A number was thrown."
  [:hey, name] -> IO.puts "!!Hey was thrown to #{name}."
  _ -> IO.puts "Something else was thrown."
after
  IO.puts "I run regardless of a throw."
end
```

## Guards
TODO: how to use guards.<br>
Errors generated in guards are swallowed and simply cause the guard to fail.<br>
Functions and operators allowed in guard clauses:

 =         | logic | math  | type_checking | tuple        |list     |binary     | Kernel
-----------|-------|-------|---------------|--------------|---------|-----------|--------
==         | or    | +     | is_atom       | elem()       |a `in` b |bit_size() | node()
!=         | and   | -     | is_binary     | tuple_size() |hd()     |byte_size()| self()
===        | not   | *     | is_bitstring  |              |tl()     |           |
!==        |       | /     | is_boolean    |              |length() |           |
>          |       |abs()  | is_exception  ||||
<          |       |div()  | is_float      ||||
<=         |       |float()| is_function   ||||
>=         |       |rem()  | is_integer    ||||
           |       |round()| is_nil        ||||
           |       |trunc()| is_list       ||||
           |       |       | is_number     ||||
           |       |       | is_pid        ||||
           |       |       | is_port       ||||
           |       |       | is_reference  ||||
           |       |       | is_tuple      ||||

**Notice** `!`, `&&` and `||` are not allowed in guard clauses.

## Anonymous Functions
Closures in which the captured variables are set at definition.<br>
Are essentially [case](#case) blocks you can pass around.<br>
Take the general form:
```elixir
fn (1, 2, 3) -> IO.inspect 1*2*3
   (a, b, c) -> IO.inspect [a,b,c]
end
```
Are called with the `.` operator.
```elixir
> add_2 = fn num -> num + 2 end
#      ⇣called with a dot
> add_2.(127) == 129
true
> three = 3
> add_3 = fn num -> num + three end
> add_3.(262) == 265
true
#   ⇣is our function compromised?
> three = 7
#                ⇣no, `three` is still 3 in the function
> add_3.(262) == 265
true
```

Like case blocks, they can have multiple matches, matches can have guards, and do not leak scope:
```elixir
> support_method = fn ("suspenders")   -> IO.puts "Hey big spender."
                      ("belt")         -> IO.puts "Who is the real hero?"
                      a when is_list a -> Enum.each(a, support_method)
                      _                -> IO.puts "What is this wizzardry?"
                   end
> support_method.(["belt", "suspenders"])
"Who is the real hero?"
"Hey big spender."
> peanut_butter = "smooth"
#                                      ⇣immediately call
> fn () -> peanut_butter = "chunky" end.()
> peanut_butter == "smooth"
true # scope was not leaked
```

They **cannot** have multiple arities (which in case statements wouldn't make sense):
```elixir
> fn (a)    -> "One"
     (a, b) -> "Two"
  end
# That's a compile error
```

## Comprehensions
Loop over any enumerable or bitstring and build another.<br>
By default they build lists.<br>
`generators`, `filters` and `into:` are comma separated. `do` follows [Do, End](#do-end) rules.<br>
Basic form:
```elixir
# ┌for         ┌Generator       ┌Block
# ⇣   ┌────────┴────────┐┌──────┴──────┐
> for num <- [1, 2, 3, 4], do: num * num
[1, 4, 9, 16]
```

They can have filters:
```elixir
#                                 ┌Filter
#                        ┌────────┴───────┐
> for num <- [1, 2, 3, 4], rem(num, 2) == 0, do: num * num
[4, 16]
```

Multiple generators:
```elixir
#            ┌Generator       ┌Generator
#     ┌──────┴─────┐┌─────────┴───────┐
> for num <- [1,2,3], str <- ["a", "b"], do: "#{num}#{str}"
["1a", "1b", "2a", "2b", "3a", "3b"]
```

Multiple filters:
```elixir
#                                            ┌Filter     ┌Filter
#                                      ┌─────┴───┐┌──────┴────┐
> for num <- [1,2,3], str <- ["a", "b"], num !== 3, str !== "a", do: "#{num}#{str}"
["1b", "2b"]
```

You can pattern match in generators:
```elixir
> for {_, age} <- %{doug: 4, lucy: 6, ralf: 10}, do: age
[4, 6, 10]
```

You can build a different data structure with `into`:
```elixir
#                             ┌Into a map
#                        ┌────┴────┐
> for num <- [1, 2, 3, 4], into: %{}, do: {num, num*num}
%{1 => 1, 2 => 4, 3 => 9, 4 => 16}
#                             ┌Into a string
#                        ┌────┴───┐
> for num <- [1, 2, 3, 4], into: "", do: "the square of #{num} is #{num * num}. "
"the square of 1 is 1. the square of 2 is 4. the square of 3 is 9. the square of 4 is 16. "
```

Enumerating binaries is a breeze though they have a special syntax:
```elixir
#                  ┌Binary generator
#     ┌────────────┴─────────────────┐
> for <<byte <- <<255, 12, 55, 89>> >>, do: byte
[255, 12, 55, 89]
#              ┌Pattern Match on bit size
#          ┌───┴───┐
> for <<bit::size(1) <- <<42, 12>> >>, do: bit
[0, 0, 1, 0, 1, 0, 1, 0, 0, 0, 0, 0, 1, 1, 0, 0]

> pixels = <<213, 45, 132, 64, 76, 32, 76, 0, 0, 234, 32, 15>>
#          ⇣Short hand for bit-size
> for <<r::8, g::8, b::8 <- pixels >>, do: {r, g, b}
[{213,45,132},{64,76,32},{76,0,0},{234,32,15}]
```

Work well with streams:
```elixir
# Grabs 5 lines of input and upcases it
stream = IO.stream(:stdio, :line)
for line <- Enum.take(stream, 5), into: stream do
  String.upcase(line)
end
```

## Sigils
Sigils create structures out of text passed to them.<br>
They take the general form `~type| content |m` and can be delimited by `{}`, `[]`, `()`, `//`, `||`, `""`, or `''`.<br>
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
> ~S{Not "interpolated" #{a}}            # "Not \"interpolated\" \#{a}"
> ~c[charlist "with" 'quotes' #{a}]      # 'charlist "with" \'quotes\' Yay!'
> ~w/a list of words #{a}/               # ["a", "list", "of", "words", "Yay!"]
> ~W"a list of words #{a}"               # ["a", "list", "of", "words", "\#{a}"]
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

## Working with Files

## Erlang Interoperability
Erlang modules can be called by prepnding them with a colon.
```elixir
:crypto.hash(:sha, "Elixir is the beez knees")
|> :crypto.bytes_to_integer
|> Integer.to_string(16) # "62A3326DEDE3EE38C9C85ED6EC87FD888A130D24"
```

## IEx
Interactive Elixir. An Elixir REPL that comes with your Elixir install<br>

### Running
run `iex` from a command prompt to enter iex.<br>
`iex some_file.ex` will compile ./some_file.ex and load it into iex.<br>

### Using
iex has built in tab completion. Press tab at an empty prompt to see all functions available.

#### Some useful functions
 - h/0 : Documentation for IEx helpers
 - h/1 : Documentation for a Module or Function
 - i/1 : Inforamiton about a value
 - c/1 : Compile and load a file
 - r/1 : Reload a module

#####TODO:
 - `-S mix`
 - Tab completion

## Mix
Task runner, build tool, testing harness.<br>
Run `mix help` from the command line for more details.<br>

### Applications
`mix new PATH` generates the boilerplate for an elixir application.<br>
Run `mix help new` for more details.

### Tasks
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

## Style Guide
 - Module, record, protocol and behaviour names start with an initial cap and are BumpyCase.
   - Ex: MyModule
 - All other identifiers start with a lowercase letter or an underscore and are snake_case.
   - Ex: in_the_air_tonight
 - For indentation use 2 spaces. No Tabs.

<hr>
**Notes**
 - Regex images generated here: http://jex.im/regulex/
