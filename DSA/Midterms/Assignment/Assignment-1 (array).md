# C++ Arrays, Pointers, and References

## 1. Array and Parts of an Array

An **array** is a group of values with the same data type stored under one name.
Each value is called an **element**. Elements are accessed using an **index**, in which c++ starts to count from zero instead of one.

### Parts of an Array

- **Data type:** `int` — the type of values stored.
- **Array name:** `numbers` — the name of the array.
- **Array size:** `5` — the number of elements.
- **Index:** The position of an element, such as `0` or `2`.
- **Element:** One value in the array, such as `numbers[2]`.
- **Value:** The actual data stored, such as `30`.

---

## 2. Declaration, Initialization, and Usage

### A. Declaration

**Declaration** tells C++ the array's data type, name, and size.
It is written as:
```cpp
data_type array_name[size];
```

### B. Initialization

**Initialization** gives the array its starting values.
It is written as
```cpp
dtype variable[size] = {value-1, value-2...};
```

Also, C++  allows partial initialization such as:

```cpp
int numbers[5] = {1, 2};
```

Where it has 5 elements, but only 2 are initialized, and the remaining elements become zero:

```text
{1, 2, 0, 0, 0}
```

The size can also be determined automatically. When array is declared without a given sized, but with initialized values, the compiler will automatically assign the size.
### C. Usage

An array is commonly used to:

- Access an element
- Change an element
- Read values
- Display values
- Process all elements using a loop

to access and print an element, you can simply:
```cpp
cout << scores[0];
```
on the other hand, to change an element, you mush provide the index, as well as desired value.
```cpp
scores[1] = 100;
```
---

## 3. Pointers and References in C++ Arrays

A **pointer** stores a memory address.

When used with an array, a pointer can store the address of an array element. This allows the program to access or change array elements through the pointer.

### Example

```cpp
int numbers[3] = {10, 20, 30};

int* ptr = numbers;
```

Here, `ptr` points to the first element of the array.

This is equivalent to:

```cpp
int* ptr = &numbers[0];
```

### Accessing array elements using a pointer

```cpp
cout << *ptr << endl;       // 10
cout << *(ptr + 1) << endl; // 20
cout << *(ptr + 2) << endl; // 30
```

Explanation:

- `ptr` points to `numbers[0]`.
- `*ptr` gets the value of `numbers[0]`.
- `ptr + 1` points to `numbers[1]`.
- `ptr + 2` points to `numbers[2]`.

### Changing an array element using a pointer

```cpp
int numbers[3] = {10, 20, 30};

int* ptr = numbers;

*ptr = 100;
```

The array becomes:

```text
{100, 20, 30}
```

Another example:

```cpp
*(ptr + 1) = 200;
```

The array becomes:

```text
{100, 200, 30}
```

### Important idea

For an array:

```cpp
numbers[i]
```

is equivalent to:

```cpp
*(numbers + i)
```

Therefore, these two statements access the same element:

```cpp
cout << numbers[2];
cout << *(numbers + 2);
```

Both output:

```text
30
```

---

## B. Reference to an Array Element

### Definition

A **reference** is another name for an existing variable.

In an array, a reference can be used as another name for a specific array element.

### Example

```cpp
int numbers[3] = {10, 20, 30};

int& ref = numbers[1];
```

Here, `ref` is another name for `numbers[1]`.

### Accessing an element using a reference

```cpp
cout << ref;
```

Output:

```text
20
```

### Changing an element using a reference

```cpp
ref = 200;
```

The array becomes:

```text
{10, 200, 30}
```

The reference does not create a new array element. It refers to the existing element.

### Reference with a loop

A reference can be used to modify each element of an array.

```cpp
int numbers[3] = {10, 20, 30};

for (int& value : numbers) {
    value *= 2;
}
```

The array becomes:

```text
{20, 40, 60}
```

In this loop, `value` refers directly to each array element.

---

## Pointer vs. Reference in an Array

| Feature | Pointer | Reference |
|---|---|---|
| Purpose | Stores the address of an array element | Acts as another name for an array element |
| Example | `int* ptr = numbers;` | `int& ref = numbers[1];` |
| Access value | `*ptr` | `ref` |
| Move to another element | `ptr + 1` | Cannot be reassigned |
| Change an element | `*ptr = 100;` | `ref = 100;` |
| Common array use | Traversing arrays using addresses | Modifying elements without copying them |

### Simple comparison

```cpp
int numbers[3] = {10, 20, 30};

// Pointer points to the first element
int* ptr = numbers;
*ptr = 100;

// Reference refers to the second element
int& ref = numbers[1];
ref = 200;
```

The resulting array is:

```text
{100, 200, 30}
```

---

## 4. Short Example Code

This program demonstrates array declaration, initialization, usage, pointers, and references.

```cpp
#include <iostream>
using namespace std;

int main() {
    // Declaration and initialization
    int numbers[3] = {10, 20, 30};

    // Display the original array
    cout << "Original array:" << endl;

    for (int i = 0; i < 3; i++) {
        cout << numbers[i] << endl;
    }

    // Pointer to the first array element
    int* ptr = numbers;

    // Change the first element using a pointer
    *ptr = 100;

    // Reference to the second array element
    int& ref = numbers[1];

    // Change the second element using a reference
    ref = 200;

    // Increase the third element
    numbers[2]++;

    // Display the modified array
    cout << "\nModified array:" << endl;

    for (int i = 0; i < 3; i++) {
        cout << numbers[i] << endl;
    }

    return 0;
}
```

### Output

```text
Original array:
10
20
30

Modified array:
100
200
31
```

### Explanation

- `numbers[3]` creates an array with three elements.
- `{10, 20, 30}` gives the elements their initial values.
- `numbers[i]` accesses an element using its index.
- `ptr` points to the first element.
- `*ptr = 100` changes the first element.
- `ref` is another name for the second element.
- `ref = 200` changes the second element.
- `numbers[2]++` increases the third element from `30` to `31`.

## Summary

- An **array** stores multiple values of the same type.
- **Declaration** creates the array.
- **Initialization** gives the array its starting values.
- **Usage** means accessing, changing, reading, or displaying elements.
- A **pointer** can point to an array element and access it using `*`.
- A **reference** can act as another name for an array element.
- Both pointers and references can modify the original array.
