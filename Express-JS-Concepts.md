# Express.js Concepts - Lenel OpenAccess API

A guide to understanding how the Express.js application works, based on the Lenel OpenAccess Express API project.

---

## Table of Contents

1. [Configuration with dotenv](#configuration-with-dotenv)
2. [JavaScript Objects vs JSON](#javascript-objects-vs-json)
3. [Module Exports and Require](#module-exports-and-require)
4. [Destructuring Assignment](#destructuring-assignment)
5. [Async Functions](#async-functions)
6. [Express Middleware](#express-middleware)
7. [Routing](#routing)
8. [Application Lifecycle](#application-lifecycle)
9. [Graceful Shutdown](#graceful-shutdown)
10. [Security Middleware](#security-middleware)

---

## Configuration with dotenv

### What is dotenv?

`dotenv` is an npm package that loads environment variables from a `.env` file into `process.env`.

### How it works

```js
require('dotenv').config();
```

This line:
1. Loads the `dotenv` package
2. Reads the `.env` file in your project root
3. Loads those variables into `process.env`

### Example

**.env file:**
```
LENEL_HOST=192.168.1.100
LENEL_USERNAME=admin
PORT=3000
```

**In your code:**
```js
process.env.LENEL_HOST      // "192.168.1.100"
process.env.LENEL_USERNAME  // "admin"
process.env.PORT            // "3000"
```

### What is process.env?

`process.env` is a built-in Node.js object containing all environment variables available to your application.

| Source | Example |
|--------|---------|
| Operating system | `PATH`, `HOME`, `USER` |
| Shell export | `export PORT=3000` |
| Command line | `PORT=3000 node app.js` |
| `.env` file | Loaded by dotenv |

**Important:** All values are **strings** (that's why you see `parseInt()` in config files).

### Where dotenv looks for .env

By default, dotenv looks for `.env` in the **current working directory** — typically the project root where you run `node` from.

---

## JavaScript Objects vs JSON

The config file creates a **JavaScript object**, not JSON. They look similar but are different:

| Feature | JavaScript Object | JSON |
|---------|-------------------|------|
| Comments | Allowed | Not allowed |
| Functions | Allowed | Not allowed |
| Dynamic values | Like `process.env.PORT` | Only static values |
| Quotes on keys | Optional | Required |
| File extension | `.js` | `.json` |

### Example JavaScript Object (dynamic)

```js
const config = {
  port: parseInt(process.env.PORT, 10) || 3000,  // Computed at runtime
  host: process.env.HOST || 'localhost'
};
```

### Example JSON (static)

```json
{
  "port": 3000,
  "host": "localhost"
}
```

---

## Module Exports and Require

### How modules work

1. **Object created in memory** when Node.js runs the file
2. **Exported with `module.exports`** to make it available
3. **Other scripts import it** with `require()`

```js
// config/index.js
const config = { port: 3000 };
module.exports = config;

// app.js
const config = require('./config');
console.log(config.port);  // 3000
```

### Caching/Singleton behavior

Node.js **caches** modules after the first `require()`:

```js
// In app.js
const config = require('./config');  // Runs index.js, creates object

// In authService.js
const config = require('./config');  // Returns the SAME cached object
```

Every file gets the **exact same object in memory** — not a copy.

### Why ./config finds index.js

When you `require('./config')` and `config` is a folder, Node.js automatically looks for `index.js` inside it:

```
require('./config')  →  ./config/index.js
```

---

## Destructuring Assignment

Destructuring imports **specific properties** from an exported object:

```js
const { errorHandler, notFoundHandler } = require('./middleware/errorHandler');
```

### Without destructuring

```js
const errorModule = require('./middleware/errorHandler');
errorModule.errorHandler(err, req, res, next);
errorModule.notFoundHandler(req, res, next);
```

### With destructuring

```js
const { errorHandler, notFoundHandler } = require('./middleware/errorHandler');
errorHandler(err, req, res, next);
notFoundHandler(req, res, next);
```

It's shorthand for:

```js
const temp = require('./middleware/errorHandler');
const errorHandler = temp.errorHandler;
const notFoundHandler = temp.notFoundHandler;
```

---

## Async Functions

An **async function** can pause and wait for operations without blocking other code.

### The problem it solves

Some operations take time (network requests, file reads). Without async:

```js
const result = makeSlowAPICall();  // Blocks everything
console.log('Done');               // Waits until API returns
```

### How async/await works

```js
async function getData() {
  const result = await makeSlowAPICall();  // Pauses here, doesn't block
  console.log('Done');                      // Runs after API returns
}
```

| Concept | Meaning |
|---------|---------|
| `async` | "This function will use await" |
| `await` | "Pause here until this finishes" |
| Non-blocking | Other code can run while waiting |

---

## Express Middleware

### What is middleware?

Functions that run **between** receiving a request and sending a response:

```
Request → Middleware 1 → Middleware 2 → Route Handler → Response
```

### Using app.use()

```js
app.use(helmet());           // Run for ALL requests
app.use('/api', routes);     // Run for requests matching /api
```

### Order matters

Middleware runs in the order it's defined:

```js
app.use(helmet());        // 1st
app.use(cors());          // 2nd
app.use(express.json());  // 3rd
app.use('/api', routes);  // 4th
app.use(errorHandler);    // Last
```

### How middleware passes data

Each middleware calls `next()` to pass control to the next one:

```js
function myMiddleware(req, res, next) {
  console.log('Request received');
  next();  // Pass to next middleware
}
```

If `next()` is not called, the request **stops** at that middleware.

### Like Linux pipes

| Linux pipe | Express middleware |
|------------|-------------------|
| `\|` passes data to next command | `next()` passes request to next middleware |
| Each command transforms data | Each middleware transforms req/res |
| Order matters | Order matters |

```bash
# Linux
cat file.txt | grep "error" | sort | uniq

# Express
request → helmet → cors → express.json → routes → response
```

---

## Routing

### How routing works

```js
app.use('/api', routes);
```

Any request starting with `/api` gets passed to the `routes` router.

### The routing chain

**1. app.js** — mounts all routes under `/api`
```js
app.use('/api', routes);
```

**2. routes/index.js** — splits to sub-routers
```js
router.use('/auth', authRoutes);           // /api/auth/*
router.use('/cardholders', cardholderRoutes); // /api/cardholders/*
```

**3. routes/cardholders.js** — handles specific endpoints
```js
router.get('/', ...);      // GET /api/cardholders
router.get('/:id', ...);   // GET /api/cardholders/123
```

### Path stripping

When passed to a sub-router, the matched prefix is removed:

```
Request: GET /api/cardholders
routes sees: GET /cardholders
```

---

## Application Lifecycle

### Startup (runs once)

```js
const express = require('express');   // Load modules
const config = require('./config');

const app = express();                // Create app
app.use(helmet());                    // Register middleware
app.use('/api', routes);              // Register routes
app.listen(PORT);                     // Start listening
```

### Request handling (runs per request)

When a request comes in:
1. Express parses raw HTTP into `req` object
2. Creates `res` object with response methods
3. Runs middleware chain
4. Route handler sends response

### The req object

```js
req = {
  method: 'POST',
  url: '/api/cardholders',
  headers: { ... },
  body: { name: 'Tom' },  // After express.json()
  params: {},
  query: {}
}
```

### The res object

```js
res = {
  status: function(code) { ... },
  json: function(data) { ... },
  send: function(data) { ... }
}
```

---

## Graceful Shutdown

### What is SIGTERM/SIGINT?

Signals telling a process to terminate:

| Signal | Triggered by |
|--------|--------------|
| `SIGTERM` | `docker stop`, `kill <pid>` |
| `SIGINT` | Pressing **Ctrl+C** |

### Handling shutdown

```js
process.on('SIGTERM', async () => {
  logger.info('Shutting down...');
  await authService.logout();  // Clean up
  process.exit(0);             // Exit successfully
});
```

### Why graceful shutdown?

| Without | With |
|---------|------|
| Session left hanging | Session properly closed |
| Resources may leak | Clean cleanup |
| Abrupt termination | Controlled exit |

---

## Security Middleware

### Helmet

Adds security HTTP headers with one line:

```js
app.use(helmet());
```

Headers it sets:
- `X-Content-Type-Options` — Prevents MIME sniffing
- `X-Frame-Options` — Prevents clickjacking
- `X-XSS-Filter` — Cross-site scripting protection
- `Strict-Transport-Security` — Forces HTTPS

### CORS

Controls which websites can make requests to your API:

```js
app.use(cors());  // Allow all origins
app.use(cors({ origin: 'https://myapp.com' }));  // Allow specific origin
```

Without CORS, browsers block requests from different origins (protocol + domain + port).

---

## Summary Flow

```
node app.js
     ↓
Load modules, register middleware (once)
     ↓
app.listen() - Start server
     ↓
Waiting for requests...
     ↓
Request comes in → Parse to req/res objects
     ↓
helmet → cors → express.json → routes
     ↓
Route handler processes, calls res.json()
     ↓
Response sent → Back to waiting
```

---

*Generated from Claude Code session - 2026-01-20*
