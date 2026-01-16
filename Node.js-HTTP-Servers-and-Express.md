# Node.js HTTP Servers and Express

## Basic HTTP Server in Node.js

```javascript
http = require('node:http');

listener = function (request, response) {
  // Send the HTTP header
  // HTTP Status: 200 : OK
  // Content Type: text/html
  response.writeHead(200, {'Content-Type': 'text/html'});

  // Send the response body as "Hello World"
  response.end('<h2 style="text-align: center;">Hello World</h2>');
};

server = http.createServer(listener);
server.listen(3000);

console.log('Server running at http://127.0.0.1:3000/');
```

### How it works:
1. **`require('node:http')`** - Imports Node's built-in HTTP module for creating web servers.
2. **`listener` function** - A callback that runs for every incoming request:
   - `response.writeHead(200, {'Content-Type': 'text/html'})` - Sets the status code to 200 (success) and tells the browser to expect HTML
   - `response.end(...)` - Sends the HTML response and closes the connection
3. **`http.createServer(listener)`** - Creates a server that uses your listener function to handle requests.
4. **`server.listen(3000)`** - Starts the server on port 3000.

---

## Routing with Plain Node.js

Use `request.url` to check the path and respond differently:

```javascript
http = require('node:http');

listener = function (request, response) {
  response.writeHead(200, {'Content-Type': 'text/html'});

  if (request.url === '/hello') {
    response.end('<h2>Hello!</h2>');
  } else if (request.url === '/goodbye') {
    response.end('<h2>Goodbye!</h2>');
  } else if (request.url === '/') {
    response.end('<h2>Home Page</h2>');
  } else {
    response.writeHead(404, {'Content-Type': 'text/html'});
    response.end('<h2>404 - Not Found</h2>');
  }
};

server = http.createServer(listener);
server.listen(3000);

console.log('Server running at http://127.0.0.1:3000/');
```

---

## HTTP Status Codes

**200** is the HTTP status code meaning **"OK"** — the request was successful.

| Code | Meaning | When it's used |
|------|---------|----------------|
| **200** | OK | Request succeeded |
| **201** | Created | Resource was created (e.g., after a POST) |
| **301** | Moved Permanently | URL has changed permanently |
| **302** | Found | Temporary redirect |
| **400** | Bad Request | Client sent invalid data |
| **401** | Unauthorized | Authentication required |
| **403** | Forbidden | Access denied |
| **404** | Not Found | Resource doesn't exist |
| **500** | Internal Server Error | Server crashed or failed |

### Grouped by first digit:
- **2xx** = Success
- **3xx** = Redirect
- **4xx** = Client error (your fault)
- **5xx** = Server error (server's fault)

---

## Node.js vs Express.js

**Node.js** is the runtime — it lets you run JavaScript outside the browser on your computer/server.

**Express.js** is a framework that runs *on top of* Node.js to make building web servers easier.

- **Node.js** = The engine
- **Express.js** = A toolkit that makes the engine easier to use

### Plain Node.js (manual work):
```javascript
const http = require('node:http');

const server = http.createServer((req, res) => {
  if (req.method === 'GET' && req.url === '/users') {
    res.writeHead(200, {'Content-Type': 'application/json'});
    res.end(JSON.stringify({users: []}));
  } else if (req.method === 'POST' && req.url === '/users') {
    // manually parse body, handle errors, etc.
  }
});

server.listen(3000);
```

### With Express.js (much simpler):
```javascript
const express = require('express');
const app = express();

app.use(express.json()); // automatic body parsing

app.get('/users', (req, res) => res.json({users: []}));
app.post('/users', (req, res) => res.json(req.body));

app.listen(3000);
```

### Express gives you:
- Clean routing (`app.get`, `app.post`, etc.)
- Automatic request body parsing
- Middleware (functions that run before your routes)
- Simpler response methods (`res.json()`, `res.send()`)

You always need Node.js. Express is optional but makes web development faster.

---

## API Framework Options

| Framework | Best for |
|-----------|----------|
| **Express.js** | General purpose, huge ecosystem, lots of tutorials |
| **Fastify** | Performance-focused, faster than Express |
| **Hono** | Lightweight, works on edge/serverless |
| **NestJS** | Large apps, TypeScript, structured architecture |
| **Koa** | Minimal, made by Express creators |

**Express** is still the most popular and has the most learning resources. It's a good default choice, especially when starting out.

### Simple API Example with Express:
```javascript
const express = require('express');
const app = express();

app.use(express.json());

const users = [];

app.get('/api/users', (req, res) => res.json(users));
app.post('/api/users', (req, res) => {
  users.push(req.body);
  res.status(201).json(req.body);
});

app.listen(3000);
```

Start with Express — you can always switch later if needed.
