# C++ Concepts

## 1. Pointers, Pointer Variable, Pointer Declaration, and Initialization

-   **Pointer** -- A pointer is a variable that stores the **memory
    address** of another variable.

-   **Pointer Variable** -- A variable specifically designed to hold
    memory addresses.

-   **Pointer Declaration** -- Defining a pointer using the `*` symbol.

    ``` cpp
    int* ptr;
    ```

-   **Pointer Initialization** -- Assigning the address of a variable to
    a pointer using the address-of operator (`&`).

    ``` cpp
    int num = 10;
    int* ptr = &num;
    ```

## 2. Reference, Dereference, Reference Variable, and Pass-by-Reference

-   **Reference** -- An alias (another name) for an existing variable.

-   **Dereference** -- Accessing or modifying the value stored at the
    address a pointer points to using the `*` operator.

    ``` cpp
    int num = 10;
    int* ptr = &num;
    cout << *ptr;   // Outputs 10
    ```

-   **Reference Variable** -- A variable declared with `&` that refers
    to another variable.

    ``` cpp
    int num = 10;
    int& ref = num;
    ```

-   **Pass-by-Reference** -- Passing a variable to a function by
    reference so the function can modify the original variable.

    ``` cpp
    void increase(int& x) {
        x++;
    }
    ```

## 3. Dynamic Memory Allocation

Dynamic memory allocation is the process of **allocating memory while
the program is running** using the `new` operator and releasing it with
the `delete` operator. It is useful when the required memory size is
unknown at compile time.

``` cpp
int* ptr = new int(10);  // Allocate memory
delete ptr;              // Free memory
```
