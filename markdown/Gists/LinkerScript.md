# Linker Script #

---
Last updated: 2016/10/29


## Reference the symbol defined in linker script in your source code ##

If a symbol is presented in `SECTION{}` or assigned by `PROVIDED(symbol = expression)` explicitly, then you can refer to this symbol in
your source code. Note that the value of a symbol is the address for the symbol, so when you refer to a symbol, you need to declare 
the symbol with `extern` keyword, e.g.: 

    extern unsigned char link_ram_start_location;

It doesn't matter what type you declare the symbol, it is just a symbol. You can also declare it as:

    extern void *link_ram_start_location;
    extern unsigned int link_ram_start_location;

Since the value of a symbol assigned in linker script is an address, when you refer to this symbol, usually it is used with `&` preffixed:

    value_of_symbol = &link_ram_start_location;

Image that the `link_ram_start_location` is a variable in your source code, and the address of the variable is assigned in linker script, so
`&lin_ram_start_location` is the value which is assigned in linker script. 

If 

    value_of_symbol = link_ram_start_location;

then the `value_of_symbol` should be the content of the address which is assigned to 'lin_ram_start_location' in linker script.