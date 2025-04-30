# Hoisting in Javascript

**Definition** : Hoisting is the behavior in JavaScript where we can access variables or functions during the memory allocation phase (i.e., before actual execution).
- `var` declarations are hoisted and initialized with `undefined`.
- Function declarations are fully hoisted with their definitions.
- `let` and `const` are hoisted too, but remain uninitialized — accessing them before their declaration results in a `ReferenceError` due to the Temporal Dead Zone (TDZ).

## not defined vs undefined
- not defined is where we tried to access a variable without declaration, then it will throw not defined error 
- undefined is a placeholder to a variable until the variable get's initialized.

```js

console.log(a); // undefined
var a = 7;
console.log(a); // 7
console.log(x); // ReferenceError: x is not defined

```
- we did not declare x anywhere in code so there is no reference related to x, that's why it throw `ReferenceError: x is not defined`.
- we tried to access `a` before initialized untill `a` get's value `undefined` work like a placeholder once `a` get's initialized it will log that value `7`.

## `let` vs `var` vs `const` 
- `var` is less stricter than `let`, `let` is less stricter than `const`.
```js
// ✅ var allows redeclaration and reassignment
var userAge = 25;
var userAge = 30; // Redeclared with a new value (no error)
console.log(userAge);  // Output: 30

// ❌ let does NOT allow redeclaration in the same scope
let userScore = 50;
// let userScore = 100; // ❌ SyntaxError: Identifier 'userScore' has already been declared
console.log(userScore); // Output: 50

// ✅ let can be declared first and initialized later
let userRank;
userRank = 1; // Initialized later
console.log(userRank); // Output: 1

// ❌ const does NOT allow redeclaration or reassignment
const MAX_LIMIT = 5000;
// const MAX_LIMIT = 6000; // ❌ SyntaxError: Identifier 'MAX_LIMIT' has already been declared
console.log(MAX_LIMIT); // Output: 5000

// ❌ const must be declared and initialized in the same line
const MIN_LIMIT;
// MIN_LIMIT = 100; // ❌ SyntaxError: Missing initializer in const declaration
console.log(MIN_LIMIT);

```
| Keyword | Redeclaration | Reassignment | Must Initialize Immediately |
|---------|----------------|--------------|-----------------------------|
| `var`   | ✅ Yes         | ✅ Yes       | ❌ No                        |
| `let`   | ❌ No          | ✅ Yes       | ❌ No                        |
| `const` | ❌ No          | ❌ No        | ✅ Yes                       |

```js
const arr = [1, 2, 3];

arr.push(4);     // ✅ Works → arr = [1, 2, 3, 4]
arr[0] = 10;     // ✅ Works → arr = [10, 2, 3, 4]

arr = [9, 8];    // ❌ TypeError: Assignment to constant variable

```
- we can modify the content / value for `const` bt we cant modify reference.

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
```
- Here we are modifying the output array like adding elem not changing the reference.

## Temporial Dead Zone
- Temporal Dead Zone is the phase between a variable's hoisting and its actual declaration line where let and const exist in memory but can’t be accessed. 
- If you try to access them in this zone, you'll get a ReferenceError.
- `let` and `const` are hoisted, but unlike var, they are not initialized during the memory phase.

>TDZ starts at the beginning at top of the scope( gobal or block or functional ) and ends at the line where the variable is declared.
<br />
```js
{
  // TDZ starts here
  console.log(value); // ❌ ReferenceError
  let value = 42;     // TDZ ends here
}
```

**`typeof` with let or const**
```js
console.log(typeof someVar); // ❌ ReferenceError: Cannot access 'someVar' before initialization
let someVar = "hi";
```

## syntax error vs reference error vs Type error

**Syntax Error** 
-  Thrown when the code violates JavaScript syntax rules.
- Detected at parse time (before execution begins).
```js
let x = ; // ❌ SyntaxError: Unexpected token ';'
```
🧠 Think: You wrote something the JS engine doesn’t even understand.
<br />
<br />

**Reference Error** 
-  Thrown when you try to access a variable that doesn't exist in the current scope.
```js
console.log(myVar); // ❌ ReferenceError: myVar is not defined
```
🧠 Think: You referenced something that was never declared or is still in the TDZ.
<br />
<br />

**Type error** 
- Thrown when an operation is performed on a value of the wrong type.
```js
const greet = null;
greet(); // ❌ TypeError: greet is not a function
```
🧠 Think: You’re calling or accessing something in a way that doesn’t match its type.
<br />
<br />

**Summary**

| Error Type       | When It Happens                                      | Example                         |
|------------------|------------------------------------------------------|----------------------------------|
| `SyntaxError`    | Code violates JavaScript grammar rules               | `let a = ;`                      |
| `ReferenceError` | Accessing undeclared variables or TDZ violations     | `console.log(x)`                |
| `TypeError`      | Performing invalid operations on a value's type      | `null()` or `"abc".push(1)`     |


## Hoisting in Functions 


**Example 1: Function Declaration Hoisting**
```js
console.log(x);     // undefined
// Calling function before definition
getName();      // Functions are fully hoisted

var x = 7;
function getName() {
    console.log("Functions are fully hoisted");
}

console.log(x); // 7
getName();      // Functions are fully hoisted

```
- The function `getName()` can be called before its declaration because the entire function definition is hoisted to the top.


**Example 2: Function Expression (No Hoisting)**
```js
greet(); // ❌ TypeError: greet is not a function

var greet = function() {
  console.log("Hello, world!");
};
```
- In this case, only the variable greet is hoisted, not the function expression itself. Therefore, greet() results in a TypeError because the function is not yet assigned to the variable at runtime.

**Example 3: Arrow Function (No Hoisting)**

```js
greet(); // ❌ TypeError: greet is not a function

var greet = () => {
  console.log("Hello from arrow function!");
};

```
- Arrow Function behave like function expressions


**Summary**


| Function Type        | Hoisted?         | Can Call Before Definition? | Notes                                        |
|----------------------|------------------|------------------------------|----------------------------------------------|
| Function Declaration | ✅ Fully hoisted  | ✅ Yes                        | Safe to call before it's defined             |
| Function Expression  | 🚫 Only variable  | ❌ No                         | `TypeError` if called before assignment      |
| Arrow Function       | 🚫 Only variable  | ❌ No                         | Same as function expressions in hoisting     |


### 📝 Notes:
- **Function Declarations** are fully hoisted — both name and body — so they can be invoked before their definition.
- **Function Expressions** are partially hoisted — the variable is hoisted, but not the assigned function. This results in a `TypeError` if accessed before assignment.

---
