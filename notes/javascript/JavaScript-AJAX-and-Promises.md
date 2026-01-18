# JavaScript: AJAX, Promises, and .then()

## What is AJAX?

**AJAX** (Asynchronous JavaScript and XML) is a technique for making HTTP requests from a web browser to a server **without reloading the entire page**.

### Key characteristics:
- **Asynchronous**: The request happens in the background, so the user can continue interacting with the page
- **Partial updates**: Only specific parts of the page can be updated with new data
- **Various data formats**: Despite the name, modern AJAX typically uses JSON instead of XML

### Example using `fetch` (modern approach):

```javascript
fetch('/api/users')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

### Example using `XMLHttpRequest` (older approach):

```javascript
const xhr = new XMLHttpRequest();
xhr.open('GET', '/api/users');
xhr.onload = function() {
  if (xhr.status === 200) {
    console.log(JSON.parse(xhr.responseText));
  }
};
xhr.send();
```

### Common use cases:
- Submitting forms without page reload
- Loading new content dynamically (infinite scroll, live search)
- Auto-saving drafts
- Real-time updates (chat, notifications)

---

## What is a Promise?

A **Promise** is a JavaScript object that represents the eventual completion (or failure) of an asynchronous operation.

Think of it like a real-world promise: "I promise to get you the data. It's not ready yet, but when it is, I'll let you know."

### Three states:

| State | Meaning |
|-------|---------|
| **Pending** | Operation in progress, no result yet |
| **Fulfilled** | Operation completed successfully |
| **Rejected** | Operation failed |

### Basic example:

```javascript
const myPromise = new Promise((resolve, reject) => {
  // Simulate async work (like fetching data)
  setTimeout(() => {
    const success = true;

    if (success) {
      resolve('Here is your data!');  // Fulfilled
    } else {
      reject('Something went wrong');  // Rejected
    }
  }, 1000);
});

// Using the promise
myPromise
  .then(result => console.log(result))   // Runs if resolved
  .catch(error => console.log(error));   // Runs if rejected
```

### Why Promises exist:

Before promises, we had "callback hell":

```javascript
// Nested callbacks (hard to read)
getData(function(a) {
  getMoreData(a, function(b) {
    getEvenMoreData(b, function(c) {
      console.log(c);
    });
  });
});
```

With promises, it's flat and readable:

```javascript
getData()
  .then(a => getMoreData(a))
  .then(b => getEvenMoreData(b))
  .then(c => console.log(c))
  .catch(error => console.error(error));
```

---

## What does .then() do?

`.then()` is a method that runs a callback function **when a Promise resolves** (completes successfully).

### Basic concept:

```javascript
somePromise.then(result => {
  // This code runs AFTER the promise completes
  // "result" contains the resolved value
});
```

### Simple example:

```javascript
const promise = new Promise(resolve => {
  setTimeout(() => resolve('Hello!'), 1000);
});

promise.then(message => {
  console.log(message);  // Prints "Hello!" after 1 second
});
```

### Chaining .then()

Each `.then()` returns a **new Promise**, so you can chain them:

```javascript
fetch('/api/users')
  .then(response => response.json())  // Returns a Promise
  .then(data => data.filter(u => u.active))  // Returns a Promise
  .then(activeUsers => console.log(activeUsers));
```

The value you **return** from one `.then()` becomes the input to the **next** `.then()`:

```javascript
Promise.resolve(5)
  .then(num => num * 2)      // Returns 10
  .then(num => num + 3)      // Returns 13
  .then(num => console.log(num));  // Prints 13
```

### Key points:

| Aspect | Behavior |
|--------|----------|
| When it runs | After the Promise resolves |
| Input | The resolved value from the previous step |
| Return value | Creates a new Promise for chaining |
| If you return a Promise | The chain waits for it to resolve |

### Without .then():

```javascript
const result = fetch('/api/users');
console.log(result);  // Prints: Promise { <pending> }
                      // NOT the actual data!
```

You need `.then()` (or `await`) to access the resolved value.

---

## Breaking down a fetch call step by step

```javascript
fetch('/api/users')
  .then(response => response.json())
  .then(data => console.log(data))
  .catch(error => console.error(error));
```

1. **`fetch('/api/users')`** - Makes an HTTP GET request. Returns a Promise.

2. **`.then(response => response.json())`** - When response arrives, parse the body as JSON. Returns another Promise.

3. **`.then(data => console.log(data))`** - When parsing completes, log the JavaScript object/array.

4. **`.catch(error => console.error(error))`** - If anything fails, catch and log the error.

### Visual flow:

```
fetch() → Promise → response.json() → Promise → data → console.log()
                          ↓
                    (if error anywhere)
                          ↓
                    console.error()
```

---

## Modern alternative: async/await

Syntactic sugar that makes promises even cleaner:

```javascript
async function fetchUsers() {
  try {
    const response = await fetch('/api/users');
    const data = await response.json();
    console.log(data);
  } catch (error) {
    console.error(error);
  }
}
```

Same behavior as the `.then()` chain, but reads like synchronous code.

---

## Important note about fetch error handling

`fetch` only rejects on **network failures**. A 404 or 500 response is still considered "successful" by fetch. To handle HTTP errors properly:

```javascript
fetch('/api/users')
  .then(response => {
    if (!response.ok) {
      throw new Error(`HTTP ${response.status}`);
    }
    return response.json();
  })
  .then(data => console.log(data))
  .catch(error => console.error(error));
```
