# Javascript Cheater

**JavaScript** is a high-level, interpreted programming language primarily used for building interactive web pages, but also for server-side development (Node.js), mobile apps (React Native), and more. It's a cornerstone of modern web development.

---

## 1. Original JavaScript Snippets

This section contains your original collection of JavaScript code snippets, demonstrating various fundamental concepts and operations. More detailed explanations and expanded usage examples for these and other topics can be found in the subsequent sections.

```javascript
// Variables
var x = 5;
let y = 10;
const z = 15;

// Functions
function greet(name) {
    return "Hello, " + name;
}

// Arrays
let myArray = [1, 2, 3];
myArray.push(4);

// Objects
let myObject = {
    name: "John",
    age: 30
};

// Conditionals
if (x > y) {
    console.log("x is greater than y");
} else {
    console.log("y is greater than or equal to x");
}

// Loops
for (let i = 0; i < 5; i++) {
    console.log(i);
}

// Classes
class MyClass {
    constructor(value) {
        this.value = value;
    }

    myMethod() {
        return this.value;
    }
}

// Modules
import { someFunction } from './myModule.js';

// Asynchronous JavaScript
async function fetchData() {
    let response = await fetch('https://api.example.com/data');
    let data = await response.json();
    return data;
}

// Error Handling
try {
    // Code that might throw an error
} catch (error) {
    console.error("An error occurred:", error);
}

// Browser Interaction
document.getElementById('myElement').textContent = 'New Text';
```

---

## 2. Expanded Explanations & Modern Usage

This section provides detailed explanations, modern syntax, and additional examples for the concepts introduced above, as well as new topics to offer a more comprehensive guide.

### 2.1 Basics: Variables, Data Types & Operators

#### Variables

Declare variables using `var`, `let`, or `const`.

*   **`var`**: (Legacy) Function-scoped, can be re-declared and re-assigned.
    ```javascript
    var name = "Alice";
    var name = "Bob"; // Allowed
    name = "Charlie"; // Allowed
    ```
*   **`let`**: (ES6+) Block-scoped, can be re-assigned, but not re-declared in the same scope.
    ```javascript
    let age = 30;
    // let age = 31; // Error: Cannot re-declare
    age = 31; // Allowed
    ```
*   **`const`**: (ES6+) Block-scoped, cannot be re-assigned or re-declared. Must be initialized.
    ```javascript
    const PI = 3.14159;
    // PI = 3.14; // Error: Cannot re-assign
    // const GREETING; // Error: Must be initialized

    // For objects/arrays, the reference is constant, but content can change
    const person = { name: "Dave" };
    person.name = "David"; // Allowed
    // person = { name: "Eve" }; // Error: Cannot re-assign reference
    ```

#### Data Types

JavaScript has several built-in data types.

*   **Primitive Types**:
    *   **`string`**: Textual data (e.g., `"hello"`, `'world'`).
    *   **`number`**: Integers and floating-point numbers (e.g., `10`, `3.14`).
    *   **`boolean`**: `true` or `false`.
    *   **`undefined`**: A variable that has been declared but not assigned a value.
    *   **`null`**: Intentional absence of any object value.
    *   **`symbol`**: (ES6+) Unique and immutable values, often used for object property keys.
    *   **`bigint`**: (ES11+) For arbitrarily large integers.
*   **Non-Primitive Type**:
    *   **`object`**: Collections of key-value pairs, arrays, functions.

#### Operators

*   **Arithmetic Operators**: `+`, `-`, `*`, `/`, `**` (exponentiation), `%` (modulo), `++` (increment), `--` (decrement).
    ```javascript
    let x_op = 10;
    console.log(x_op + 5); // 15
    console.log(x_op % 3); // 1
    x_op++; // x_op is now 11
    ```
*   **Assignment Operators**: `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `**=`.
    ```javascript
    let y_op = 5;
    y_op += 3; // y_op is now 8 (y_op = y_op + 3)
    ```
*   **Comparison Operators**: `==` (loose equality), `===` (strict equality), `!=` (loose inequality), `!==` (strict inequality), `>`, `<`, `>=`, `<=`.
    ```javascript
    console.log(5 == "5");   // true (loose equality, type coercion)
    console.log(5 === "5");  // false (strict equality, no type coercion)
    console.log(10 > 5);     // true
    ```
*   **Logical Operators**: `&&` (AND), `||` (OR), `!` (NOT).
    ```javascript
    let a_log = true, b_log = false;
    console.log(a_log && b_log); // false
    console.log(a_log || b_log); // true
    console.log(!a_log);     // false
    ```
*   **Ternary Operator**: `condition ? value_if_true : value_if_false`.
    ```javascript
    let current_age = 20;
    let status = (current_age >= 18) ? "Adult" : "Minor";
    ```
*   **Nullish Coalescing Operator (`??`)**: (ES11+) Returns the right-hand operand when the left-hand operand is `null` or `undefined`.
    ```javascript
    let user = null;
    let defaultName = "Guest";
    let displayName = user ?? defaultName; // Guest
    ```

### 2.2 Control Flow

#### Conditional Statements

*   **`if...else if...else`**:
    ```javascript
    let temperature = 25;
    if (temperature > 30) {
        console.log("It's hot!");
    } else if (temperature > 20) {
        console.log("It's warm.");
    } else {
        console.log("It's cool.");
    }
    ```
*   **`switch`**:
    ```javascript
    let day = "Monday";
    switch (day) {
        case "Monday":
            console.log("Start of the week.");
            break;
        case "Friday":
            console.log("End of the week!");
            break;
        default:
            console.log("Mid-week.");
    }
    ```

#### Loops

*   **`for` loop**:
    ```javascript
    for (let i = 0; i < 5; i++) {
        console.log("For loop iteration: " + i);
    }
    ```
*   **`while` loop**:
    ```javascript
    let count = 0;
    while (count < 3) {
        console.log("While loop iteration: " + count);
        count++;
    }
    ```
*   **`do...while` loop**: Executes at least once.
    ```javascript
    let j = 0;
    do {
        console.log("Do-while loop iteration: " + j);
        j++;
    } while (j < 2);
    ```
*   **`for...in` loop**: Iterates over enumerable properties of an object.
    ```javascript
    const person_loop = { name: "John", age: 30 };
    for (const key in person_loop) {
        console.log(`${key}: ${person_loop[key]}`); // name: John, age: 30
    }
    ```
*   **`for...of` loop**: (ES6+) Iterates over iterable objects (Arrays, Strings, Maps, Sets, NodeLists, etc.).
    ```javascript
    const colors = ["red", "green", "blue"];
    for (const color of colors) {
        console.log(color); // red, green, blue
    }
    ```

### 2.3 Functions & Generators

#### Function Definition

```javascript
function calculateSum(a, b) {
    return a + b;
}
console.log(calculateSum(10, 20)); // 30
```

#### Lambda Functions (Anonymous Functions)

Small, single-expression functions.

```javascript
const multiply = (x, y) => x * y;
console.log(multiply(5, 6)); // 30
```

#### Immediately Invoked Function Expressions (IIFE)

Runs as soon as it's defined. Used for creating private scopes.

```javascript
(function() {
    var secret = "shh!"; // secret is not accessible outside this function
    console.log("IIFE executed.");
})();
// console.log(secret); // Error: secret is not defined
```

### 2.4 Data Structures: Arrays, Objects, Strings

#### Arrays

Ordered, mutable collections of items.

*   **Common Methods**: `push()`, `pop()`, `unshift()`, `shift()`, `splice(start, deleteCount, ...items)`, `slice(start, end)`, `indexOf()`, `includes()`, `forEach(callback)`, `map(callback)`, `filter(callback)`, `reduce(callback, initialValue)`, `find(callback)`.
    ```javascript
    const numbers = [10, 20, 30];
    numbers.push(40); // [10, 20, 30, 40]
    const mapped = numbers.map(n => n * 2); // [20, 40, 60, 80]
    ```

#### Objects

Unordered collections of key-value pairs.

*   **Common Methods & ES6+ Features**:
    *   **`Object.keys()` / `Object.values()` / `Object.entries()`**:
        ```javascript
        const my_car = { brand: "Toyota", model: "Camry" };
        console.log(Object.keys(my_car));   // ["brand", "model"]
        ```
    *   **Spread Syntax (`...`)**: Copy properties, merge objects.
        ```javascript
        const new_car = { ...my_car, color: "blue" };
        ```
    *   **Object Destructuring (ES6+)**: Extract values from objects into variables.
        ```javascript
        const { brand, model } = my_car;
        console.log(brand); // Toyota
        ```

#### Strings

*   **Template Literals (ES6+)**: Use backticks (`` ` ``) for multi-line strings and embedded expressions.
    ```javascript
    const user_name = "Jane";
    const greeting_msg = `Hello, ${user_name}!
Welcome to JavaScript.`;
    console.log(greeting_msg);
    ```
*   **Common Methods**: `substring(start, end)`, `slice(start, end)`, `split(separator)`, `join(separator)` (Array method), `replace(old, new)`, `replaceAll(old, new)`, `trim()`, `startsWith()`, `endsWith()`.

### 2.5 Classes & Objects (Object-Oriented Programming)

#### Class Definition

```javascript
class Animal {
    constructor(name) {
        this.name = name;
    }
    speak() {
        console.log(`${this.name} makes a sound.`);
    }
}
class Dog extends Animal {
    constructor(name, breed) {
        super(name);
        this.breed = breed;
    }
    speak() {
        console.log(`${this.name} (${this.breed}) barks.`);
    }
}
const myDog = new Dog("Buddy", "Golden Retriever");
myDog.speak(); // Buddy (Golden Retriever) barks.
```

### 2.6 Modules & Packages

#### Importing

```javascript
// myModule.js
export const PI = 3.14;
export function multiply(a, b) { return a * b; }

// main.js
import { PI, multiply } from './myModule.js';
import * as MyModule from './myModule.js'; // Import all as an object
console.log(PI); // 3.14
console.log(multiply(2, 4)); // 8
```

#### The `if __name__ == "__main__":` Block (Node.js/Server-side analog)

This is more common in Python, but in Node.js, direct script execution is the default. However, conditional logic can be used for script-specific actions.

```javascript
// Example in Node.js for a script that might be imported
function main() {
    console.log("This code runs when script is executed directly.");
}

if (require.main === module) {
    main();
}
```

### 2.7 Asynchronous JavaScript (Promises & Async/Await)

Handling operations that take time (e.g., network requests, file I/O).

#### Promises (ES6+)

```javascript
const fetchData = () => {
    return new Promise((resolve, reject) => {
        // Simulate an async operation
        setTimeout(() => {
            const success = true; // or false for rejection
            if (success) {
                resolve("Data fetched successfully!");
            } else {
                reject("Failed to fetch data.");
            }
        }, 1500);
    });
};

fetchData()
    .then(data => console.log(data)) // Handles successful result
    .catch(error => console.error(error)) // Handles errors
    .finally(() => console.log("Operation complete.")); // Always runs
```

#### Async/Await (ES8+)

Syntactic sugar built on Promises, making asynchronous code look and behave more like synchronous code.

```javascript
async function getDataAsync() {
    try {
        const response = await fetch('https://api.example.com/some_data'); // 'await' pauses execution until Promise resolves
        const data = await response.json();
        console.log("Async data:", data);
    } catch (error) {
        console.error("Error fetching data:", error);
    }
}

// getDataAsync(); // Call the async function
```

### 2.8 Error Handling (`try...catch...finally`)

Gracefully handle runtime errors.

```javascript
try {
    // Code that might throw an error
    let result = 10 / 0; // In JavaScript, this results in Infinity, not an error
    if (result === Infinity) {
        throw new Error("Division by zero!");
    }
    console.log(result);
} catch (error) {
    console.error("An error occurred:", error.message);
} finally {
    console.log("This always runs, regardless of error.");
}
```

### 2.9 Browser Interaction (Client-Side JS)

These commands are specific to running JavaScript in a web browser environment.

*   **`console.log()`**: Output messages to the browser's developer console.
    ```javascript
    console.log("Debugging information.");
    console.warn("A warning message.");
    console.error("An error message.");
    ```
*   **`alert()`**: Displays a modal alert box with a message.
    ```javascript
    // alert("Hello! This is an alert.");
    ```
*   **`prompt()`**: Displays a modal dialog box that prompts the user for input.
    ```javascript
    // let userName = prompt("Please enter your name:", "Guest");
    // console.log(`User name: ${userName}`);
    ```
*   **`document.querySelector()`**: Selects the first element that matches a specified CSS selector.
    ```javascript
    const myButton = document.querySelector("#myButton");
    if (myButton) {
        myButton.addEventListener("click", () => {
            console.log("Button clicked!");
        });
    }
    ```
*   **`document.getElementById()`**: Selects an element by its ID.
    ```javascript
    const header = document.getElementById("main-header");
    if (header) {
        header.textContent = "New Header Text";
    }
    ```
*   **`localStorage` / `sessionStorage`**: Store data in the browser.
    ```javascript
    localStorage.setItem("username", "Alice");
    let storedUser = localStorage.getItem("username");
    localStorage.removeItem("username");
    // localStorage.clear();
    ```

### 2.10 Modern JavaScript (ES6+ Highlights)

*   **Destructuring Assignment**:
    ```javascript
    const [first, second] = ["a", "b"]; // Array destructuring
    const { name: pName, age: pAge } = { name: "Zoe", age: 25 }; // Object destructuring with renaming
    ```
*   **Rest Parameters (`...`)**: Collects remaining arguments into an array.
    ```javascript
    function sumAll(...numbers) {
        return numbers.reduce((acc, num) => acc + num, 0);
    }
    console.log(sumAll(1, 2, 3)); // 6
    ```
*   **Modules (`import`/`export`)**: For organizing code into separate files.
    ```javascript
    // myModule.js
    export const PI = 3.14;
    export function subtract(a, b) { return a - b; }

    // main.js
    import { PI, subtract } from './myModule.js';
    import * as MyModule from './myModule.js'; // Import all as an object
    console.log(PI); // 3.14
    console.log(subtract(10, 4)); // 6
    ```

### 2.11 Useful Global Objects and Functions

*   **`Math`**: For mathematical operations.
    ```javascript
    console.log(Math.PI);
    console.log(Math.random());    // Random number between 0 (inclusive) and 1 (exclusive)
    console.log(Math.floor(4.7));  // 4
    ```
*   **`Date`**: For working with dates and times.
    ```javascript
    const now = new Date();
    console.log(now.getFullYear());
    console.log(now.toDateString());
    ```*   **`JSON`**: For parsing and stringifying JSON data.
    ```javascript
    const obj = { name: "Alice", age: 30 };
    const jsonString = JSON.stringify(obj); // '{"name":"Alice","age":30}'
    const parsedObj = JSON.parse(jsonString); // { name: "Alice", age: 30 }
    ```
*   **`parseInt()` / `parseFloat()`**: Convert strings to numbers.
    ```javascript
    console.log(parseInt("100px")); // 100
    console.log(parseFloat("3.14abc")); // 3.14
    ```
