# JavaScript Tutorial for Beginners

## Table of Contents
1. [What is JavaScript?](#what-is-javascript)
2. [History of JavaScript](#history-of-javascript)
3. [Use Cases](#use-cases)
4. [Getting Started](#getting-started)
5. [Basic Syntax](#basic-syntax)
6. [Variables](#variables)
7. [Data Types](#data-types)
8. [Operators](#operators)
9. [Control Flow](#control-flow)
10. [Functions](#functions)
11. [Arrays](#arrays)
12. [Objects](#objects)
13. [Practical Examples](#practical-examples)

---

## What is JavaScript?

JavaScript is a **high-level, interpreted programming language** that is one of the core technologies of the World Wide Web, alongside HTML and CSS. It allows you to create dynamic, interactive content on websites and has evolved to power backend servers, mobile apps, desktop applications, and even IoT devices.

### Key Characteristics:
- **Interpreted** - Runs directly without compilation
- **Dynamically Typed** - Variable types are determined at runtime
- **Multi-paradigm** - Supports object-oriented, functional, and imperative programming
- **Single-threaded** - Executes one command at a time (but uses asynchronous patterns)

---

## History of JavaScript

### Timeline:

**1995** - Created by **Brendan Eich** at Netscape in just **10 days**. Originally called "Mocha," then "LiveScript," and finally "JavaScript" (for marketing reasons, riding on Java's popularity).

**1996** - Netscape submitted JavaScript to ECMA International for standardization.

**1997** - **ECMAScript 1** released as the official standard.

**2009** - **Node.js** created by Ryan Dahl, allowing JavaScript to run on servers (backend).

**2015** - **ES6/ES2015** released with major updates: arrow functions, classes, modules, promises, let/const, and more. This was a massive modernization.

**2016-Present** - Yearly ECMAScript updates (ES2016, ES2017, etc.) with incremental improvements.

### Interesting Facts:
- JavaScript has **nothing to do with Java** - the naming was purely marketing
- It was created in 10 days but has become one of the most popular languages
- Originally designed for simple form validation, now powers entire applications

---

## Use Cases

JavaScript is incredibly versatile. Here's where it's used:

### 1. **Frontend Web Development**
- Interactive websites (animations, form validation, dynamic content)
- Single Page Applications (SPAs) using React, Vue, Angular
- Examples: Facebook, Gmail, Twitter feeds

### 2. **Backend Development** (Your focus!)
- Server-side applications with Node.js
- RESTful APIs
- Real-time applications (chat, notifications)
- Examples: Netflix, PayPal, LinkedIn backends

### 3. **Mobile App Development**
- Cross-platform apps with React Native, Ionic
- Examples: Instagram, Facebook app, Discord

### 4. **Desktop Applications**
- Electron framework (VS Code, Slack, Discord desktop)

### 5. **Game Development**
- Browser games, 2D/3D games with libraries like Phaser, Three.js

### 6. **IoT & Hardware**
- Raspberry Pi, Arduino programming with Johnny-Five

### 7. **Automation & Scripting**
- Browser automation, task automation (like your n8n workflows!)
- Server automation, deployment scripts

### 8. **Machine Learning & AI**
- TensorFlow.js for browser-based ML

---

## Getting Started

### Running JavaScript

You can run JavaScript in three main ways:

1. **Browser Console** (Easiest to start)
   - Open Chrome/Firefox
   - Press F12 or Ctrl+Shift+I
   - Go to "Console" tab
   - Type JavaScript and hit Enter

2. **HTML File** (For web projects)
   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My First JavaScript</title>
   </head>
   <body>
       <h1>Hello World</h1>
       <script>
           console.log("Hello from JavaScript!");
       </script>
   </body>
   </html>
   ```

3. **Node.js** (For backend - what you're learning!)
   - In Termux: `node myfile.js`
   - Or interactive mode: just type `node` and press Enter

---

## Basic Syntax

### Comments
```javascript
// This is a single-line comment

/*
This is a
multi-line comment
*/
```

### Statements
```javascript
// Each statement ends with a semicolon (optional but recommended)
console.log("Hello World");
let x = 5;
```

### Console Output
```javascript
console.log("Hello World");  // Print to console
console.error("Error message");  // Print error
console.warn("Warning message");  // Print warning
```

---

## Variables

Variables are containers for storing data values.

### Three Ways to Declare Variables:

```javascript
// var - old way (avoid in modern code)
var name = "Tom";

// let - modern way for values that change
let age = 30;
age = 31;  // Can be reassigned

// const - modern way for values that DON'T change
const PI = 3.14159;
// PI = 3.14;  // ERROR! Cannot reassign const
```

### Naming Rules:
- Must start with letter, $, or _
- Can contain letters, numbers, $, _
- Case sensitive (myVar ≠ MyVar)
- Cannot use reserved keywords (let, function, if, etc.)

```javascript
// Good variable names
let firstName = "Tom";
let user_age = 30;
let $price = 99.99;
let _private = true;

// Bad variable names (will cause errors)
// let 1stName = "Tom";  // Cannot start with number
// let first-name = "Tom";  // Cannot use hyphens
// let let = 5;  // Cannot use reserved keyword
```

### Naming Conventions:
```javascript
// camelCase - most common for variables and functions
let firstName = "Tom";
let totalPrice = 100;

// PascalCase - used for classes
class UserAccount {}

// UPPER_SNAKE_CASE - used for constants
const MAX_LOGIN_ATTEMPTS = 3;
const API_KEY = "abc123";
```

---

## Data Types

JavaScript has **7 primitive types** and **objects**:

### 1. String
```javascript
let name = "Tom";
let message = 'Hello World';
let template = `My name is ${name}`;  // Template literal (ES6)

// String methods
let text = "JavaScript";
console.log(text.length);        // 10
console.log(text.toLowerCase()); // "javascript"
console.log(text.toUpperCase()); // "JAVASCRIPT"
console.log(text.charAt(0));     // "J"
console.log(text.slice(0, 4));   // "Java"
```

### 2. Number
```javascript
let age = 30;
let price = 99.99;
let negative = -42;

// Math operations
let sum = 10 + 5;      // 15
let difference = 10 - 5; // 5
let product = 10 * 5;   // 50
let quotient = 10 / 5;  // 2
let remainder = 10 % 3; // 1 (modulo)

// Special numbers
let infinity = Infinity;
let notANumber = NaN;
```

### 3. Boolean
```javascript
let isActive = true;
let hasPermission = false;

// Boolean from comparisons
let isAdult = age >= 18;  // true or false
```

### 4. Undefined
```javascript
let x;  // Declared but not assigned
console.log(x);  // undefined
```

### 5. Null
```javascript
let empty = null;  // Intentionally empty
```

### 6. BigInt (for very large numbers)
```javascript
let bigNumber = 9007199254740991n;
```

### 7. Symbol (advanced, used for unique identifiers)
```javascript
let id = Symbol("id");
```

### Checking Types
```javascript
console.log(typeof "Hello");    // "string"
console.log(typeof 42);         // "number"
console.log(typeof true);       // "boolean"
console.log(typeof undefined);  // "undefined"
console.log(typeof null);       // "object" (JavaScript quirk!)
```

---

## Operators

### Arithmetic Operators
```javascript
let x = 10;
let y = 3;

console.log(x + y);  // 13 (addition)
console.log(x - y);  // 7 (subtraction)
console.log(x * y);  // 30 (multiplication)
console.log(x / y);  // 3.333... (division)
console.log(x % y);  // 1 (remainder/modulo)
console.log(x ** y); // 1000 (exponentiation: 10^3)

// Increment/Decrement
let count = 5;
count++;  // count = count + 1 (now 6)
count--;  // count = count - 1 (now 5)
```

### Assignment Operators
```javascript
let x = 10;
x += 5;  // x = x + 5 (now 15)
x -= 3;  // x = x - 3 (now 12)
x *= 2;  // x = x * 2 (now 24)
x /= 4;  // x = x / 4 (now 6)
```

### Comparison Operators
```javascript
let a = 5;
let b = "5";

// Equality (type coercion)
console.log(a == b);   // true (compares values, converts types)
console.log(a != b);   // false

// Strict equality (NO type coercion - RECOMMENDED)
console.log(a === b);  // false (different types)
console.log(a !== b);  // true

// Comparison
console.log(5 > 3);    // true
console.log(5 < 3);    // false
console.log(5 >= 5);   // true
console.log(5 <= 4);   // false
```

### Logical Operators
```javascript
let age = 25;
let hasLicense = true;

// AND - both must be true
console.log(age >= 18 && hasLicense);  // true

// OR - at least one must be true
console.log(age >= 21 || hasLicense);  // true

// NOT - inverts boolean
console.log(!hasLicense);  // false
```

---

## Control Flow

### If Statements
```javascript
let temperature = 25;

if (temperature > 30) {
    console.log("It's hot!");
} else if (temperature > 20) {
    console.log("It's warm");
} else {
    console.log("It's cold");
}
```

### Ternary Operator (shorthand if/else)
```javascript
let age = 20;
let status = age >= 18 ? "Adult" : "Minor";
console.log(status);  // "Adult"
```

### Switch Statement
```javascript
let day = "Monday";

switch(day) {
    case "Monday":
        console.log("Start of work week");
        break;
    case "Friday":
        console.log("Almost weekend!");
        break;
    case "Saturday":
    case "Sunday":
        console.log("Weekend!");
        break;
    default:
        console.log("Midweek day");
}
```

### Loops

#### For Loop
```javascript
// Count from 0 to 4
for (let i = 0; i < 5; i++) {
    console.log(i);
}
// Output: 0, 1, 2, 3, 4
```

#### While Loop
```javascript
let count = 0;
while (count < 5) {
    console.log(count);
    count++;
}
```

#### Do-While Loop
```javascript
let x = 0;
do {
    console.log(x);
    x++;
} while (x < 5);
```

#### For...of Loop (for arrays)
```javascript
let fruits = ["apple", "banana", "orange"];
for (let fruit of fruits) {
    console.log(fruit);
}
```

---

## Functions

Functions are reusable blocks of code.

### Function Declaration
```javascript
function greet(name) {
    return "Hello, " + name + "!";
}

console.log(greet("Tom"));  // "Hello, Tom!"
```

### Function Expression
```javascript
const greet = function(name) {
    return "Hello, " + name + "!";
};
```

### Arrow Functions (ES6 - Modern!)
```javascript
// Concise syntax
const greet = (name) => {
    return "Hello, " + name + "!";
};

// Even shorter (implicit return)
const greet = name => "Hello, " + name + "!";

// Multiple parameters
const add = (a, b) => a + b;
console.log(add(5, 3));  // 8
```

### Parameters and Arguments
```javascript
// Default parameters
function greet(name = "Guest") {
    return `Hello, ${name}!`;
}

console.log(greet());      // "Hello, Guest!"
console.log(greet("Tom")); // "Hello, Tom!"

// Multiple parameters
function calculateTotal(price, tax, discount = 0) {
    return price + tax - discount;
}

console.log(calculateTotal(100, 10, 5));  // 105
```

### Return Values
```javascript
function multiply(a, b) {
    return a * b;
    console.log("This never runs");  // Code after return is ignored
}

let result = multiply(5, 3);
console.log(result);  // 15
```

---

## Arrays

Arrays store multiple values in a single variable.

### Creating Arrays
```javascript
let fruits = ["apple", "banana", "orange"];
let numbers = [1, 2, 3, 4, 5];
let mixed = [1, "hello", true, null];

// Array constructor
let items = new Array("a", "b", "c");
```

### Accessing Elements
```javascript
let fruits = ["apple", "banana", "orange"];

console.log(fruits[0]);  // "apple" (first element)
console.log(fruits[2]);  // "orange"
console.log(fruits.length);  // 3
```

### Modifying Arrays
```javascript
let fruits = ["apple", "banana"];

// Add to end
fruits.push("orange");
console.log(fruits);  // ["apple", "banana", "orange"]

// Remove from end
let last = fruits.pop();
console.log(last);    // "orange"
console.log(fruits);  // ["apple", "banana"]

// Add to beginning
fruits.unshift("mango");
console.log(fruits);  // ["mango", "apple", "banana"]

// Remove from beginning
let first = fruits.shift();
console.log(first);   // "mango"
console.log(fruits);  // ["apple", "banana"]
```

### Array Methods
```javascript
let numbers = [1, 2, 3, 4, 5];

// Find index
console.log(numbers.indexOf(3));  // 2

// Check if includes
console.log(numbers.includes(4));  // true

// Join into string
console.log(numbers.join("-"));  // "1-2-3-4-5"

// Slice (copy portion)
let slice = numbers.slice(1, 4);
console.log(slice);  // [2, 3, 4]

// Splice (remove/insert)
let removed = numbers.splice(2, 1);  // Remove 1 element at index 2
console.log(numbers);  // [1, 2, 4, 5]
console.log(removed);  // [3]
```

### Array Iteration
```javascript
let fruits = ["apple", "banana", "orange"];

// forEach
fruits.forEach(function(fruit, index) {
    console.log(index + ": " + fruit);
});

// map (transform each element)
let lengths = fruits.map(fruit => fruit.length);
console.log(lengths);  // [5, 6, 6]

// filter (keep elements that match condition)
let longFruits = fruits.filter(fruit => fruit.length > 5);
console.log(longFruits);  // ["banana", "orange"]

// find (get first match)
let found = fruits.find(fruit => fruit.startsWith("b"));
console.log(found);  // "banana"

// reduce (combine all elements)
let numbers = [1, 2, 3, 4, 5];
let sum = numbers.reduce((total, num) => total + num, 0);
console.log(sum);  // 15
```

---

## Objects

Objects store collections of key-value pairs.

### Creating Objects
```javascript
// Object literal (most common)
let person = {
    firstName: "Tom",
    lastName: "Smith",
    age: 30,
    isActive: true
};

// Accessing properties
console.log(person.firstName);     // "Tom" (dot notation)
console.log(person["lastName"]);   // "Smith" (bracket notation)
```

### Modifying Objects
```javascript
let person = {
    name: "Tom",
    age: 30
};

// Add property
person.email = "tom@example.com";

// Modify property
person.age = 31;

// Delete property
delete person.email;

console.log(person);  // { name: "Tom", age: 31 }
```

### Methods (Functions in Objects)
```javascript
let person = {
    firstName: "Tom",
    lastName: "Smith",
    
    // Method
    fullName: function() {
        return this.firstName + " " + this.lastName;
    },
    
    // Shorthand method (ES6)
    greet() {
        return `Hello, I'm ${this.firstName}`;
    }
};

console.log(person.fullName());  // "Tom Smith"
console.log(person.greet());     // "Hello, I'm Tom"
```

### Object Iteration
```javascript
let person = {
    name: "Tom",
    age: 30,
    city: "Auckland"
};

// Get keys
console.log(Object.keys(person));    // ["name", "age", "city"]

// Get values
console.log(Object.values(person));  // ["Tom", 30, "Auckland"]

// Get entries (key-value pairs)
console.log(Object.entries(person));
// [["name", "Tom"], ["age", 30], ["city", "Auckland"]]

// Loop through object
for (let key in person) {
    console.log(key + ": " + person[key]);
}
```

### Nested Objects
```javascript
let user = {
    name: "Tom",
    address: {
        street: "123 Main St",
        city: "Auckland",
        country: "New Zealand"
    },
    hobbies: ["kettlebells", "coding", "stoicism"]
};

console.log(user.address.city);      // "Auckland"
console.log(user.hobbies[0]);        // "kettlebells"
```

---

## Practical Examples

### Example 1: Temperature Converter
```javascript
function celsiusToFahrenheit(celsius) {
    return (celsius * 9/5) + 32;
}

function fahrenheitToCelsius(fahrenheit) {
    return (fahrenheit - 32) * 5/9;
}

console.log(celsiusToFahrenheit(25));   // 77
console.log(fahrenheitToCelsius(77));   // 25
```

### Example 2: Simple Calculator
```javascript
function calculator(num1, num2, operation) {
    switch(operation) {
        case 'add':
            return num1 + num2;
        case 'subtract':
            return num1 - num2;
        case 'multiply':
            return num1 * num2;
        case 'divide':
            return num2 !== 0 ? num1 / num2 : "Cannot divide by zero";
        default:
            return "Invalid operation";
    }
}

console.log(calculator(10, 5, 'add'));       // 15
console.log(calculator(10, 5, 'multiply'));  // 50
console.log(calculator(10, 0, 'divide'));    // "Cannot divide by zero"
```

### Example 3: Grade Calculator
```javascript
function calculateGrade(score) {
    if (score >= 90) return 'A';
    if (score >= 80) return 'B';
    if (score >= 70) return 'C';
    if (score >= 60) return 'D';
    return 'F';
}

console.log(calculateGrade(95));  // "A"
console.log(calculateGrade(72));  // "C"
console.log(calculateGrade(55));  // "F"
```

### Example 4: Array Statistics
```javascript
function getStats(numbers) {
    let sum = numbers.reduce((total, num) => total + num, 0);
    let average = sum / numbers.length;
    let max = Math.max(...numbers);
    let min = Math.min(...numbers);
    
    return {
        sum: sum,
        average: average,
        max: max,
        min: min,
        count: numbers.length
    };
}

let scores = [85, 92, 78, 90, 88];
console.log(getStats(scores));
// { sum: 433, average: 86.6, max: 92, min: 78, count: 5 }
```

### Example 5: Todo List (Object-Oriented)
```javascript
let todoList = {
    tasks: [],
    
    addTask(task) {
        this.tasks.push({
            id: Date.now(),
            text: task,
            completed: false
        });
    },
    
    completeTask(id) {
        let task = this.tasks.find(t => t.id === id);
        if (task) {
            task.completed = true;
        }
    },
    
    removeTask(id) {
        this.tasks = this.tasks.filter(t => t.id !== id);
    },
    
    listTasks() {
        return this.tasks.map(task => 
            `${task.completed ? '[✓]' : '[ ]'} ${task.text}`
        ).join('\n');
    }
};

// Usage
todoList.addTask("Learn JavaScript");
todoList.addTask("Build a project");
todoList.addTask("Practice coding");

console.log(todoList.listTasks());
// [ ] Learn JavaScript
// [ ] Build a project
// [ ] Practice coding

todoList.completeTask(todoList.tasks[0].id);
console.log(todoList.listTasks());
// [✓] Learn JavaScript
// [ ] Build a project
// [ ] Practice coding
```

### Example 6: Blood Sugar Tracker (Relevant to you!)
```javascript
let bloodSugarLog = {
    readings: [],
    
    addReading(value, time, notes = "") {
        this.readings.push({
            value: value,
            time: time,
            notes: notes,
            date: new Date().toLocaleDateString()
        });
    },
    
    getAverage() {
        let sum = this.readings.reduce((total, reading) => total + reading.value, 0);
        return (sum / this.readings.length).toFixed(1);
    },
    
    getHighReadings(threshold = 140) {
        return this.readings.filter(r => r.value > threshold);
    },
    
    getSummary() {
        let values = this.readings.map(r => r.value);
        return {
            count: this.readings.length,
            average: this.getAverage(),
            highest: Math.max(...values),
            lowest: Math.min(...values)
        };
    }
};

// Usage
bloodSugarLog.addReading(110, "morning", "fasting");
bloodSugarLog.addReading(145, "afternoon", "after lunch");
bloodSugarLog.addReading(98, "evening", "before dinner");

console.log(bloodSugarLog.getSummary());
// { count: 3, average: '117.7', highest: 145, lowest: 98 }

console.log(bloodSugarLog.getHighReadings());
// [{ value: 145, time: 'afternoon', notes: 'after lunch', date: '...' }]
```

### Example 7: Workout Logger
```javascript
function workoutLogger() {
    let workouts = [];
    
    return {
        logWorkout(exercise, weight, reps, sets) {
            workouts.push({
                exercise: exercise,
                weight: weight,
                reps: reps,
                sets: sets,
                date: new Date().toLocaleDateString(),
                totalVolume: weight * reps * sets
            });
        },
        
        getWorkoutHistory(exercise) {
            return workouts.filter(w => w.exercise === exercise);
        },
        
        getTotalVolume() {
            return workouts.reduce((total, w) => total + w.totalVolume, 0);
        },
        
        getProgress(exercise) {
            let history = this.getWorkoutHistory(exercise);
            if (history.length < 2) return "Need more data";
            
            let first = history[0].totalVolume;
            let last = history[history.length - 1].totalVolume;
            let improvement = ((last - first) / first * 100).toFixed(1);
            
            return `${improvement}% improvement`;
        }
    };
}

// Usage
let myWorkouts = workoutLogger();
myWorkouts.logWorkout("Double Kettlebell Press", 20, 5, 5);
myWorkouts.logWorkout("Double Kettlebell Press", 20, 6, 5);
myWorkouts.logWorkout("Kettlebell Swing", 24, 10, 5);

console.log(myWorkouts.getTotalVolume());  // Total kg lifted
console.log(myWorkouts.getProgress("Double Kettlebell Press"));
```

---

## Next Steps

After mastering these basics, you should explore:

1. **DOM Manipulation** - Interacting with web pages (if doing frontend)
2. **Async JavaScript** - Promises, async/await (critical for Node.js!)
3. **ES6+ Features** - Destructuring, spread operator, modules
4. **Error Handling** - try/catch blocks
5. **JSON** - Working with data
6. **Node.js Fundamentals** - File system, modules, npm
7. **Express.js** - Building web servers (your backend roadmap!)

### Practice Projects:
- Build a command-line calculator
- Create a todo list app
- Build a simple API with Express
- Automate tasks with Node.js scripts

### Resources:
- freeCodeCamp.org
- JavaScript.info
- MDN Web Docs
- Eloquent JavaScript (free book)

---

## Tips for Learning:

1. **Code every day** - Even 15 minutes in Termux
2. **Type, don't copy** - Muscle memory matters
3. **Break things** - Experimentation is learning
4. **Build projects** - Apply what you learn immediately
5. **Read error messages** - They tell you what's wrong
6. **Use console.log()** - Debug and understand your code

---

**Good luck on your JavaScript journey, Tom!** 🚀

Remember: JavaScript is a tool for building things. Focus on understanding concepts, practice regularly, and most importantly - build real projects that interest you (automation, security tools, fitness trackers, etc.).
