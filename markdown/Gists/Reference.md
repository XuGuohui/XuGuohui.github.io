# Reference in C++

## Gist

- lref can bind lvalue only
- rref can bind rvalue only
- const lref can bind both, and const lvalue, const rvalue

- Do not return reference in function

- value reference and value reference themselves are lvalue

- const lvalue reference can bind rvalue (for example a returned value), this will extend the life cycle of the rvalue:

## Practice

```
SYSTEM_MODE(MANUAL);

SerialLogHandler l(LOG_LEVEL_INFO);

int l_value = 10;

void func1 (int& x) {
    x++;
    Log.info("func1(), 0x%04lX -> x: %d", (uint32_t)&x, x);

    Log.info("int& is lvalue reference: %s", std::is_lvalue_reference<int&>::value ? " true" : "false");
    return;
}

String func2() { // We are not reterning a reference!!!
    String temp("hello");
    Log.info("0x%04lX -> temp", (uint32_t)&temp);
    return temp;
}

void setup() {
    while (!Serial.isConnected());
    Log.info("Application started");

    int& l_ref_a = l_value;
    int&& r_ref_a = l_value++; // The rvalue is allocated in stack
    int& l_ref_b = l_ref_a;

    Log.info("int&& is lvalue reference: %s", std::is_lvalue_reference<int&&>::value ? " true" : "false");

    Log.info("0x%04lX -> l_ref_a", (uint32_t)&l_ref_a);
    Log.info("0x%04lX -> r_ref_a", (uint32_t)&r_ref_a);
    Log.info("0x%04lX -> l_ref_b", (uint32_t)&l_ref_b);

    func1(l_ref_a);
    func1(r_ref_a);
    func1(l_ref_b);

    const String& str = func2();
    Log.info("0x%04lX -> str: %s", (uint32_t)&str, str.c_str());

    String&& str1 = func2();
    str1.concat(" world");
    Log.info("0x%04lX -> str1: %s", (uint32_t)&str1, str1.c_str());
}
```

Output:
```
0000002908 [app] INFO: Application started
0000002908 [app] INFO: int&& is lvalue reference: false
0000002909 [app] INFO: 0x2003E65C -> l_ref_a
0000002910 [app] INFO: 0x200179B4 -> r_ref_a
0000002910 [app] INFO: 0x2003E65C -> l_ref_b
0000002911 [app] INFO: func1(), 0x2003E65C -> x: 12
0000002911 [app] INFO: int& is lvalue reference:  true
0000002912 [app] INFO: func1(), 0x200179B4 -> x: 11
0000002912 [app] INFO: int& is lvalue reference:  true
0000002913 [app] INFO: func1(), 0x2003E65C -> x: 13
0000002913 [app] INFO: int& is lvalue reference:  true
0000002914 [app] INFO: 0x200179B8 -> temp
0000002914 [app] INFO: 0x200179B8 -> str: hello
0000002915 [app] INFO: 0x200179C8 -> temp
0000002915 [app] INFO: 0x200179C8 -> str1: hello world
```

