#Elixir Cheat Sheet
A quick reference for the Elixir programming Language and standard library.  
Elixir Website: http://elixir-lang.org/  
Elixir Documentation: http://elixir-lang.org/docs.html  
Elixir Source: https://github.com/elixir-lang/elixir  
This Cheet Sheet: https://github.com/itsgreggreg/elixir_cheet_sheet  

###Primitives
####**Integer**
Can be specified in base 10, hex, or binary. All are stored as base 10.

 - base 10: `1234567` *-or-* `1_234_567`
 - hex: `0xcafe` *-or-* `0xCAFE`
 - binary: `0b10101`


 ```(elixir)
 > 0xCafe == 0b1100101011111110            # true
 > 0xCafe == 51_966                        # true
 > Integer.to_string(51_966, 16) == "CAFE" # true
 > Integer.to_string(0xcafe) == "51996"    # true
 ```
