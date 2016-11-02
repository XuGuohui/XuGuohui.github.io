# Linker Script #

---
Last updated: 2016/11/1    
Edited by: Guohui Xu

### [Contribute to this file](https://github.com/XuGuohui/XuGuohui.github.io/blob/master/markdown/Gists/LinkerScript.md)###


## What is LMA and VMA ##

LMA, shorted for Load Memory Address, specifies the address where an output section should be placed when downloading it. It influences what the final image looks like before running. The final image is a binary file containing 0 and 1, without any address and debug informations. Usually, a program should be loaded into flash so that it won't lost when power off. Thus, all output sections should be located at flash region by specifying the LMA.

VMA, Virtual Memory Address, specifies the address of a symbol when it is referenced in code at run time. For example, a viariable `x` is assigned with `1` in source code, before linking we don't know what the address of `x` is, so that we just assign `1` to a symbol. During linking, as we specifying the VMA, the linker then know where the `x` is at run time, then it will tell the program that the `1` should be put into the run time address of the `x`.

## Reference the symbol defined in linker script in your source code ##

[>> More details on sourceware.org](https://sourceware.org/binutils/docs/ld/Source-Code-Reference.html#Source-Code-Reference)

Symbols are organized as a symbol table in object files. For example, there are three global varialbles in source code:

```
int x;
char *y;
char z[];
```

After being compiled in object file:

| symbol | address     | value  |
|:------:|:-----------:|:------:|
| x      | &x          | int    |
| y      | &y          | char * |
| z      | z           | char[] |

Note: the address is settled down after linking.

If a symbol is presented in `SECTION{}` or assigned by `PROVIDED(symbol = expression)` explicitly, then you can refer to this symbol in
your source code. Note that the value of a symbol is the address for the symbol. To reference a symbol, you need to declare 
the symbol with `extern` keyword, e.g.: 

    extern unsigned char link_ram_start_location;

It doesn't matter what type you declare the symbol, it is just a symbol. You can also declare it as:

    extern void *link_ram_start_location;
    extern unsigned int link_ram_start_location;

Since the value of a symbol assigned in linker script is an address, when you refer to this symbol, usually it is used with `&` preffixed:

    value_of_symbol = &link_ram_start_location;
    
Particularly, if you declare the symbol as an array or vector, then the array name stands for the begining address of the array. In this case, you should not prefix the symbol with `&`. See this example:

```
extern char *link_ram_start_location1;
extern char link_ram_start_location2[];
```

The `memcpy(&link_ram_start_location1, src_addr, length);` will set the destination address to be the value assigned to `link_ram_start_location1` in linker script. The `memcpy(link_ram_start_location2, src_addr, length);` has the same effect, but without `&` prefixed.

Image that the `link_ram_start_location` is a variable in your source code, and the address of the variable is assigned in linker script, so `&lin_ram_start_location` is the value which is assigned in linker script. 

If 

    value_of_symbol = link_ram_start_location;

then the `value_of_symbol` should be the content of the address which is assigned to `lin_ram_start_location` in linker script.
