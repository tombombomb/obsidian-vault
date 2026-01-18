# Express.js Initialization Tutorial

## What is Express.js?

Express.js is a minimal and flexible Node.js web application framework that provides a robust set of features for web and mobile applications. It's the most popular Node.js framework and makes building web servers much simpler than using raw Node.js.

## Prerequisites

Before starting, ensure you have:
- Node.js installed (version 14 or higher recommended)
- NPM (comes with Node.js)
- Basic understanding of JavaScript
- A code editor (VS Code, Sublime, etc.)

## Initial Project Setup

### Step 1: Create Project Directory

```bash
mkdir my-express-app
cd my-express-app
```

### Step 2: Initialize NPM

```bash
npm init -y
```

This creates a `package.json` file with default settings.

### Step 3: Install Express

```bash
npm install express
```

This installs Express.js and adds it to your dependencies in `package.json`.

## Creating Your First Express Server

### Basic Server Setup

Create a file called `index.js` or `server.js`:

```javascript
// Import the express module
const express = require('express');

// Create an Express application
const app = express();

// Define the port to run the server on
const PORT = 3000;

// Create a basic route
app.get('/', (req, res) => {
  res.send('Hello, World!');
});

// Start the server
app.listen(PORT, () => {
  console.log(`Server is running on http://localhost:${PORT}`);
});
```

### Running Your Server

```bash
node index.js
```

Visit `http://localhost:3000` in your browser to see "Hello, World!"

## Understanding the Code

Let's break down what's happening:

**Importing Express**
```javascript
const express = require('express');
```
This imports the Express module into your application.

**Creating an App Instance**
```javascript
const app = express();
```
This creates an Express application object. This `app` object has methods for routing, middleware, and more.

**Defining Routes**
```javascript
app.get('/', (req, res) => {
  res.send('Hello, World!');
});
```
- `app.get()` defines a route that responds to GET requests
- First parameter: the path (`'/'` is the root route)
- Second parameter: callback function with `req` (request) and `res` (response) objects

**Starting the Server**
```javascript
app.listen(PORT, callback)
```
This tells Express to listen for requests on the specified port.

## Project Structure

A typical Express.js project structure:

```
my-express-app/
├── node_modules/
├── public/
│   ├── css/
│   ├── js/
│   └── images/
├── routes/
│   ├── index.js
│   └── users.js
├── views/
│   └── index.html
├── .env
├── .gitignore
├── package.json
├── package-lock.json
└── server.js
```

## Essential Middleware

Middleware functions have access to the request and response objects. They can execute code, modify these objects, end the request-response cycle, or call the next middleware.

### Installing Common Middleware

```bash
npm install dotenv morgan cors helmet
```

### Body Parser Middleware (Built-in)

For parsing JSON and URL-encoded data:

```javascript
const express = require('express');
const app = express();

// Parse JSON bodies
app.use(express.json());

// Parse URL-encoded bodies
app.use(express.urlencoded({ extended: true }));
```

### Serving Static Files

```javascript
// Serve static files from the 'public' directory
app.use(express.static('public'));
```

Now files in your `public` folder are accessible directly:
- `public/style.css` → `http://localhost:3000/style.css`

### CORS (Cross-Origin Resource Sharing)

```javascript
const cors = require('cors');

// Enable all CORS requests
app.use(cors());

// Or configure specific origins
app.use(cors({
  origin: 'http://example.com'
}));
```

### Morgan (HTTP Request Logger)

```javascript
const morgan = require('morgan');

// Log all requests to console
app.use(morgan('dev'));
```

### Helmet (Security Headers)

```javascript
const helmet = require('helmet');

// Add security headers
app.use(helmet());
```

## Complete Example with Middleware

```javascript
const express = require('express');
const morgan = require('morgan');
const cors = require('cors');
const helmet = require('helmet');
require('dotenv').config();

const app = express();
const PORT = process.env.PORT || 3000;

// Middleware
app.use(helmet());
app.use(cors());
app.use(morgan('dev'));
app.use(express.json());
app.use(express.urlencoded({ extended: true }));
app.use(express.static('public'));

// Routes
app.get('/', (req, res) => {
  res.send('Welcome to my Express app!');
});

app.get('/api/users', (req, res) => {
  res.json([
    { id: 1, name: 'Alice' },
    { id: 2, name: 'Bob' }
  ]);
});

app.post('/api/users', (req, res) => {
  const newUser = req.body;
  res.status(201).json({ message: 'User created', user: newUser });
});

// Error handling middleware (should be last)
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});

// 404 handler (should be second to last)
app.use((req, res) => {
  res.status(404).send('Page not found');
});

// Start server
app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

## Routing

### Basic Routes

Express supports all HTTP methods:

```javascript
// GET request
app.get('/users', (req, res) => {
  res.send('GET request to /users');
});

// POST request
app.post('/users', (req, res) => {
  res.send('POST request to /users');
});

// PUT request
app.put('/users/:id', (req, res) => {
  res.send(`PUT request to /users/${req.params.id}`);
});

// DELETE request
app.delete('/users/:id', (req, res) => {
  res.send(`DELETE request to /users/${req.params.id}`);
});
```

### Route Parameters

```javascript
// URL parameter
app.get('/users/:id', (req, res) => {
  const userId = req.params.id;
  res.send(`User ID: ${userId}`);
});

// Multiple parameters
app.get('/users/:userId/posts/:postId', (req, res) => {
  const { userId, postId } = req.params;
  res.send(`User ${userId}, Post ${postId}`);
});
```

### Query Parameters

```javascript
// URL: /search?q=express&limit=10
app.get('/search', (req, res) => {
  const { q, limit } = req.query;
  res.send(`Searching for: ${q}, Limit: ${limit}`);
});
```

### Router Objects

For better organization, use Express Router:

**routes/users.js**
```javascript
const express = require('express');
const router = express.Router();

router.get('/', (req, res) => {
  res.json({ message: 'Get all users' });
});

router.get('/:id', (req, res) => {
  res.json({ message: `Get user ${req.params.id}` });
});

router.post('/', (req, res) => {
  res.json({ message: 'Create user', data: req.body });
});

module.exports = router;
```

**server.js**
```javascript
const express = require('express');
const app = express();
const usersRouter = require('./routes/users');

app.use(express.json());

// Mount the router
app.use('/api/users', usersRouter);

app.listen(3000);
```

## Environment Variables

Create a `.env` file in your project root:

```
PORT=3000
DB_HOST=localhost
DB_USER=admin
DB_PASS=secret123
NODE_ENV=development
```

**Using dotenv:**

```javascript
require('dotenv').config();

const PORT = process.env.PORT || 3000;
const dbHost = process.env.DB_HOST;

console.log(`Server will run on port ${PORT}`);
```

**Don't forget to add `.env` to `.gitignore`!**

## Response Methods

Express provides multiple ways to send responses:

```javascript
// Send plain text
res.send('Hello World');

// Send JSON
res.json({ message: 'Success', data: [1, 2, 3] });

// Send status code
res.sendStatus(404); // Sends "Not Found"

// Set status and send
res.status(201).json({ message: 'Created' });

// Redirect
res.redirect('/new-page');
res.redirect(301, '/permanently-moved');

// Download file
res.download('/path/to/file.pdf');

// Send file
res.sendFile('/path/to/file.html');

// Render template (with template engine)
res.render('index', { title: 'My App' });
```

## Development Tools

### Nodemon (Auto-restart on Changes)

Install as dev dependency:

```bash
npm install --save-dev nodemon
```

Update `package.json` scripts:

```json
"scripts": {
  "start": "node server.js",
  "dev": "nodemon server.js"
}
```

Run with:
```bash
npm run dev
```

## Common Patterns

### API Response Helper

```javascript
const sendSuccess = (res, data, message = 'Success') => {
  res.json({
    success: true,
    message,
    data
  });
};

const sendError = (res, status, message) => {
  res.status(status).json({
    success: false,
    message
  });
};

// Usage
app.get('/api/data', (req, res) => {
  const data = { id: 1, name: 'Example' };
  sendSuccess(res, data, 'Data retrieved successfully');
});
```

### Async Route Handlers

```javascript
// Wrapper to catch async errors
const asyncHandler = fn => (req, res, next) => {
  Promise.resolve(fn(req, res, next)).catch(next);
};

// Usage
app.get('/api/users', asyncHandler(async (req, res) => {
  const users = await db.getAllUsers(); // Async database call
  res.json(users);
}));
```

### Validation Middleware

```javascript
const validateUser = (req, res, next) => {
  const { name, email } = req.body;
  
  if (!name || !email) {
    return res.status(400).json({ 
      error: 'Name and email are required' 
    });
  }
  
  if (!email.includes('@')) {
    return res.status(400).json({ 
      error: 'Invalid email format' 
    });
  }
  
  next(); // Validation passed, continue to route handler
};

app.post('/api/users', validateUser, (req, res) => {
  res.json({ message: 'User created', user: req.body });
});
```

## Package.json Scripts Setup

Here's a recommended scripts section for your `package.json`:

```json
{
  "name": "my-express-app",
  "version": "1.0.0",
  "description": "Express.js application",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": ["express", "nodejs", "api"],
  "author": "Your Name",
  "license": "ISC",
  "dependencies": {
    "express": "^4.18.2",
    "dotenv": "^16.0.3",
    "cors": "^2.8.5",
    "helmet": "^7.0.0",
    "morgan": "^1.10.0"
  },
  "devDependencies": {
    "nodemon": "^3.0.1"
  }
}
```

## Testing Your API

You can test your Express routes using:

**1. Browser** (for GET requests only)
- Navigate to `http://localhost:3000/your-route`

**2. cURL** (command line)
```bash
# GET request
curl http://localhost:3000/api/users

# POST request
curl -X POST http://localhost:3000/api/users \
  -H "Content-Type: application/json" \
  -d '{"name":"John","email":"john@example.com"}'
```

**3. Postman or Thunder Client**
- GUI tools for testing APIs with all HTTP methods

**4. VS Code REST Client Extension**
Create a `test.http` file:
```
### Get all users
GET http://localhost:3000/api/users

### Create a user
POST http://localhost:3000/api/users
Content-Type: application/json

{
  "name": "John Doe",
  "email": "john@example.com"
}
```

## Complete Starter Template

Here's everything together:

```javascript
// Load environment variables
require('dotenv').config();

// Import dependencies
const express = require('express');
const morgan = require('morgan');
const cors = require('cors');
const helmet = require('helmet');

// Initialize Express app
const app = express();
const PORT = process.env.PORT || 3000;

// Middleware
app.use(helmet()); // Security headers
app.use(cors()); // Enable CORS
app.use(morgan('dev')); // HTTP request logging
app.use(express.json()); // Parse JSON bodies
app.use(express.urlencoded({ extended: true })); // Parse URL-encoded bodies
app.use(express.static('public')); // Serve static files

// Routes
app.get('/', (req, res) => {
  res.json({ message: 'Welcome to the API' });
});

app.get('/health', (req, res) => {
  res.json({ status: 'OK', timestamp: new Date().toISOString() });
});

// Import and use route modules
const usersRouter = require('./routes/users');
app.use('/api/users', usersRouter);

// 404 handler
app.use((req, res) => {
  res.status(404).json({ error: 'Route not found' });
});

// Error handling middleware
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(err.status || 500).json({ 
    error: err.message || 'Internal Server Error' 
  });
});

// Start server
app.listen(PORT, () => {
  console.log(`🚀 Server running on http://localhost:${PORT}`);
  console.log(`📝 Environment: ${process.env.NODE_ENV || 'development'}`);
});
```

## Next Steps

Once you're comfortable with the basics, explore:

1. **Template Engines**: EJS, Pug, Handlebars for server-side rendering
2. **Database Integration**: MongoDB (with Mongoose), PostgreSQL (with Sequelize)
3. **Authentication**: JWT, Passport.js, sessions
4. **Validation Libraries**: Joi, express-validator
5. **API Documentation**: Swagger/OpenAPI
6. **Testing**: Jest, Mocha, Supertest
7. **Production Deployment**: PM2, Docker, cloud platforms

## Best Practices

1. **Use environment variables** for configuration
2. **Organize routes** using Express Router
3. **Implement error handling** middleware
4. **Validate input** before processing
5. **Use async/await** for asynchronous operations
6. **Log requests** for debugging and monitoring
7. **Secure your app** with helmet and proper CORS configuration
8. **Never commit sensitive data** (use .env and .gitignore)
9. **Keep dependencies updated** but test thoroughly
10. **Structure your code** logically with separate files for routes, controllers, and models

## Conclusion

Express.js simplifies building web servers and APIs in Node.js. This tutorial covered the fundamentals of initialization, routing, middleware, and project structure. With this foundation, you can build robust web applications and RESTful APIs. Happy coding!
