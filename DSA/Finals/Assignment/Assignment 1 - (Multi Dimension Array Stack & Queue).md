# 1. Insertion and Deletion of Elements in a 2D Array (C++)

Insertion is the process of adding an element to a specific row and column in a two-dimensional array. In C++, arrays have fixed sizes, so insertion may involve shifting elements or placing a value in an available position.

Deletion is the process of removing an element from a specific position. Since C++ arrays have fixed sizes, the remaining elements may be shifted to fill the gap, or the position may simply be overwritten.

## 2. Stack and Queue Implementation Using Arrays (C++)

**Stack**: A stack follows the Last In, First Out (LIFO) principle. Using a 2D array, elements can be stored in rows and columns, with a `top` variable tracking the position of the most recently inserted element. The `push` operation adds an element, while `pop` removes the top element.

**Queue**: A queue follows the First In, First Out (FIFO) principle. Using a 2D array, elements can be stored in rows and columns, with `front` and `rear` variables tracking the first and last elements. The `enqueue` operation adds an element, while `dequeue` removes the first element.

## 3. Sample Programs in C++

### A. Insertion and Deletion in a 2D Array

```cpp
#include <iostream>
using namespace std;

int main() {
    int arr[2][3] = {
        {1, 2, 3},
        {4, 5, 6}
    };

    // Insert: replace the element at row 0, column 1
    arr[0][1] = 10;

    cout << "After insertion:\n";
    for (int i = 0; i < 2; i++) {
        for (int j = 0; j < 3; j++)
            cout << arr[i][j] << " ";
        cout << endl;
    }

    // Delete: shift elements left in row 0
    for (int j = 1; j < 2; j++)
        arr[0][j] = arr[0][j + 1];

    arr[0][2] = 0; // Mark the last position as empty

    cout << "After deletion:\n";
    for (int i = 0; i < 2; i++) {
        for (int j = 0; j < 3; j++)
            cout << arr[i][j] << " ";
        cout << endl;
    }

    return 0;
}
```

Sample output:

```
After insertion:
1 10 3
4 5 6
After deletion:
1 3 0
4 5 6
```

Note: This example demonstrates replacing an existing value and deleting it by shifting the remaining elements. The value `0` marks the now-unused position.

### B. Stack Implementation Using a 2D Array

```cpp
#include <iostream>
using namespace std;

int main() {
    int stack[1][5];
    int top = -1;

    // Push elements
    stack[0][++top] = 10;
    stack[0][++top] = 20;
    stack[0][++top] = 30;

    cout << "Stack: ";
    for (int i = 0; i <= top; i++)
        cout << stack[0][i] << " ";

    // Pop the top element
    top--;

    cout << "\nAfter pop: ";
    for (int i = 0; i <= top; i++)
        cout << stack[0][i] << " ";

    return 0;
}
```

Sample output:

```
Stack: 10 20 30
After pop: 10 20
```

### C. Queue Implementation Using a 2D Array

```cpp
#include <iostream>
using namespace std;

int main() {
    int queue[1][5];
    int front = 0, rear = -1;

    // Enqueue elements
    queue[0][++rear] = 10;
    queue[0][++rear] = 20;
    queue[0][++rear] = 30;

    cout << "Queue: ";
    for (int i = front; i <= rear; i++)
        cout << queue[0][i] << " ";

    // Dequeue the first element
    front++;

    cout << "\nAfter dequeue: ";
    for (int i = front; i <= rear; i++)
        cout << queue[0][i] << " ";

    return 0;
}
```

Sample output:

```
Queue: 10 20 30
After dequeue: 20 30
```
