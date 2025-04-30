# Promises

## Why we need to use promises instead of callbacks ?
- By using nested callbacks we lose control over result & it lead to callback hell.
- To avoid this we need to use promises 
  
  
```js

const cart = ["shirt", "shoes", "straw hat"];

api.createOrder(cart, function () {
    api.proceedToPayment(orderId, function () {
        api.orderSummary(paymentId, function () {
            api.showWalletballence();
        });
    });
});

```

- Here 2 problems we have by using nested callbacks, 
  - Callback hell
  - Inversion Of control
- **Callback Hell** : It is deep nested hard to maintain & debug, this kind of structure also known as pyramid of doom.
- **Inversion of Control** : Here `createOrder` create orderId by taking cart items, then using that orderId it call another nested function & provide paymentId, but here we are losing control over code, only we provide cart items remaining all createOrder function handling, if nested functions didn't work as expected then we dont know where exactly to find cause, So we loose our control on code. This phenomena is called Inversion of control.

- To avoid these both issues we use `Promises`.


## Promise

**Promise** : Promise is an object representing the eventual completion or failure of an asynchronous operation.
- It has 3 states : 
  - **pending**: initial state, neither fulfilled nor rejected.
  - **fulfilled**: meaning that the operation was completed successfully.
  - **rejected**: meaning that the operation failed.

```js

const GITHUB_API = "https://api.github.com/users/Charlie-Chows";
const user = fetch(GITHUB_API);
console.log(user);  


user.then(function(data){  
    console.log(data);
})

```
- `fetch` return a promise.
- Promises are immutable.
- `then` run only once irreaspective of success / failure.


```js

const cart = [ "shirt", "Pant", "Strawhat" ];

// we can write in both ways

// syntax 1
const promise = createOrder( cart );
promise.then( function( orderId) {
    proceedToPayment();
})

// syntax 2
createOrder(cart).then(function( orderId) {
    proceedToPayment();
})

```

**Callbacks vs promises**
```js

const cart = ["shirt", "Pant", "Strawhat"];

// using callbacks
createOrder(cart, function (orderId) {
    proceedToPayment(orderId, function (paymentInfo) {
        showOrderSummary(paymentInfo, function () {
            updateWalletBalance();
        });
    });
});

// using promises ( traditional func )
createOrder(cart)
    .then(function (orderId) {
        return proceedToPayment(orderId)
    })
    .then(function (paymentInfo) {
        return showOrderSummary(paymentInfo)
    })
    .then(function () {
        return updateWalletBalance();
    });

// using promises ( arrow func )
createOrder(cart)
    .then(orderId => proceedToPayment(orderId))
    .then(paymentInfo => showOrderSummary(paymentInfo))
    .then(() => updateWalletBalance());

```


**Promise Syntax**
```js

const cart = ["shirt", "Pant", "Strawhat"];

const promise = createOrder(cart);

promise.then( function(orderId){
    console.log(orderId);
})
.catch( function(err) {
    console.log(err.message);
})

function createOrder( cart ) {
    const pr = new Promise(function(resolve, reject) {
        if(!validateCart(cart)) {
            let err = new Error("cart is not valid");
            reject(err);
        }
        
        let orderId = "123";
        if (orderId) {
            resolve(orderId);
        }
    })
    return pr;
}

function validateCart(cart){
    return true;
};

```
- promise reject when cart is not validated, it resolves when validated & return with orderId
- promise can have multiple resolves or rejects but only first one exicutes others will be ignored irrespective of resolve or reject.

```js
const p = new Promise((resolve, reject) => {
  resolve("✅ First resolve");       // 👈 This works
  resolve("🚫 Second resolve");      // Ignored
  reject("🚫 Rejection after resolve"); // Ignored
});

p.then(res => console.log(res)).catch(err => console.error(err));

// output
// ✅ First resolve
```

## Promise Chaining

```js


const cart = ["shirt", "Pant", "Strawhat"];

const promise = createOrder(cart);

promise.then( function(orderId){
    console.log(orderId);
    return orderId;
})
.catch( function(err) {
    console.log(err.message);
})
.then( function(orderId) { 
    console.log("it will run no matter what"); 
    return proceedToPayment(orderId); 
})
.then( function( paymentInfo) {
    console.log(paymentInfo);

})
.catch( function(err) {
    console.log(err.message);
})

function createOrder( cart ) {
    const pr = new Promise(function(resolve, reject) {
        if(!validateCart(cart)) {
            let err = new Error("cart is not valid");
            reject(err);
        }

        let orderId = "123";
        if (orderId) {
            resolve(orderId);
        }
    })
    return pr;
}

function validateCart(cart){
    return true;
};

function proceedToPayment ( orderId ) {
    return new Promise( (resolve, reject ) => {
        resolve("payment Successful");
    })
}

```
- we always need to `return` in `then` block.
- `catch` only check error above `then` blocks only.
- so we can use multiple catch blocks wherever we need to check error.
- if any error appear it goes to nearest `catch` block.
  
  ```js
  .then( function(orderId) { 
    console.log("it will run no matter what"); 
    return proceedToPayment(orderId); 
    });
  ```
  - `orderId` receives from above `then` and passed it as param to `proceedToPayment`.


- we can use as many `catch` blocks we need after the catch blocks if there any `then` blocks is available those will run irrespective of catch.

```js
Promise.resolve("initial value")
  .then((res) => {
    console.log("then block 1:", res);
    return "value from then 1";
  })
  .then((res) => {
    console.log("then block 2:", res);
    throw new Error("Error in then block 2");
  })
  .catch((err) => {
    console.log("catch block 1:", err.message);
    return "recovered value";
  })
  .then((res) => {
    console.log("then block 3:", res);
    throw new Error("Error in then block 4");
  })
  .then((res) => {
    console.log("then block 4:", res);
  })
  .catch((err) => {
    console.log("catch block 2:", err.message);
  });

/* 
output 

then block 1: initial value
then block 2: value from then 1
catch block 1: Error in then block 2
then block 3: recovered value
catch block 2: Error in then block 4

*/
```

## Promise API's

### Promise.all()

```js
// ✅ SUCCESS CASE

// Suppose we have three promises:
const p1 = new Promise((res) => setTimeout(() => res("p1 done"), 3000)); // 3 sec
const p2 = new Promise((res) => setTimeout(() => res("p2 done"), 1000)); // 1 sec
const p3 = new Promise((res) => setTimeout(() => res("p3 done"), 2000)); // 2 sec

Promise.all([p1, p2, p3])
  .then((results) => {
    // It returns an array of resolved values in the same order as input
    console.log(results); // [ "p1 done", "p2 done", "p3 done" ]
  })
  .catch((err) => {
    console.error(err); // This won’t run in success case
  });

// It will take 3 seconds to resolve because Promise.all waits for all promises to finish.

```

```js

// FAILURE CASE 

const p1 = Promise.resolve("✅ p1 done");
const p2 = Promise.reject("❌ p2 failed");
const p3 = Promise.resolve("✅ p3 done");

Promise.all([p1, p2, p3])
  .then((results) => {
    console.log("All succeeded:", results);
  })
  .catch((err) => {
    console.error("❌ Promise.all failed:", err);
  });


// output 
// ❌ Promise.all failed: ❌ p2 failed

// If any of the promises passed to Promise.all() fails, it immediately rejects with that error. Even though the remaining promises may still continue running in the background, their results are ignored and not returned.

```

**Using API's**
```js
// Example: Parallel API calls using Promise.all

const fetchUserData = fetch("https://api.example.com/user");
const fetchPostsData = fetch("https://api.example.com/posts");
const fetchCommentsData = fetch("https://api.example.com/comments");

Promise.all([fetchUserData, fetchPostsData, fetchCommentsData])
  .then((responses) => {
    // All promises resolved successfully, process the data
    const [userResponse, postsResponse, commentsResponse] = responses;

    // Parse the JSON responses (assuming API returns JSON)
    return Promise.all([
      userResponse.json(),
      postsResponse.json(),
      commentsResponse.json(),
    ]);
  })
  .then(([userData, postsData, commentsData]) => {
    console.log("User Data:", userData);
    console.log("Posts Data:", postsData);
    console.log("Comments Data:", commentsData);
  })
  .catch((err) => {
    console.error("One of the API calls failed:", err);
  });


```
- We make three parallel `fetch` calls to get data from the APIs.
  
- `Promise.all` ensures that we wait for all promises to resolve.
  
- Once all promises resolve, the responses are returned as an array.
  
- We then call .`json()` on each of the responses to parse the JSON data.
  
- If any of the API calls fail (rejects), the entire chain will be rejected, and the `catch` block will handle the error.


### Promise.allSettled()

```js
// SUCCESS CASE 

const p1 = Promise.resolve("✅ p1 done");
const p2 = Promise.resolve("✅ p2 done");
const p3 = Promise.resolve("✅ p3 done");

Promise.allSettled([p1, p2, p3])
  .then((results) => {
    console.log("✅ Success Case:");
    console.log(results);
  });

/*
Output:
[
  { status: "fulfilled", value: "✅ p1 done" },
  { status: "fulfilled", value: "✅ p2 done" },
  { status: "fulfilled", value: "✅ p3 done" }
]
*/

```

```js
// FAILURE CASE

const p1 = Promise.resolve("✅ p1 done");
const p2 = Promise.reject("❌ p2 failed");
const p3 = Promise.resolve("✅ p3 done");

Promise.allSettled([p1, p2, p3])
  .then((results) => {
    console.log(results);
  });

/*
Output:
[
  { status: "fulfilled", value: "✅ p1 done" },
  { status: "rejected", reason: "❌ p2 failed" },
  { status: "fulfilled", value: "✅ p3 done" }
]
*/

```
**Using API's**
```js
// Example: Parallel API calls using Promise.allSettled

const fetchUserData = fetch("https://api.example.com/user");
const fetchPostsData = fetch("https://api.example.com/posts");
const fetchCommentsData = fetch("https://api.example.com/comments");

Promise.allSettled([fetchUserData, fetchPostsData, fetchCommentsData])
  .then((results) => {
    results.forEach((result, index) => {
      if (result.status === "fulfilled") {
        console.log(`API ${index + 1} succeeded:`, result.value);
      } else {
        console.log(`API ${index + 1} failed:`, result.reason);
      }
    });
  });

```
- `Promise.allSettled` waits for all promises to settle.

- Even if some promises are rejected, the remaining promises will be processed.

- The result contains information about each promise, including whether it was fulfilled or rejected.

### Promise.race()

```js
// SUCCESS CASE 

const p1 = new Promise((resolve) => setTimeout(() => resolve("✅ p1 done"), 3000));
const p2 = new Promise((resolve) => setTimeout(() => resolve("✅ p2 done"), 1000));
const p3 = new Promise((resolve) => setTimeout(() => resolve("✅ p3 done"), 2000));

Promise.race([p1, p2, p3])
  .then((result) => {
    console.log("Race Winner:", result); // ✅ p2 done
  })
  .catch((err) => {
    console.error("Race Failed:", err);
  });

  // OUTPUT
  // Race Winner: ✅ p2 done

```

```JS
// FAILURE CASE 

const p1 = new Promise((resolve) => setTimeout(() => resolve("✅ p1 done"), 3000));
const p2 = new Promise((_, reject) => setTimeout(() => reject("❌ p2 failed"), 1000));
const p3 = new Promise((resolve) => setTimeout(() => resolve("✅ p3 done"), 2000));

Promise.race([p1, p2, p3])
  .then((result) => {
    console.log("Race Winner:", result);
  })
  .catch((err) => {
    console.error("Race Failed:", err); // ❌ p2 failed
  });

// Output: "Race Failed: ❌ p2 failed"

// Note : if p2 is 4000ms so output will be p3 done 
// it does not care about resolve / reject who ever comes first it will return, it doesn't care about remaining result.
```

**Using API's**
```js
// Example: Parallel API calls using Promise.race (first one resolves or rejects)

const fetchUserData = fetch("https://api.example.com/user");
const fetchPostsData = fetch("https://api.example.com/posts");
const fetchCommentsData = fetch("https://api.example.com/comments");

Promise.race([fetchUserData, fetchPostsData, fetchCommentsData])
  .then((response) => {
    // Process the first successful response
    console.log("First successful API response:", response);
  })
  .catch((err) => {
    // Process the first failed response
    console.error("First failed API response:", err);
  });


```

- `Promise.race` will resolve or reject as soon as the first promise settles.

- The remaining promises will continue to run, but their results are ignored.

- This is useful when you're only interested in the first API response.

### Promise.any()

```js
// SUCCESS CASE 

const p1 = new Promise((_, reject) => setTimeout(() => reject("❌ p1 failed"), 1000));
const p2 = new Promise((resolve) => setTimeout(() => resolve("✅ p2 done"), 2000));
const p3 = new Promise((resolve) => setTimeout(() => resolve("✅ p3 done"), 3000));

Promise.any([p1, p2, p3])
  .then((result) => {
    console.log("First fulfilled:", result);
  })
  .catch((err) => {
    console.error("All promises failed:", err);
  });


// First fulfilled: ✅ p2 done

```

```JS
// FAILURE CASE

const p1 = new Promise((_, reject) => setTimeout(() => reject("❌ p1 failed"), 1000));
const p2 = new Promise((_, reject) => setTimeout(() => reject("❌ p2 failed"), 2000));
const p3 = new Promise((_, reject) => setTimeout(() => reject("❌ p3 failed"), 3000));

Promise.any([p1, p2, p3])
  .then((result) => {
    console.log("First fulfilled:", result);
  })
  .catch((err) => {
    console.error("All promises failed:");
    console.error(err);  // AggregateError - here it wont show all errors
    console.log(err.errors); // now all errors will display as array 
  });

// All promises failed:
// AggregateError: All promises were rejected
// ['❌ p1 failed', '❌ p2 failed', '❌ p3 failed']

```

**Using API's**
```js
// Example: Parallel API calls using Promise.any (first one fulfills)

const fetchUserData = fetch("https://api.example.com/user");
const fetchPostsData = fetch("https://api.example.com/posts");
const fetchCommentsData = fetch("https://api.example.com/comments");

Promise.any([fetchUserData, fetchPostsData, fetchCommentsData])
  .then((response) => {
    console.log("First successful API response:", response);
  })
  .catch((err) => {
    console.error("All promises failed:", err);
  });

```

- `Promise.any` resolves as soon as any one of the promises is fulfilled.

- If all promises are rejected, it will throw an `AggregateError`.



| API                 | Resolves When                        | Rejects When                         | Result Type                        |
|---------------------|--------------------------------------|--------------------------------------|------------------------------------|
| `Promise.all`        | All fulfill                          | Any one rejects                      | `[val1, val2, ...]`                |
| `Promise.allSettled` | All settle (resolve/reject)          | Never rejects                        | `[ {status, value/reason}, ... ]` |
| `Promise.race`       | First settles (resolve/reject)       | First rejects                        | First settled value or error      |
| `Promise.any`        | First fulfills                       | All reject → `AggregateError`        | First fulfilled value             |

