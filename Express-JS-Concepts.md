# Learning Express.js - Lenel OpenAccess API Project

These are my notes from learning how Express.js works by exploring the Lenel OpenAccess API project.

---

## What does `require('dotenv').config()` do?

This does two things:

1. **`require('dotenv')`** — Loads the dotenv npm package
2. **`.config()`** — Reads the `.env` file and loads variables into `process.env`

So if your `.env` file has:
```
LENEL_HOST=192.168.1.100
LENEL_USERNAME=admin
```

After this line runs, you can access them as:
```js
process.env.LENEL_HOST      // "192.168.1.100"
process.env.LENEL_USERNAME  // "admin"
```

---

## What is dotenv?

An npm package that loads environment variables from a `.env` file into `process.env`.

**Why use it?**
- Keeps sensitive data (passwords, API keys) out of your code
- Different environments (dev, production) can have different `.env` files
- The `.env` file is in `.gitignore` so secrets aren't committed

**Where does dotenv look for .env?**

By default, it looks in the **current working directory** — the project root where you run `node` from.

---

## What is process.env?

A built-in Node.js object that contains all environment variables available to your application.

Variables can come from:
- **Operating system** — `PATH`, `HOME`, `USER`
- **Shell export** — `export PORT=3000`
- **Command line** — `PORT=3000 node app.js`
- **`.env` file** — Loaded by dotenv

**Important:** All values are **strings**. That's why you see `parseInt()` in config files.

---

## Is the config a JavaScript object or JSON?

It's a **JavaScript object**, not JSON. They look similar but are different:

| Feature | JavaScript Object | JSON |
|---------|-------------------|------|
| Comments | ✅ Allowed | ❌ Not allowed |
| Functions | ✅ Allowed | ❌ Not allowed |
| Dynamic values | ✅ Like `process.env.PORT` | ❌ Only static values |

The config **builds** an object dynamically at runtime using environment variables, rather than loading static JSON from a file.

---

## Where is the config stored — memory or a file?

**In memory**, not a file.

When Node.js runs `index.js`, it creates the `config` object in memory. Then `module.exports = config` makes it available to other files.

---

## How do other scripts access the config?

```js
const config = require('./config');

console.log(config.server.port);    // 3000
console.log(config.lenel.host);     // "192.168.1.100"
```

**Key behavior:** Node.js **caches** the module after the first `require()`. Every file that imports config gets the **exact same object in memory** — not a copy.

---

## The flow of loading config

```
.env file (on disk)
      ↓
dotenv reads it
      ↓
process.env (system memory)
      ↓
config object built (JavaScript object in memory)
      ↓
module.exports
      ↓
Any script can require('./config') to access it
```

---

## Why does `require('./config')` find `index.js`?

When you `require()` a folder, Node.js automatically looks for `index.js` inside it:

```
require('./config')  →  ./config/index.js
```

It's a convention — `index.js` acts as the "main" file for a folder.

---

## What is destructuring assignment?

This syntax imports **specific functions** from a module:

```js
const { errorHandler, notFoundHandler } = require('./middleware/errorHandler');
```

**Without destructuring:**
```js
const errorModule = require('./middleware/errorHandler');
errorModule.errorHandler(err, req, res, next);
```

**With destructuring:**
```js
const { errorHandler, notFoundHandler } = require('./middleware/errorHandler');
errorHandler(err, req, res, next);
```

The curly braces "pick out" specific properties from the exported object.

---

## What is an async function?

A function that can pause and wait for things to finish (like API calls) without blocking other code.

```js
async function getData() {
  const result = await makeSlowAPICall();  // Pauses here, doesn't block
  console.log('Done');                      // Runs after API returns
}
```

- **`async`** — Marks the function as asynchronous
- **`await`** — Pauses execution until the operation completes

Without `await`, the code would continue immediately without waiting for the API call to finish.

---

## What does the startServer function do?

1. Checks if Lenel credentials are configured in `.env`
2. If yes, tries to auto-login to Lenel API
3. If auto-login fails, logs a warning but **doesn't crash** — server still starts
4. Starts Express server with `app.listen(PORT)`
5. Logs startup info

The key design: auto-login failure doesn't prevent the server from starting.

---

## What is graceful shutdown?

Cleaning up properly when the server is asked to stop.

```js
process.on('SIGTERM', async () => {
  await authService.logout();  // Clean up session
  process.exit(0);             // Exit successfully
});
```

**SIGTERM** is a signal sent when:
- `docker stop` runs
- `kill <pid>` runs
- A process manager restarts your app

**SIGINT** is sent when you press **Ctrl+C**.

---

## What is Helmet?

Security middleware that adds HTTP headers to protect against common vulnerabilities:

```js
app.use(helmet());
```

One line adds headers like:
- `X-Frame-Options` — Prevents clickjacking
- `X-XSS-Filter` — Cross-site scripting protection
- `Strict-Transport-Security` — Forces HTTPS

It's like putting a helmet on your app — basic protection with minimal effort.

---

## What is CORS?

**Cross-Origin Resource Sharing** — controls which websites can make requests to your API.

By default, browsers block requests from one website to another. CORS middleware tells browsers "it's okay to accept my responses":

```js
app.use(cors());  // Allow requests from any origin
```

Your app uses CORS so external tools (n8n, dashboards) can call it from different domains.

---

## Which part starts the server and waits for requests?

```js
app.listen(PORT, () => {
  logger.info(`Server running on port ${PORT}`);
});
```

Once this runs, the server is **actively waiting** for requests on that port until terminated.

---

## How does routing work?

```js
app.use('/api', routes);
```

Any request starting with `/api` gets passed to the `routes` router.

**The chain:**
```
GET /api/cardholders/42

/api           → app.js says: "use routes"
/cardholders   → routes/index.js says: "use cardholderRoutes"
/42            → routes/cardholders.js says: "GET /:id → run handler"
```

The `/api` prefix is stripped when passed to the next router.

---

## What is `app.use()` for?

It's the main way to add **middleware** in Express:

```js
app.use(helmet());           // Run for ALL requests
app.use('/api', routes);     // Run for requests matching /api
```

**Order matters** — middleware runs in the order it's defined.

---

## How does the middleware chain work?

Like a **Linux pipe command**!

**Linux:**
```bash
cat file.txt | grep "error" | sort | uniq
```

**Express:**
```
request → helmet → cors → express.json → routes → response
```

Each middleware processes the request and calls `next()` to pass it to the next one.

---

## What happens when a request comes in?

1. **Express parses** raw HTTP into a `req` object
2. **Creates** a `res` object with response methods
3. **Passes** `req` and `res` through the middleware chain
4. **Route handler** uses `req` data and calls `res.json()` to respond

```js
req = {
  method: 'POST',
  url: '/api/cardholders',
  headers: { ... },
  body: { name: 'Tom' },  // After express.json() middleware
}

res = {
  status: function(code) { ... },
  json: function(data) { ... },
}
```

---

## Does the code run every time or just once?

**Runs top to bottom once** at startup, loads everything into memory, then sits waiting for requests.

**Startup (once):**
```js
const express = require('express');  // Load modules
const app = express();               // Create app
app.use(helmet());                   // Register middleware
app.listen(PORT);                    // Start listening
```

**Per request:**
- Execute middleware chain
- Run matching route handler
- Send response
- Back to waiting

---

## Summary Flow

```
node app.js
     ↓
Load modules, register middleware (once)
     ↓
app.listen() — Start server
     ↓
Waiting for requests...
     ↓
Request comes in → Parse to req/res
     ↓
helmet → cors → express.json → routes
     ↓
Route handler → res.json()
     ↓
Response sent → Back to waiting
```

---

*Session notes - 2026-01-20*
