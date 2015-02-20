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

```elixir
> 1.2345
> 0.001 == 1.0e-3 # true
> .001            # syntax error!
```

####Atom
Constants whose name is their value. Stored in a global table once used.<br>
Can contain: `A-Z`, `a-z`, `0-9`, `_` and `@`. Can end with `!`.<br>
To use other characters you must quote the atom.<br>
TODO: Note which characters can be used when quoted.<br>
Must begin with: `A-Z`, `a-z` or `_`. `:!` and `:@` also allowed.<br>

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
