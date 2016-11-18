# Linker Script #

---
Last updated: 2016/11/19   
Edited by: Guohui Xu

### [Contribute to this file](https://github.com/XuGuohui/XuGuohui.github.io/blob/master/markdown/Gists/LinkerScript.md)###


## How the entry point effect the link output

In general, everything exits for some reason, the same as the entry point when linking a program. Let's image this scene:

1. We have a program, the source code of which is made up of three functions: `funca()`, `func2()` and `func3()`.
2. Both `func1()` and `func2()` invoke the `func3()`.

Then how will the program execute? It depends on the link behaviour. But how does the linker know where to begin the link procedure? 

1. Link the `func1()` and `func3()`?
2. Link the `func2()` and `func3()`?

To get this question resolved, we should knoe how does the linker behave during linking. The compiler compiles the source code and generates the object files, which contain the symbols to be linked together. When linking, the linker checks what symbols are needed and what should be discarded. For instance, if the `func1()` is needed, then `func3()` is also needed in output, since the `func1()` references the `func3()`. Similarly, if the `func2()` is needed. then `func3()` is also needed in output.

But how does the linker know the symbol to be linked first to finally generate the target program? `func1()` or `func2()`? So, we need to tell the linker the first instruction to begin the link procedure, that is what the entry point does. If we specify the entry point to be `func1()` (i.e. the address of the first instruction of `func1()`), then the linker will link symbols begining with the `func1()`, which will result the final program running like this:

`func1() -> func3() -> exit`

If we specify the entry point to be `func2()`, then the linker will link symbols begining with the `func2()`, which will result the final program running like this:

`func2() -> func3() -> exit`

If we specify the entry point to be `func3()`, then the linker will link symbols begining with the `func3()`, which will result the final program running like this:

`func3() -> exit`

After all referenced symbols are linked, the unreferenced symbols will be discarded if `--gc-sections` is specified when linking, or they are kept in final program. If the `--gc-sections` is specified, you can still keep some symbols by prefix the `KEEP()` keyword in linker script.

**Note** : If a symbol is kept in the output because of one of the following situation:

1. It is specified as the entry point
2. It is referenced by previously linked symbols
3. It is unreferenced but `--gc-sections` is not specified
4. It is prefix with the `KEEP()` keyword in linker script

then the symbols referenced by it will also be kept in the output. For example, if the `func1()` is the entry point and, the `--gc-sections` is not specified or the `func2()` is prefix with `KEEP()`, then the `func2()` will be kept in the output, as well as the symbols being referenced by `func2()`.

Since the first symbol (i.e. the address of the first instruction) to be linked is the origin of the linking procedure, so that's why

> The first instruction to execute in a program is called the entry point.

There are several ways to set the entry point. The linker will set the entry point by trying each of the following methods in order, and stopping when one of them succeeds:

> - the `-e' entry command-line option;
> - the ENTRY(symbol) command in a linker script;
> - the value of a target specific symbol, if it is defined; For many targets this is start, but PE and BeOS based systems for example check a list of possible entry symbols, matching the first one found.
> - the address of the first byte of the `.text' section, if present;
> - The address 0.

The Linker will linked the symbols being referenced after the first instruction.

**Note:** The entry point is only to tell the linker from which instruction to start the linker procedure. It may not be the first instruction to run after reset!


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
