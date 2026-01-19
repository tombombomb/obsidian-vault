# Basic Express.js Server Guide

A beginner-friendly guide explaining a simple Express.js server.

## The Complete Code

```javascript
// SETUP
// require('express') imports the Express framework (a library that helps build web servers)
// The () at the end immediately calls it to create our application instance
const app = require('express')();

// This is the port number where our server will run
// A port is like a door number - your computer has many ports, and we're using door 8080
const PORT = 8080;

// ROUTE DEFINITION
// app.get() creates an endpoint that responds to GET requests (when someone visits a URL)
// '/tshirt' is the path - so the full URL will be http://localhost:8080/tshirt
// (req, res) are two objects:
//   - req (request): contains info about the incoming request from the user
//   - res (response): used to send data back to the user
app.get('/tshirt', (req,res) =>{
    // res.status(200) sets the HTTP status code to 200, which means "OK" (success)
    // .send() sends the response back to the user
    // The object { tshirt: 'logo', size: 'large' } is automatically converted to JSON
    res.status(200).send({
        tshirt: 'logo',
        size: 'large'
    })
});

// START THE SERVER
// app.listen() tells our app to start listening for incoming requests
// First argument: the port number (8080)
// Second argument: a callback function that runs once the server is successfully started
// This callback just logs a message so we know everything is working
app.listen(
    PORT,
    () => console.log(`it's alive on http://localhost:${PORT}`)
)
```

---

## Detailed Explanations

### 1. Setting Up Express

```javascript
const app = require('express')();
```

This line does **two things at once**:

1. **`require('express')`** - Imports the Express library (returns a function)
2. **`()`** - Immediately calls that function to create an app instance

It's shorthand for:
```javascript
const express = require('express');  // Step 1: Import Express
const app = express();               // Step 2: Call it to create an app
```

> **Note:** `app` is just a variable name - you can call it anything. However, `app` is the convention everyone uses.

---

### 2. Dependencies: require() vs npm install

| Step | Command/Code | What it does |
|------|--------------|--------------|
| 1 | `npm install express` | Downloads and installs Express (creates the dependency) |
| 2 | `require('express')` | Loads the already-installed Express into your code |

Think of it like:
- `npm install` = going to the store and buying a tool
- `require()` = taking that tool out of your toolbox to use it

---

### 3. Arrow Functions (`=>`)

The `=>` is called an **arrow function**. It's a shorter way to write functions.

These are equivalent:
```javascript
// Arrow function (modern)
app.get('/tshirt', (req, res) => {
    res.send({ tshirt: 'logo' })
});

// Traditional function (older)
app.get('/tshirt', function(req, res) {
    res.send({ tshirt: 'logo' })
});
```

Breaking down the syntax:
```
(req, res) => { ... }
    │          │   │
    │          │   └── The function body (code that runs)
    │          └────── The arrow (replaces "function")
    └───────────────── The parameters (inputs)
```

---

### 4. Route Definition

```javascript
app.get('/tshirt', (req, res) => {
    res.status(200).send({
        tshirt: 'logo',
        size: 'large'
    })
});
```

| Part | What it does |
|------|--------------|
| `app.get()` | Creates a route that listens for GET requests |
| `'/tshirt'` | The URL path this route responds to |
| `(req, res) =>` | Function that runs when someone visits the route |
| `req` | The incoming request (data *from* the user) |
| `res` | The response object (sends data *back to* user) |
| `res.status(200)` | Sets the status code (200 = success) |
| `.send({...})` | Sends the JSON data back |

---

### 5. HTTP Response Structure

The status and data are part of the **HTTP response**, which has two parts:

```
┌─────────────────────────────────────┐
│         HTTP RESPONSE               │
├─────────────────────────────────────┤
│  HEADERS (metadata)                 │
│  ├── Status: 200 OK    ← .status()  │
│  ├── Content-Type: application/json │
│  └── etc...                         │
├─────────────────────────────────────┤
│  BODY (content)        ← .send()    │
│  {                                  │
│      "tshirt": "logo",              │
│      "size": "large"                │
│  }                                  │
└─────────────────────────────────────┘
```

- **Headers** are invisible metadata (view in browser DevTools → Network tab)
- **Body/Payload** is the actual content users see

---

### 6. Body vs Payload

These terms are often used interchangeably:

| Term | Meaning |
|------|---------|
| **Body** | Official HTTP term for the content |
| **Payload** | Casual term meaning "the actual data being carried" |

Like a delivery truck:
- **Headers** = shipping label (metadata)
- **Body/Payload** = the package inside (the stuff you care about)

---

### 7. How Express Knows to Send JSON

Express automatically detects what to send based on what you pass to `.send()`:

| What you send | Content-Type Express sets |
|---------------|---------------------------|
| Object or Array | `application/json` |
| String | `text/html` |
| Buffer | `application/octet-stream` |

So when you write:
```javascript
res.send({ tshirt: 'logo', size: 'large' })
```

Express internally does:
```javascript
res.setHeader('Content-Type', 'application/json');
res.send('{"tshirt":"logo","size":"large"}');
```

You can also use `res.json()` to explicitly send JSON:
```javascript
res.json({ tshirt: 'logo', size: 'large' })  // Same result
```

---

## Running the Server

1. Install dependencies:
   ```bash
   npm install express
   ```

2. Run the server:
   ```bash
   node index.js
   ```

3. Visit in your browser:
   ```
   http://localhost:8080/tshirt
   ```

You should see:
```json
{
    "tshirt": "logo",
    "size": "large"
}
```
