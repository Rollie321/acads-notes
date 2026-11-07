# Algorithms and Algorithm Development

## 1. Define and Discuss: Algorithm

An **algorithm** is a finite, ordered set of clear instructions used to
solve a problem or complete a task. It accepts input, processes the
information, and produces an output.

### Characteristics of a Good Algorithm

-   **Input:** Accepts zero or more input values.
-   **Output:** Produces at least one result.
-   **Definiteness:** Each instruction is clear and unambiguous.
-   **Finiteness:** Ends after a limited number of steps.
-   **Effectiveness:** Each step is simple and can be performed.

Algorithms are important in programming because they provide a logical
plan for solving a problem before writing the actual code. They can be
represented using plain language, pseudocode, or flowcharts.

## 2. Algorithm Development

**Algorithm development** is the process of planning and organizing the
steps needed to solve a problem efficiently and correctly.

### Basic Steps in Algorithm Development

1.  **Define the problem** -- Identify what needs to be solved.
2.  **Analyze the requirements** -- Determine the required inputs,
    processes, and expected outputs.
3.  **Design the solution** -- Arrange the solution into logical,
    step-by-step instructions.
4.  **Represent the algorithm** -- Use pseudocode or a flowchart to
    describe the logic.
5.  **Implement the algorithm** -- Convert the design into a programming
    language.
6.  **Test and debug** -- Check the program using different inputs and
    correct any errors.
7.  **Evaluate and improve** -- Make the solution clearer, more
    accurate, or more efficient when needed.

## 3. Vending Machine

### A. Problem Description

Create a simple vending machine program that first accepts the user's
money, then displays only the products that can be purchased with that
amount. The user selects a product, the machine calculates the change,
dispenses the product, and finally returns the change.

### B. Flowchart

``` mermaid
flowchart TD
    A([Start]) --> B[/Enter amount of money/]
    B --> C[Display products affordable with the amount]
    C --> D{Is at least one product affordable?}
    D -- No --> E[Return the inserted money]
    E --> Z([End])
    D -- Yes --> F[/Select a product/]
    F --> G{Is the selection valid and affordable?}
    G -- No --> H[Display invalid selection]
    H --> Z
    G -- Yes --> I[Calculate change]
    I --> J[Dispense selected product]
    J --> K[Dispense change]
    K --> L[Display transaction successful]
    L --> Z
```

### C. Pseudocode

``` text
START

INPUT money

DISPLAY products whose prices are less than or equal to money

IF no product is affordable THEN
    DISPLAY "No available item for the amount entered"
    RETURN the inserted money
ELSE
    INPUT product selection

    IF selection is invalid or product price is greater than money THEN
        DISPLAY "Invalid selection"
        RETURN the inserted money
    ELSE
        change = money - product price

        DISPENSE selected product
        DISPENSE change

        DISPLAY "Transaction successful"
    END IF
END IF

END
```

### D. C++ Program

``` cpp
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    double money, change;
    int choice;
    double price = 0.0;

    cout << "===== VENDING MACHINE =====\n";
    cout << "Insert money (PHP): ";
    cin >> money;

    if (money <= 0) {
        cout << "Invalid amount.\n";
        return 0;
    }

    cout << "\n===== AVAILABLE ITEMS =====\n";

    if (money >= 20.00)
        cout << "1. Water       - PHP 20.00\n";

    if (money >= 35.00)
        cout << "2. Juice       - PHP 35.00\n";

    if (money >= 50.00)
        cout << "3. Sandwich    - PHP 50.00\n";

    if (money >= 25.00)
        cout << "4. Chips       - PHP 25.00\n";

    if (money < 20.00) {
        cout << "No item available for this amount.\n";
        cout << "Returning PHP " << fixed
             << setprecision(2) << money << "\n";
        return 0;
    }

    cout << "\nSelect an item: ";
    cin >> choice;

    switch (choice) {
        case 1:
            price = 20.00;
            break;
        case 2:
            price = 35.00;
            break;
        case 3:
            price = 50.00;
            break;
        case 4:
            price = 25.00;
            break;
        default:
            cout << "Invalid selection. Money returned.\n";
            return 0;
    }

    if (price > money) {
        cout << "Insufficient amount for the selected item.\n";
        cout << "Returning PHP " << fixed
             << setprecision(2) << money << "\n";
        return 0;
    }

    change = money - price;

    cout << "\nCalculating change...\n";
    cout << "Dispensing selected item...\n";
    cout << "Dispensing change: PHP " << fixed
         << setprecision(2) << change << "\n";
    cout << "Transaction successful!\n";

    return 0;
}
```

### E. Explanation of the Program

1.  **Take input money:** The user enters the amount of money to insert.
2.  **Display available items:** The program displays only products
    whose prices do not exceed the inserted amount.
3.  **Select item:** The user enters the number of the desired product.
4.  **Calculate change:** The program subtracts the product price from
    the inserted amount.
5.  **Dispense item:** The machine releases the selected product.
6.  **Dispense change:** The machine returns the remaining money to the
    user.

The program also checks for invalid amounts, invalid selections, and
selections that cost more than the inserted money.

**Note:** This is a basic console-based simulation. It does not control
physical vending-machine hardware or process actual coins and bills.
