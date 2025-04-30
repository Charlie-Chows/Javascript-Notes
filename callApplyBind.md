
# call, apply, bind

## ✅ Example Code

```js
// Reusable function in different objects
let printFullName = function (hometown, state) {
    console.log(this.firstName + " " + this.lastName + " from " + hometown + " , " + state);
};

// Object 1
let name = {
    firstName: "Sujith",
    lastName: "Kumar",
};

// Object 2
let name2 = {
    firstName: "Yazith",
    lastName: "Roronoa",
};
```

## `call()`

**Definition :** Immediately invokes the function with a specified `this`context and comma-separated arguments.

```js
// Syntax: function.call(thisArg, arg1, arg2, ...)
printFullName.call(name, "kadapa", "AndhraPradesh");   // Sujith Kumar from kadapa , AndhraPradesh
printFullName.call(name2, "Chennai", "Tamilnadu");     // Yazith Roronoa from Chennai , Tamilnadu
```
##  `apply()`

**Definition :** Just like `call()`, but takes arguments as an array instead of comma-separated.


```js
// Syntax: function.apply(thisArg, [arg1, arg2])
printFullName.apply(name2, ["Chennai", "Tamilnadu"]);  // Yazith Roronoa from Chennai , Tamilnadu
```

##  `call() & apply()`
```js
let args = ["Kadapa", "Andhra Pradesh"];

// Both are same:
printFullName.apply(name, args);
printFullName.call(name, ...args);
```
> ✅ **Note:**  
> - `apply()` accepts arguments as an array (`[arg1, arg2]`), whereas `call()` accepts them as comma-separated values.  
> -  Using the **spread operator (`...`)** with `call()` allows you to pass an array just like `apply()` does.  
> -  So both lines are functionally **equivalent** here.


##  `bind()`

**Definition :** `bind()` creates a new copy of the function , it wont execute immediatly that we can invoke it later how is this .


```js
// Syntax: function.bind(thisArg, arg1, arg2)
// Note: Returns a new function which can be invoked later
let printMyName = printFullName.bind(name, "kadapa", "AndhraPradesh");
printMyName();  // Sujith Kumar from kadapa , AndhraPradesh

// You can also log the bound function
console.log(printMyName);
/*
ƒ ( hometown, state ) {
    console.log( this.firstName + " " + this.lastName + " from " + hometown + " , " + state );
}
*/
```
## 📌 Key Differences

| Feature       | `call()`                        | `apply()`                          | `bind()`                             |
|---------------|----------------------------------|-------------------------------------|--------------------------------------|
| **Execution** | Immediately                      | Immediately                         | Returns a new function               |
| **Arguments** | Comma-separated                  | Passed as an array                  | Comma-separated (used for binding)   |
| **Use Case**  | Call with known arguments        | Call when you already have an array | Save for later execution             |
