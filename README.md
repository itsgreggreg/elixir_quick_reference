#Elixir Cheat Sheet
A quick reference for the Elixir programming Language and standard library.  
Elixir Website: http://elixir-lang.org/  
Elixir Documentation: http://elixir-lang.org/docs.html  
Elixir Source: https://github.com/elixir-lang/elixir  
This Cheet Sheet: https://github.com/itsgreggreg/elixir_cheet_sheet  

###Basic Types
####**Integer**
Can be specified in base 10, hex, or binary. All are stored as base 10.

 - `1234567` or `1_234_567`
 - `0xcafe` or `0xCAFE`
 - `0b10101`


 ```(elixir)
 > 0xCafe == 0b1100101011111110            # true
 > 0xCafe == 51_966                        # true
 > Integer.to_string(51_966, 16) == "CAFE" # true
 > Integer.to_string(0xcafe) == "51996"    # true
 ```

###**Float**
64bit double precision. Can be specified with an exponent. Cannot begin with `.`.
 - `1.2345`
 - `0.01` or `1.0e-2`
```(elixir)
> 0.001 == 1.0e-2 # true
> .001            # syntax error
```

###Atom
Constants whose name is their value. Stored in a global table once used.<br>
Can contain: `A-Z`, `a-z`, `0-9`, `_` and `@`. To use other characters you must quote the atom.<br>
TODO: Note which characters can be used when quoted.<br>
Must begin with: `A-Z`, `a-z` or `_`.<br>
```(elixir)
> :something
> :_some_thing
> :Some@Thing@12345
> :"Üñîçødé and Spaces"
> :123                  # syntax error
```
