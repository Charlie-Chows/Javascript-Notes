# Scope Chaining
 - We can access data from same scope if it is not avaible it will look into it parent enviroment / lexical scope & grand parent environment till it reaches to null. This phenomena is called Scope Chaining.

 ```js
function x() {
    var b = 10;

    function y() {
        console.log(b);     // 10
    }
    y();
}

x();
 ```

 - It will check only parent scope not sibling scope

```js
function x() {
    var a = 10;
 
    function y() {
        var b = 20;
        console.log(a);
        console.log(c);     // Reference error : c not defined
    }
    y();

    function z() {
        var c = 30;
        console.log(a);
        console.log(b);     // Reference error : b not defined
    }
    z();
}

x();
```

**What is block ?**
- A block is a group of JavaScript statements wrapped inside curly braces `{}`. It allows you to keep multiple lines of code together in one place.
 
```js
{
  let a = 10;
  console.log(a);
}
```

### Block Scope

```js
{
    var a = 10;
    let b = 20;
    const c = 30;

    console.log(a);     // 10
    console.log(b);     // 20
    console.log(c);     // 30
}

console.log(a);     // 10
console.log(b);     // Referene Error : b not defined
console.log(c);     // this line wont execute due previous line error
```

- `let` & `const` are block scopped we cant access from out side of block.


### Variable Shadowing

```js

var a = 100;
{
    var a = 10; // it shadows the variable 
    console.log(a);     // 10
}

console.log(a);     // 10

```
- inside `a` shadows the global `a` & modify the value beacuse both are pointing to same memory location.
- let & const won't support variable shadowing due to both are pointing to different memory location

```js
let b = 200;
{
    let b = 20;
    console.log(b);     // 20
}

console.log(b);     // 200;
```

## Closures

**Closure :** Closure is a combination of function along with it lexical environment bundled together is called closure.
```js
function x() {
    let a = 7;
    function y() {
        console.log(a);
    }
    return y;  // y return func + it's own lexical binding thats why we can able to log 7 when we call z().
}

let z = x();

console.log(z);
/*

ƒ y() {
    console.log(a);
} 

*/

z();    // 7 
```

```js

function x() {
    var a = 7;
    function y() {
        var b = 70;
        function z() {
            console.log(a, b);
        }
        return z;
    }
    return y;
}

let fn = x();

console.log(fn());
/* 

ƒ z() {
         console.log(a, b);
    }

*/

let inner = fn();
inner();    // 7 70 

```
**🤔 What happens when you run this?**
- `x()` runs:
  - Creates `a = 7`
  - Defines `y()`
  - Returns `y`, so `fn = y`
- Then we do `fn()`:
  - This is now calling `y()`
  - Inside `y()`, `b = 70`, and `z()` is defined
  - `y()` returns `z`, but you're not storing or calling it
- So `z()` is returned, but not executed.
- That's why we got output as `ƒ z() { console.log(a, b); }`

**To get actual output :**
```js
let fn = x();       // fn = y
let inner = fn();   // inner = z
inner();            // Now z() runs => console.log(a, b)
```
### Where we use Closures
- Module Design Pattern
- Currying
- Functions like once
- Memoize
- setTimeout 
- iterator etc...

### Tricky question
```js

function x() {
    for (let i = 1; i <= 5; i++) {
        setTimeout(() => {
            console.log(i)      // 1 2 3 4 5
        }, i * 1000)
    }
}

x();

function x() {
    for (var i = 1; i <= 5; i++) {
        setTimeout(() => {
            console.log(i)      // 6 6 6 6 6
        }, i * 1000)
    }
}

x();
```

- `var i` is function scoped so `i` always pointing to reference not value, loop iterate quickly, before `setTimout` even finishes.
- After setTimeout done when it check for `i` value to log it is `6` because loop already finishes iteration.

- But when we use `let` it is block scope every iteration it creates new variable with new reference thats why we got `1 2 3 4 5`.

**With var**

```js
function x() {
    for (var i = 1; i <= 5; i++) {
        function close(x) {
            setTimeout(() => {
                console.log(x); // 1 2 3 4 5
            }, x * 1000);
        }
        close(i)
    }
}

x();
```

- here we are sending `i` to `close` function with updated value so we got `1 2 3 4 5`.

**Examples**
```js
function outerMost(){
    var x = 7;
    console.log(x);
    return function outer () {
        var y = 70;
        console.log(y);
        return function inner() {
            console.log(x,y);
        }
    }
}

outerMost()()();
```

- we are accessing `x,y` from lexical scope.
- we can call nested functions using braces `outerMost()()()`;


**Data Privacy**
```js

function counter() {
    let count = 0;
    return function increment() {
        count++;
        console.log(count)
    }
}

let c1 = counter();
c1();   // 1
c1();   // 2
c1();   // 3

let c2 = counter();
c2();   // 1
```
- here we cant directly access or modify the `count` value, only we can update by calling func.
- `c1 & c2` are separate, `c2` won't start where `c1` `count` ends, `c2` creates altogether new closures new variable entire new context itself.

**Scaling the counter above code**
```js

function Counter() {
    let count = 0;
    this.increment = function () {
        count++;
        console.log(count)
    }
    this.decrement = function () {
        count--;
        console.log(count)
    }
}

let count = new Counter();
count.increment();  // 1
count.increment();  // 2
count.decrement();  // 1

```

- Now it is scalable we can add how many actions(increment, decrement, etc,,,) we want using constructor.

### Garbage Collection
```js

function outer ( ) {
    var a = 10;
    var b = 20;
    function inner () {
        console.log(b);
    }
    return inner;
}
outer()();

let fn = outer();
fn();
``` 

- here `a` is garbage collected once outer function returned.
- `b` is not garbage collected, it's preserved in the closure scope, because we using b in `inner` func.

