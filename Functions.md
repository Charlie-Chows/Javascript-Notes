# Functions in JavaScript

## Functions & Variable Environment
- Functions always store it lexical environment at the time of creation. Even though that function is not available in memory phase, it can access lexical environemnt data.

```js
var x = 10; // Global variable x initialized to 10

a(); 
b(); 
console.log(x); // Prints 100 (global x was updated by function a)

function a() {
    x = 100; // No 'var', so this updates the global x to 100
    console.log(x); // Prints 100
}

function c() {
    console.log(x); // Prints 100 - bcoz x is not available in functional memory so it will get access from global or parent scope
}

function b() {
    var x = 1000; // Local variable x (function-scoped), does NOT affect global x
    console.log(x); // Prints 1000
}

{
    var x = 2000; // 'var' is not block scoped — this REPLACES the global x
    console.log(x); // Prints 2000
}

console.log(x); // Prints 2000 (global x is now updated again by the block)

```

**why x is not updated when `var x = 100` in `b()` in globally ?**
- Every time a function is called a new Execution Context is created.
- Each Execution Context has its own memory space (called Variable Environment), so it wont effect by global scope variable unless we did not declare `var` like `a()`.
- Global variable remain unchanged.

## Function Declaration ( a.k.a function statement ) vs Expression

**Function Declaration**
```js

function a() {
    console.log("Hello World");
}
a();
```
**Function Expression**
```js
var b = function () {
    console.log("Hello World");
}
b();


var c = function greet(n) {
    console.log("Hello");
    greet(n-1); // works inside useful for recursive calls 
     throw new Error("Oops!"); // throw error of greet name instead of anonymous 
};

c(); // works 
greet() // wont work on outside calls
```
### 📊 Comparison Table: Function Declaration vs Function Expression

| Feature                  | Function Declaration       | Function Expression          |
|--------------------------|----------------------------|------------------------------|
| Syntax                   | `function a() {}`          | `var a = function() {}`      |
| Hoisting                 | ✅ Fully hoisted            | ❌ Only variable is hoisted   |
| Can call before defined? | ✅ Yes                     | ❌ No                         |
| Named?                   | ✅ Has name                | ✅ Optional (can be anonymous) |


## Anonymous & Named Functions

**Anonymous Function**
```js
var add = function(a, b) {
    return a + b;
};

```
**Named Function**
```js

function a() {          // noraml function also named function
    console.log("hello");
}

var c = function greet(n) {
    console.log("Hello");
    greet(n-1); // works inside useful for recursive calls 
     throw new Error("Oops!"); // throw error of greet name instead of anonymous 
};

c(); // works 
greet() // wont work on outside calls
```

### 📊 Function Types: Named vs Anonymous

| Example                        | Type                        | Named?     |
|-------------------------------|-----------------------------|------------|
| `function a() {}`             | Function Declaration         | ✅ Yes      |
| `var b = function() {}`       | Anonymous Function Expression | ❌ No       |
| `var c = function greet() {}` | Named Function Expression     | ✅ Yes (internally) |


## Function Parameters vs arguments 
```js
function sum ( param1, param2 ) {   // parameters
    return param1 + param2;
}

sum( 7, 4 );    // arguments
```

## Arrow Functions	
```js
const add = (a, b) => {
    return a + b;
};
console.log(add(2, 3)); // Output: 5

```

## First-Class Functions

- Functions can be treated as first-class citizens. This means functions can be :
    - Assigned to variables
    - Passed as arguments to other functions
    - Returned from other functions
    - Stored in data structures like arrays or objects

- These features makes js functional programming friendly that's why we called as first-class functions or citizens

**Assigned to variables**
```js
var greet = function() {
    console.log("Hello!");
};

greet(); // Output: Hello!

```
**Passed as arguments to other functions**
```js
var greet = function() {
    console.log("Hello!");
};

function callFunction(fn) {
    fn(); // Calling the function passed as an argument
}

callFunction(greet); // Output: Hello!

```

**Returned from other functions**
```js
function outer() {
    return function inner() {
        console.log("Inner Function");
    };
}

var returnedFunction = outer();
returnedFunction(); // Output: Inner Function

```
**Stored in data structures like arrays or objects**
```js
var functionsArray = [
    function() { console.log("First Function"); },
    function() { console.log("Second Function"); }
];

functionsArray[0](); // Output: First Function
functionsArray[1](); // Output: Second Function

```

## Return Values + Early Return Pattern

**Basic Return Example**
```js
function add(a, b) {
    return a + b; // Returns the sum of a and b
}

const result = add(3, 4);
console.log(result); // Output: 7

```

**Early Return Pattern**
- The early return pattern is a programming technique used to simplify the code and avoid unnecessary levels of indentation.
- espicially while writing edge case it will be more helpful

*Without Early Return*
```js
function processOrder(order) {
    if (order) {
        if (order.items.length > 0) {
            console.log("Processing order...");
            // More processing logic here...
        } else {
            console.log("Order has no items.");
        }
    } else {
        console.log("Invalid order.");
    }
}

```

*with Early Return*
```js
function processOrder(order) {
    if (!order) {
        console.log("Invalid order.");
        return; // Early return if order is invalid
    }

    if (order.items.length === 0) {
        console.log("Order has no items.");
        return; // Early return if no items in the order
    }

    console.log("Processing order...");
    // More processing logic here...
}

```
*One more example*
```js
function checkDiscount(user, totalAmount) {
    if (!user || totalAmount <= 0) {
        console.log("Invalid data for discount.");
        return; // Early return for invalid input
    }

    if (user.isMember) {
        console.log("Discount applied: 20%");
        return totalAmount * 0.8; // Apply 20% discount for members
    }

    if (totalAmount > 100) {
        console.log("Discount applied: 10%");
        return totalAmount * 0.9; // Apply 10% discount for orders over 100
    }

    console.log("No discount applied.");
    return totalAmount; // Return the total without discount
}

console.log(checkDiscount({ isMember: true }, 200)); // Output: Discount applied: 20%, 160

```
*DSA example*
```js
function findMax(arr) {
    if (arr.length === 0) {
        return null; // Early return for empty array
    }

    let max = arr[0];
    for (let i = 1; i < arr.length; i++) {
        if (arr[i] > max) {
            max = arr[i];
        }
    }
    return max;
}

```

## Pure vs Impure Functions

**Pure Function** 
- it always return same output if input is same no side effects
```js
function add(a, b) {
    return a + b;  // Always returns the sum of a and b, no side effects
}

console.log(add(2, 3));  // Output: 5
console.log(add(2, 3));  // Output: 5 (consistent result)

```

**Impure Functions**
- it will modify external state , no gaurentee for same output even the input is same.
```js
let counter = 0;

function increment() {
    counter += 1;  // Side effect: modifies external variable
    return counter;
}

console.log(increment());  // Output: 1
console.log(increment());  // Output: 2 (output changes based on external state)

```

| Feature                        | **Pure Function** (`add`)                             | **Impure Function** (`increment`)                    |
|--------------------------------|-------------------------------------------------------|------------------------------------------------------|
| **State Modification**         | No modification of external state                    | Modifies external state (`counter`)                  |
| **Determinism**                | Always returns the same output for the same input    | Output varies based on external state                |
| **Side Effects**               | No side effects                                      | Has side effects (modifies global variable)          |
| **Mutability**                 | Doesn’t mutate anything (pure calculation)           | Mutates an external variable (`counter`)             |
| **Example Behavior**           | `add(2, 3)` will always return `5`                   | `increment()` will return `1`, `2`, `3`, ...         |


## Higher Order Functions

**HOC :** A function that takes another function as an argument, or returns a function, or does both, is called a Higher-Order Function.

 **Without HOC**
 ```js
 
const radius = [ 3, 1, 2, 4 ];

const calculateArea = function ( radius ) {
    const output = [];
    for ( let i = 0; i < radius.length; i++ ) {
        output.push(Math.PI * radius[i] * radius[i] );
    }
    return output;
}

console.log(calculateArea(radius));


const calculateCircumfrence = function ( radius ) {
    const output = [];
    for ( let i = 0; i < radius.length; i++ ) {
        output.push( 2 * Math.PI * radius[i]);
    }
    return output;
}

console.log( calculateCircumfrence(radius));

const calculateDiameter = function ( radius ) {
    const output = [];
    for ( let i = 0; i < radius.length; i++ ) {
        output.push( 2 * radius[i]);
    }
    return output;
}

console.log( calculateDiameter(radius));
 ```

 **Wth HOC**
 ```js
 
const radius = [ 3, 1, 2, 4 ];

const area = function ( radius ) {
    return Math.PI * radius* radius;
}

const circumFrence = function ( radius ) {
    return 2 * Math.PI * radius;
}

const diameter = function ( radius ) {
    return 2 * radius;
}

// HOC - receiving func as arg
const calculate = function ( arr, formula ) {
    const output = [];
    for ( let i = 0; i < arr.length; i++ ) {
        output.push( formula(arr[i]));
    }
    return output;
}

console.log(calculate( radius, area ));
console.log(calculate( radius, circumFrence ));
console.log(calculate( radius, diameter ));
 ```

## Callback Functions

```js

setTimeout(function(){
    console.log("Timeout");
},7000)

function x ( y ) {
    console.log("x");
    y();
}

// we are passing y func as a callback func to x
x( function y() {
    console.log("y");
})

// output 
// x 
// y 
// timeout ( after 7 sec )

```

**SetTimeout**
```js
// Using a callback with setTimeout
setTimeout(function() {
  console.log("This runs after 2 seconds");
}, 2000);

```

**Api calls**
```js
function fetchData(url, callback) {
  fetch(url)
    .then(response => response.json())
    .then(data => callback(data));  // Call the callback with data
}

fetchData('https://jsonplaceholder.typicode.com/posts', function(data) {
  console.log(data);
});

```

**Event Handling**

```js
// Handling a button click
const button = document.querySelector('button');

button.addEventListener('click', function() {
  alert('Button clicked!');
});
```

**Example**
```js
// A function that accepts a callback
function greet(name, callback) {
  console.log(`Hello, ${name}`);
  callback(); // Calling the callback function
}

// A simple callback function that is passed to greet()
function sayGoodbye() {
  console.log('Goodbye!');
}

// Passing sayGoodbye as a callback to greet()
greet('Alice', sayGoodbye);

// Output:
// Hello, Alice
// Goodbye!

```

## Immediately Invoked Function Expression (IIFE)
- An IIFE is a function that is defined and executed immediately after its creation. It is a common pattern used to create a private scope, often to avoid polluting the global namespace.

```js

// function
(function() {
    // Code here is executed immediately
})();

// arrow function 
(() => {
    // Code here is executed immediately
})();

```
> **Key Points**
> - Immediately Invoked: The function is executed as soon as it is defined.
> - Function Expression: The function is treated as an expression (not a declaration).
>- Encapsulation: It creates a new scope, preventing variables from leaking into the global scope.

```js
(function() {
    var message = "Hello, I am an IIFE!";
    console.log(message);
})();
// Output: "Hello, I am an IIFE!"

// message is not accessible outside the IIFE
console.log(message);  // ReferenceError: message is not defined

```

**IIFE with params & args**
```js
(function(name) {
    console.log("Hello, " + name);
})("John");  // Output: Hello, John

```

### Few React Example using IIFE

**API calls in useEffect**
```jsx

import React, { useState, useEffect } from "react";

const FetchDataWithIIFE = () => {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    (async () => {
      try {
        const response = await fetch("https://jsonplaceholder.typicode.com/posts");
        if (!response.ok) {
          throw new Error("Failed to fetch data");
        }
        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    })(); // IIFE - Immediately Invoked Function Expression
  }, []);

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error}</p>;

  return (
    <div>
      <h1>Fetched Posts</h1>
      <ul>
        {data.map((post) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  );
};

export default FetchDataWithIIFE;

```

**Handling Form Submission**
```jsx
const handleSubmit = (e) => {
  e.preventDefault();
  (async () => {
    const result = await submitForm(formData);
    if (result.success) {
      alert('Form submitted successfully');
    } else {
      alert('Error submitting form');
    }
  })();
};

```

