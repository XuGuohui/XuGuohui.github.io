# Reference in C++

## Gist

*Unless otherwise stated, the lvalue, rvalue, lvalue reference and rvalue reference mentioned herein are NON const*

```
class Test {

};
```

- lvalue
    ```
    int a = 10;
    Test testLvalue;
    ```

- const lvalue
    ```
    const int b = 20;
    const Test testConstLvalue;
    ```

- lvalue reference can bind lvalue
    ```
    Test& testLvalueRefA = testLvalue;
    ```

- const lvalue reference can bind lvalue, const lvalue, rvalue, const rvalue
    ```
    const Test& testConstLvalueRefA = testLvalue;                               // const lvalue reference can bind lvalue
    const Test& testConstLvalueRefB = testConstLvalue;                          // const lvalue reference can bind const lvalue
    const Test& testConstLvalueRefC = ( []()->Test{return Test();} )();         // const lvalue reference can bind returned value (rvalue)
    const Test& testConstLvalueRefD = ( []()->const Test{ return Test();} )();  // const lvalue reference can bind returned const value (const rvalue)
    const int& intConstLvalueRefE = 1;                                          // const lvalue reference can bind const value (rvalue)
    const int& intConstLvalueRefF = a++;                                        // const lvalue reference can bind intermediate value (rvalue)
    const int& intConstLvalueRefG = ( []()->const int{ return 10;} )();         // const lvalue reference can bind returned const pure value (rvalue)
    ```

- rvalue reference can bind rvalue and const pure value
    ```
    Test&& testRvalueRefA = ( []()->Test{return Test();} )();                   // rvalue reference can bind returned non const value (rvalue)
    int&& intRvalueRefB = 1;                                                    // rvalue reference can bind const value (rvalue)
    int&& intRvalueRefC = a++;                                                  // rvalue reference can bind intermediate value (rvalue)
    int&& intRvalueRefD = ( []()->const int{ return 10;} )();                   // rvalue reference can bind returned const pure value (rvalue)
    ```

- const rvalue reference can bind rvalue and const rvalue
    ```
    const Test&& testConstRvalueRefA = ( []()->Test{return Test();} )();        // const rvalue reference can bind returned value (rvalue)
    const Test&& testConstRvalueRefB = ( []()->const Test{ return Test();} )(); // const rvalue reference can bind returned const value (const rvalue)
    const int&& intConstRvalueRefC = 1;                                         // const rvalue reference can bind const value (rvalue)
    const int&& intConstRvalueRefD = a++;                                       // const rvalue reference can bind intermediate value (rvalue)
    const int&& intConstRvalueRefE = ( []()->const int{ return 10;} )();        // const rvalue reference can bind returned const pure value (rvalue)
    ```

- lvalue reference can be initialized with lvalue reference and rvalue reference. When a function's parameter is lvalue reference, we may pass a rvalue reference as the parameter, in which case the rvalue reference is converted to lvalue reference. That's why we need [`std::forward()`](https://en.cppreference.com/w/cpp/utility/forward).
    ```
    Test& testLvalueRefInitA = testLvalueRefA;                                  // lvalue reference can be initialized with lvalue reference
    Test& testLvalueRefInitB = testRvalueRefA;                                  // lvalue reference can be initialized with rvalue reference

    void rvalueRefToLvalueRef(Test& test) {
        Log.info("test is lvalue reference: %s", std::is_lvalue_reference<Test&>::value ? " true" : "false");
    }
    rvalueRefToLvalueRef(testRvalueRefA);
    ```

- const lvalue reference can be initialized with lvalue reference, rvalue reference, const lvalue reference and const rvalue reference. That's why the copy constructor's parameter is const lvalue reference.
    ```
    const Test& testConstLvalueRefInitA = testLvalueRefA;                       // const lvalue reference can be initialized with lvalue reference
    const Test& testConstLvalueRefInitB = testConstLvalueRefA;                  // const lvalue reference can be initialized with const lvalue reference
    const Test& testConstLvalueRefInitC = testRvalueRefA;                       // const lvalue reference can be initialized with rvalue reference
    const Test& testConstLvalueRefInitD = testConstRvalueRefA;                  // const lvalue reference can be initialized with const rvalue reference
    ```

- rvalue reference and const rvalue reference *cannot* be initialized with any kind of reference. All kind of references themselves are lvalue. That's why we need [`std::move()`](https://en.cppreference.com/w/cpp/utility/move). For move constructor, the parameter is rvalue reference. To pass parameter to move constructor, the parameter need to be rvalue, or the returned value of `std::move()`.
    ```
    void notBuildableIfPassRef(Test&& test) {
    }
    void notBuildableIfPassRefConst(const Test&& test) {
    }

    notBuildableIfPassRef(std::move(testLvalueRefA));
    notBuildableIfPassRef(std::move(testRvalueRefA));
    notBuildableIfPassRefConst(std::move(testConstLvalueRefA));
    notBuildableIfPassRefConst(std::move(testConstRvalueRefA));
    ```

- Do not return any kind of reference in function

- Since rvalue reference and const rvalue reference can bind rvalue (for example a returned value), this feature can be used to extend the life cycle of a temporary variable.
    ```
    String refCanExtendTempVarLifeCycle() { // We are not reterning reference!!
        return String("hello");;
    }

    const String& str1 = refCanExtendTempVarLifeCycle(); // const lvalue reference is not writeable

    String&& str2 = refCanExtendTempVarLifeCycle(); // non const rvalue reeference is writeable
    str2.concat(" world");
    ```
    
- lvalue reference and rvalue reference are readable and *writeable*. Thus, even if the const pure value is bind to rvalue reference, the const pure value is allocated in RAM and it has address, and since rvalue reference is writeable, we can modify the content of the address.

- When the parameter of a function is lvalue, all kind of references can be passed in. If we pass in a reference, then a copy constructor will be called, since *all kind of references themselves are lvalue*. To get the move constructor called, we need to use `std::move()`.
```
void acceptRef(Test test) {
}

// Copy constructor will be called
acceptRef(testLvalueRefA);
acceptRef(testConstLvalueRefA);
acceptRef(testRvalueRefA);
acceptRef(testConstRvalueRefA);

// Move constructor will be called
acceptRef(std::move(testLvalueRefA));
acceptRef(std::move(testConstLvalueRefA));
acceptRef(std::move(testRvalueRefA));
acceptRef(std::move(testConstRvalueRefA));
```

## Practice

```
#include "Particle.h"
SYSTEM_MODE(MANUAL);
SerialLogHandler l(LOG_LEVEL_INFO);

class Test {

};

int a = 0; // lvalue
const int b = 1; //lvalue

// lvalue
Test testLvalue; // global lvalue that is allocated in static RAM
const Test testConstLvalue; // Global const lvalue that is allocated in static RAM

// lvalue reference can bind non const lvalue
Test& testLvalueRefA = testLvalue;                                          // lvalue reference can bind non const lvalue

// const lvalue reference can bind all kinds of lvalue and rvalue
const Test& testConstLvalueRefA = testLvalue;                               // const lvalue reference can bind lvalue
const Test& testConstLvalueRefB = testConstLvalue;                          // const lvalue reference can bind const lvalue
const Test& testConstLvalueRefC = ( []()->Test{return Test();} )();         // const lvalue reference can bind returned value (rvalue)
const Test& testConstLvalueRefD = ( []()->const Test{ return Test();} )();  // const lvalue reference can bind returned const value (const rvalue)
const int& intConstLvalueRefE = 1;                                          // const lvalue reference can bind const value (rvalue)
const int& intConstLvalueRefF = a++;                                        // const lvalue reference can bind intermediate value (rvalue)
const int& intConstLvalueRefG = ( []()->const int{ return 10;} )();         // const lvalue reference can bind returned const pure value (rvalue)

// rvalue reference can bind non const rvalue and pure value
Test&& testRvalueRefA = ( []()->Test{return Test();} )();                   // rvalue reference can bind returned value (rvalue)
int&& intRvalueRefB = 1;                                                    // rvalue reference can bind const value (rvalue)
int&& intRvalueRefC = a++;                                                  // rvalue reference can bind intermediate value (rvalue)
int&& intRvalueRefD = ( []()->const int{ return 10;} )();                   // rvalue reference can bind returned const pure value (rvalue)

// const rvalue reference can bind all kinds of rvalue
const Test&& testConstRvalueRefA = ( []()->Test{return Test();} )();        // const rvalue reference can bind returned value (rvalue)
const Test&& testConstRvalueRefB = ( []()->const Test{ return Test();} )(); // const rvalue reference can bind returned const value (const rvalue)
const int&& intConstRvalueRefC = 1;                                         // const rvalue reference can bind const value (rvalue)
const int&& intConstRvalueRefD = a++;                                       // const rvalue reference can bind intermediate value (rvalue)
const int&& intConstRvalueRefE = ( []()->const int{ return 10;} )();        // const rvalue reference can bind returned const pure value (rvalue)

// lvalue reference can be initialized with non const lvalue reference and non const rvalue reference
Test& testLvalueRefInitA = testLvalueRefA;                                  // lvalue reference can be initialized with lvalue reference
Test& testLvalueRefInitB = testRvalueRefA;                                  // lvalue reference can be initialized with rvalue reference

// const lvalue reference can be initialized with all kinds of reference
const Test& testConstLvalueRefInitA = testLvalueRefA;                       // const lvalue reference can be initialized with lvalue reference
const Test& testConstLvalueRefInitB = testConstLvalueRefA;                  // const lvalue reference can be initialized with const lvalue reference
const Test& testConstLvalueRefInitC = testRvalueRefA;                       // const lvalue reference can be initialized with rvalue reference
const Test& testConstLvalueRefInitD = testConstRvalueRefA;                  // const lvalue reference can be initialized with const rvalue reference

// Both const and non const rvalue reference cannot be initialized with any kind of reference
// That's why we need std::move
void notBuildableIfPassRef(Test&& test) {
}
void notBuildableIfPassRefConst(const Test&& test) {
}

// lvalue reference can be initialized with non const rvalue reference
// That's why we need std::forward
void rvalueRefToLvalueRef(Test& test) {
    Log.info("test is lvalue reference: %s", std::is_lvalue_reference<Test&>::value ? " true" : "false");
}

String refCanExtendTempVarLifeCycle() { // We are not reterning reference!!
    return String("hello");;
}

void setup() {
    while (!Serial.isConnected());
    Log.info("Application started");

    // A rvalue reference being bind with a const value is writeable
    intRvalueRefB++;
    Log.info("0x%04lX -> intRvalueRefB: %d", (uint32_t)&intRvalueRefB, intRvalueRefB);

    // A rvalue reference being bind with const pure value is writeable
    intRvalueRefD++;
    Log.info("0x%04lX -> intRvalueRefD: %d", (uint32_t)&intRvalueRefD, intRvalueRefD);

    rvalueRefToLvalueRef(testRvalueRefA);

    const String& str1 = refCanExtendTempVarLifeCycle(); // const lvalue reference is not writeable
    Log.info("str: %s", str1.c_str());

    String&& str2 = refCanExtendTempVarLifeCycle(); // non const rvalue reeference is writeable
    str2.concat(" world");
    Log.info("str2: %s", str2.c_str());

    // notBuildableIfPassRef(testLvalueRefA);
    // notBuildableIfPassRef(testConstLvalueRefA);
    // notBuildableIfPassRef(testRvalueRefA);
    // notBuildableIfPassRef(testConstRvalueRefA);
    notBuildableIfPassRef(std::move(testLvalueRefA));
    notBuildableIfPassRef(std::move(testRvalueRefA));
    notBuildableIfPassRefConst(std::move(testConstLvalueRefA));
    notBuildableIfPassRefConst(std::move(testConstRvalueRefA));
}

```


