# JavaScript Cheat-Sheet

**JavaScript** is a high-level, interpreted programming language primarily used for building interactive web pages, but also for server-side development (Node.js), mobile apps (React Native), and more. It's a cornerstone of modern web development.

---

## 1. Basics: Variables, Data Types & Operators

### 1.1 Variables

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

### 1.2 Data Types

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

### 1.3 Operators

*   **Arithmetic Operators**: `+`, `-`, `*`, `/`, `**` (exponentiation), `%` (modulo), `++` (increment), `--` (decrement).
    ```javascript
    let x = 10;
    console.log(x + 5); // 15
    console.log(x % 3); // 1
    x++; // x is now 11
    ```
*   **Assignment Operators**: `=`, `+=`, `-=`, `*=`, `/=`, `%=`, `**=`.
    ```javascript
    let y = 5;
    y += 3; // y is now 8 (y = y + 3)
    ```
*   **Comparison Operators**: `==` (loose equality), `===` (strict equality), `!=` (loose inequality), `!==` (strict inequality), `>`, `<`, `>=`, `<=`.
    ```javascript
    console.log(5 == "5");   // true (loose equality, type coercion)
    console.log(5 === "5");  // false (strict equality, no type coercion)
    console.log(10 > 5);     // true
    ```
*   **Logical Operators**: `&&` (AND), `||` (OR), `!` (NOT).
    ```javascript
    let a = true, b = false;
    console.log(a && b); // false
    console.log(a || b); // true
    console.log(!a);     // false
    ```
*   **Ternary Operator**: `condition ? value_if_true : value_if_false`.
    ```javascript
    let status = (age >= 18) ? "Adult" : "Minor";
    ```
*   **Nullish Coalescing Operator (`??`)**: (ES11+) Returns the right-hand operand when the left-hand operand is `null` or `undefined`.
    ```javascript
    let user = null;
    let defaultName = "Guest";
    let displayName = user ?? defaultName; // Guest
    ```

---

## 2. Control Flow

### 2.1 Conditional Statements

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

### 2.2 Loops

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
    const person = { name: "John", age: 30 };
    for (const key in person) {
        console.log(`${key}: ${person[key]}`); // name: John, age: 30
    }
    ```
*   **`for...of` loop**: (ES6+) Iterates over iterable objects (Arrays, Strings, Maps, Sets, NodeLists, etc.).
    ```javascript
    const colors = ["red", "green", "blue"];
    for (const color of colors) {
        console.log(color); // red, green, blue
    }
    ```

---

## 3. Functions

Functions are blocks of code designed to perform a particular task.

### 3.1 Function Declaration

```javascript
function greet(name) {
    return `Hello, ${name}!`;
}
console.log(greet("Alice")); // Hello, Alice!
```

### 3.2 Function Expression (Anonymous Function)

```javascript
const sayHello = function(name) {
    return `Hello, ${name}!`;
};
console.log(sayHello("Bob")); // Hello, Bob!
```

### 3.3 Arrow Functions (ES6+)

Concise syntax, often used for shorter functions and when `this` context handling is important.

*   **Single parameter, single expression**:
    ```javascript
    const double = num => num * 2;
    console.log(double(5)); // 10
    ```
*   **Multiple parameters or no parameters**:
    ```javascript
    const add = (a, b) => a + b;
    console.log(add(2, 3)); // 5

    const getPi = () => 3.14159;
    console.log(getPi()); // 3.14159
    ```
*   **Multiple statements (requires curly braces and `return`)**:
    ```javascript
    const process = (val) => {
        let result = val * 10;
        return result - 1;
    };
    console.log(process(4)); // 39
    ```

### 3.4 Immediately Invoked Function Expressions (IIFE)

Runs as soon as it's defined. Used for creating private scopes.

```javascript
(function() {
    var secret = "shh!"; // secret is not accessible outside this function
    console.log("IIFE executed.");
})();
// console.log(secret); // Error: secret is not defined
```

---

## 4. Arrays

Ordered collections of items.

### 4.1 Declaration & Access

```javascript
const fruits = ["apple", "banana", "cherry"];
console.log(fruits[0]); // apple
console.log(fruits.length); // 3
```

### 4.2 Common Methods

*   **`push()`**: Add to the end.
    ```javascript
    fruits.push("date"); // ["apple", "banana", "cherry", "date"]
    ```
*   **`pop()`**: Remove from the end.
    ```javascript
    const lastFruit = fruits.pop(); // date, fruits is now ["apple", "banana", "cherry"]
    ```
*   **`unshift()`**: Add to the beginning.
    ```javascript
    fruits.unshift("grape"); // ["grape", "apple", "banana", "cherry"]
    ```
*   **`shift()`**: Remove from the beginning.
    ```javascript
    const firstFruit = fruits.shift(); // grape, fruits is now ["apple", "banana", "cherry"]
    ```
*   **`splice(start, deleteCount, ...items)`**: Add/remove elements at any position.
    ```javascript
    fruits.splice(1, 1, "kiwi"); // Remove 1 element at index 1 ("banana"), add "kiwi"
    // fruits is now ["apple", "kiwi", "cherry"]
    ```
*   **`slice(start, end)`**: Creates a shallow copy of a portion of an array.
    ```javascript
    const citrus = fruits.slice(0, 1); // ["apple"]
    ```
*   **`indexOf()` / `lastIndexOf()`**: Find index of an element.
    ```javascript
    console.log(fruits.indexOf("cherry")); // 2
    ```
*   **`includes()`**: Check if an array contains an element.
    ```javascript
    console.log(fruits.includes("apple")); // true
    ```
*   **`forEach(callback)`**: Iterate over elements.
    ```javascript
    fruits.forEach((fruit, index) => {
        console.log(`${index}: ${fruit}`);
    });
    ```
*   **`map(callback)`**: Creates a new array with the results of calling a provided function on every element.
    ```javascript
    const upperFruits = fruits.map(fruit => fruit.toUpperCase());
    // ["APPLE", "KIWI", "CHERRY"]
    ```
*   **`filter(callback)`**: Creates a new array with all elements that pass the test implemented by the provided function.
    ```javascript
    const shortFruits = fruits.filter(fruit => fruit.length < 6);
    // ["apple", "kiwi"] (assuming original fruits: ["apple", "kiwi", "cherry"])
    ```
*   **`reduce(callback, initialValue)`**: Executes a reducer function on each element of the array, resulting in a single output value.
    ```javascript
    const numbers = [1, 2, 3, 4];
    const sum = numbers.reduce((acc, curr) => acc + curr, 0); // 10
    ```
*   **`find(callback)`**: Returns the value of the first element in the array that satisfies the provided testing function.
    ```javascript
    const found = fruits.find(fruit => fruit.startsWith("c")); // "cherry"
    ```

---

## 5. Objects

Unordered collections of key-value pairs.

### 5.1 Declaration & Access

```javascript
const car = {
    brand: "Toyota",
    model: "Camry",
    year: 2020,
    start: function() {
        console.log("Engine started!");
    }
};

// Dot notation
console.log(car.brand); // Toyota
car.year = 2021;

// Bracket notation (useful for dynamic keys or keys with spaces/special chars)
console.log(car["model"]); // Camry
let prop = "brand";
console.log(car[prop]); // Toyota

car.start(); // Engine started!
```

### 5.2 Common Methods & ES6+ Features

*   **`Object.keys()` / `Object.values()` / `Object.entries()`**:
    ```javascript
    console.log(Object.keys(car));   // ["brand", "model", "year", "start"]
    console.log(Object.values(car)); // ["Toyota", "Camry", 2021, ƒ]
    console.log(Object.entries(car)); // [["brand", "Toyota"], ["model", "Camry"], ...]
    ```
*   **Spread Syntax (`...`)**: Copy properties, merge objects.
    ```javascript
    const newCar = { ...car, color: "blue" }; // Copy car and add color
    const merged = { a: 1, b: 2, ...{ c: 3, d: 4 } }; // { a: 1, b: 2, c: 3, d: 4 }
    ```
*   **Object Destructuring (ES6+)**: Extract values from objects into variables.
    ```javascript
    const { brand, model } = car;
    console.log(brand); // Toyota
    console.log(model); // Camry
    ```

---

## 6. Strings

### 6.1 Basic Operations

```javascript
let greeting = "Hello, World!";
console.log(greeting.length); // 13
console.log(greeting[0]);     // H
console.log(greeting.toUpperCase()); // HELLO, WORLD!```

### 6.2 Template Literals (ES6+)

Use backticks (`` ` ``) for multi-line strings and embedded expressions.

```javascript
const name = "Jane";
const message = `Hello, ${name}!
Welcome to JavaScript.`;
console.log(message);
// Output:
// Hello, Jane!
// Welcome to JavaScript.
```

### 6.3 Common Methods

*   **`substring(start, end)` / `slice(start, end)`**: Extract a portion of a string.
    ```javascript
    console.log(greeting.substring(0, 5)); // Hello
    console.log(greeting.slice(-6));       // World!
    ```
*   **`split(separator)`**: Split a string into an array of substrings.
    ```javascript
    const words = greeting.split(" "); // ["Hello,", "World!"]
    ```
*   **`join(separator)`**: (Array method, often used with `split`) Joins array elements into a string.
    ```javascript
    const joined = words.join("-"); // "Hello,-World!"
    ```
*   **`replace(old, new)` / `replaceAll(old, new)`**: Replace occurrences.
    ```javascript
    console.log(greeting.replace("World", "JS")); // Hello, JS!
    ```
*   **`trim()`**: Remove whitespace from both ends.
    ```javascript
    "  some text  ".trim(); // "some text"
    ```
*   **`startsWith()` / `endsWith()`**: Check if a string begins/ends with another.
    ```javascript
    console.log(greeting.startsWith("Hello")); // true
    ```

---

## 7. Error Handling (`try...catch`)

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

---

## 8. Asynchronous JavaScript (Promises & Async/Await)

Handling operations that take time (e.g., network requests, file I/O).

### 8.1 Callbacks (Older method, prone to "callback hell")

```javascript
// setTimeout(function() {
//     console.log("Delayed message after 1 second.");
// }, 1000);
```

### 8.2 Promises (ES6+)

Represent the eventual completion (or failure) of an asynchronous operation.

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

### 8.3 Async/Await (ES8+)

Syntactic sugar built on Promises, making asynchronous code look and behave more like synchronous code.

```javascript
async function getGreeting() {
    try {
        const response = await fetch('https://api.example.com/greeting'); // 'await' pauses execution until Promise resolves
        const data = await response.json();
        console.log(data.message);
    } catch (error) {
        console.error("Error fetching greeting:", error);
    }
}

// getGreeting(); // Call the async function
```
*Note: `await` can only be used inside an `async` function.*

---

## 9. Browser Interaction (Client-Side JS)

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
    // const myButton = document.querySelector("#myButton");
    // if (myButton) {
    //     myButton.addEventListener("click", () => {
    //         console.log("Button clicked!");
    //     });
    // }
    ```
*   **`document.getElementById()`**: Selects an element by its ID.
    ```javascript
    // const header = document.getElementById("main-header");
    // if (header) {
    //     header.textContent = "New Header Text";
    // }
    ```
*   **`localStorage` / `sessionStorage`**: Store data in the browser.
    ```javascript
    // localStorage.setItem("username", "Alice");
    // let storedUser = localStorage.getItem("username");
    // localStorage.removeItem("username");
    // localStorage.clear();
    ```

---

## 10. Modern JavaScript (ES6+ Highlights)

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
*   **Classes**: (Syntactic sugar over prototypes)
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
*   **Modules (`import`/`export`)**: For organizing code into separate files.
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

---

## 11. Useful Global Objects and Functions

*   **`Math`**: For mathematical operations.
    ```javascript
    console.log(Math.PI);
    console.log(Math.random());    // Random number between 0 (inclusive) and 1 (exclusive)
    console.log(Math.floor(4.7));  // 4
    console.log(Math.ceil(4.2));   // 5
    console.log(Math.max(1, 5, 2)); // 5
    ```
*   **`Date`**: For working with dates and times.
    ```javascript
    const now = new Date();
    console.log(now.getFullYear());
    console.log(now.toDateString());
    ```
*   **`JSON`**: For parsing and stringifying JSON data.
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
