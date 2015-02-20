#Elixir Cheat Sheet
A quick reference for the Elixir programming Language and standard library.  
Elixir Website: http://elixir-lang.org/  
Elixir Documentation: http://elixir-lang.org/docs.html  
Elixir Source: https://github.com/elixir-lang/elixir  
This Cheet Sheet: https://github.com/itsgreggreg/elixir_cheet_sheet  

###Primitives
####**Integer**
Can be specified in base 10, hex, or binary. All are stored as base 10.

 - `1234567` *-or-* `1_234_567`
 - `0xcafe` *-or-* `0xCAFE`
 - `0b10101`


 ```(elixir)
 > 0xCafe == 0b1100101011111110            # true
 > 0xCafe == 51_966                        # true
 > Integer.to_string(51_966, 16) == "CAFE" # true
 > Integer.to_string(0xcafe) == "51996"    # true
 ```

###**Float**
64bit double precision. Can be specified with an exponent. Must have a number, cannot begin with `.`.
 - `1.2345`
 - `0.01` or `1.0e-2`
```(elixir)
> 0.001 == 1.0e-2 # true
> .001            # syntax error
```
