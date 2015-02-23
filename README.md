#Elixir Quick Reference
A quick reference for the Elixir programming Language and standard library.<br>
Elixir Website: http://elixir-lang.org/<br>
Elixir Documentation: http://elixir-lang.org/docs.html<br>
Elixir Source: https://github.com/elixir-lang/elixir<br>
This Document: https://github.com/itsgreggreg/elixir_quick_reference<br>

###Basic Types

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
Begin with `:` and can contain: `A-Z`, `a-z`, `0-9`, `_` and `@`. Can end with `!`.<br>
To use other characters you must quote the atom.<br>
TODO: Note which characters can be used when quoted.<br>
Must begin with: `A-Z`, `a-z` or `_`. `:!` and `:@` also allowed.<br>
Stored in a global table once used and never de-allocated so avoid programmatic creation.<br>

```elixir
> :something
> :_some_thing
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

#### Regular Expression
Inherited from Erlang's `re` module and are Perl compatible.<br>
Written literally with the `~r` `Sigil` and can span multiple lines.<br>
Can have a number of modifiers specified directly after the pattern.<br>
Many functions take a captures option that limits captures.

```elixir
> Regex.compile!("caf[eé]") == ~r/caf[eé]/ # true
> Regex.match?(~r/caf[eé]/, "café")        # true
> Regex.regex?(~r"caf[eé]")                # true
> Regex.regex?("caf[eé]")                  # false! string not compiled regex
> Regex.run(~r/hat: (.*)/, "hat: 🎩", [capture: :all_but_first]) == ["🎩"]  # true
```

###Collection Types

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
> hd [1, 2, 3]              # 1
> tl [1, 2, 3]              # [2, 3]
> length [:a, :b, :c, :d]   # 4
> Enum.reverse [:a, :b, :c] # [:c, :b, :a]
```

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
```

#### HashDict
