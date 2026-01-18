# Express.js Webhook Server Tutorial

A comprehensive guide to building webhook servers with Express.js, with line-by-line explanations.

---

## Table of Contents

1. [Basic Webhook Server](#basic-webhook-server)
2. [Enhanced Version with Logging](#enhanced-version-with-logging)
3. [Secure Webhooks with Signature Verification](#secure-webhooks-with-signature-verification)
4. [Testing Your Webhook Server](#testing-your-webhook-server)

---

## Basic Webhook Server

### Complete Code

```javascript
const express = require('express');
const app = express();
const PORT = process.env.PORT || 3000;

app.use(express.json());
app.use(express.urlencoded({ extended: true }));

app.post('/webhook', (req, res) => {
  console.log('Webhook received:', req.body);
  
  const payload = req.body;
  
  res.status(200).json({ 
    success: true, 
    message: 'Webhook received successfully' 
  });
});

app.get('/health', (req, res) => {
  res.status(200).json({ status: 'OK' });
});

app.listen(PORT, () => {
  console.log(`Webhook server running on port ${PORT}`);
});
```

### Line-by-Line Explanation

```javascript
const express = require('express');
```
**What it does:** Imports the Express.js framework into your application.
**Why:** Express is a minimal web framework that makes it easy to create web servers and handle HTTP requests/responses.

---

```javascript
const app = express();
```
**What it does:** Creates an Express application instance.
**Why:** This `app` object is what you'll use to define routes, add middleware, and configure your server.

---

```javascript
const PORT = process.env.PORT || 3000;
```
**What it does:** Sets the port number your server will listen on.
**Why:** 
- `process.env.PORT` checks if there's an environment variable called PORT (useful for deployment platforms like Heroku)
- `|| 3000` means "if PORT isn't set, use 3000 as default"
- This makes your app flexible across different environments

---

```javascript
app.use(express.json());
```
**What it does:** Adds middleware that automatically parses incoming JSON data.
**Why:** When a webhook sends JSON data like `{"event": "motion_detected"}`, this middleware converts it from a string into a JavaScript object you can easily work with via `req.body`.

**Example:**
- Without this: `req.body` would be undefined or a raw string
- With this: `req.body.event` would equal `"motion_detected"`

---

```javascript
app.use(express.urlencoded({ extended: true }));
```
**What it does:** Adds middleware that parses URL-encoded data (form data).
**Why:** 
- Some webhooks send data as form-encoded instead of JSON
- `extended: true` allows parsing complex objects and arrays, not just simple key-value pairs
- This ensures your server can handle both JSON and form submissions

---

```javascript
app.post('/webhook', (req, res) => {
```
**What it does:** Defines a route that handles POST requests to `/webhook`.
**Why:**
- `app.post()` means this route only responds to POST requests (webhooks typically use POST)
- `'/webhook'` is the URL path (your full URL would be `http://yourserver.com/webhook`)
- `(req, res) => {}` is a callback function with two parameters:
  - `req` (request) - contains data sent TO your server
  - `res` (response) - used to send data BACK to whoever called your webhook

---

```javascript
  console.log('Webhook received:', req.body);
```
**What it does:** Prints the webhook payload to your server console.
**Why:** 
- Debugging - lets you see what data is being sent
- Monitoring - helps you track webhook activity
- `req.body` contains the parsed JSON/form data sent in the request

---

```javascript
  const payload = req.body;
```
**What it does:** Stores the webhook data in a variable called `payload`.
**Why:** 
- Makes code more readable and semantic
- In a real application, you'd extract specific fields like `payload.cameraId` or `payload.eventType`
- Separates receiving data from processing it

---

```javascript
  res.status(200).json({ 
    success: true, 
    message: 'Webhook received successfully' 
  });
```
**What it does:** Sends a response back to whoever called your webhook.
**Why:**
- `res.status(200)` sets HTTP status code 200 (OK - success)
- `.json({...})` sends a JSON response back
- This tells the webhook sender that you received and processed their request successfully
- **Important:** Many webhook systems will retry if they don't get a 200 response

---

```javascript
app.get('/health', (req, res) => {
```
**What it does:** Defines a route that handles GET requests to `/health`.
**Why:**
- Health check endpoints are standard practice
- Allows you to quickly verify your server is running
- Useful for monitoring tools and load balancers
- Uses `GET` instead of `POST` because you're just checking status, not submitting data

---

```javascript
  res.status(200).json({ status: 'OK' });
```
**What it does:** Returns a simple JSON response indicating the server is healthy.
**Why:** Quick way to confirm server is alive and responding.

---

```javascript
app.listen(PORT, () => {
```
**What it does:** Starts the server and tells it to listen for incoming requests on the specified PORT.
**Why:**
- Without this line, your server code exists but isn't actually running
- The callback function `() => {}` runs once when the server successfully starts

---

```javascript
  console.log(`Webhook server running on port ${PORT}`);
});
```
**What it does:** Prints a confirmation message when the server starts.
**Why:** 
- Confirms the server started successfully
- Shows which port it's using
- Template literals (backticks) allow you to insert the `${PORT}` variable

---

## Enhanced Version with Logging

### Complete Code

```javascript
const express = require('express');
const app = express();
const PORT = process.env.PORT || 3000;

app.use(express.json());
app.use(express.urlencoded({ extended: true }));

app.use((req, res, next) => {
  console.log(`[${new Date().toISOString()}] ${req.method} ${req.path}`);
  next();
});

app.post('/webhook/camera-motion', (req, res) => {
  const { cameraId, timestamp, eventType, boundingBoxes } = req.body;
  
  console.log('Motion detected:', {
    cameraId,
    timestamp,
    eventType,
    boundingBoxes
  });
  
  res.status(200).json({ 
    received: true,
    processedAt: new Date().toISOString()
  });
});

app.post('/webhook/:source', (req, res) => {
  const source = req.params.source;
  console.log(`Webhook from ${source}:`, req.body);
  
  res.status(200).json({ 
    success: true,
    source: source
  });
});

app.use((err, req, res, next) => {
  console.error('Error:', err);
  res.status(500).json({ 
    error: 'Internal server error',
    message: err.message 
  });
});

app.listen(PORT, () => {
  console.log(`Webhook server listening on port ${PORT}`);
});
```

### Line-by-Line Explanation (New Sections Only)

```javascript
app.use((req, res, next) => {
  console.log(`[${new Date().toISOString()}] ${req.method} ${req.path}`);
  next();
});
```
**What it does:** Creates custom middleware that logs every request.
**Why:**
- `app.use()` without a path applies to ALL routes
- Runs before your route handlers
- Logs timestamp, HTTP method (GET/POST), and path
- `next()` is critical - it tells Express to continue to the next middleware/route
- Without `next()`, requests would hang

**Example output:**
```
[2025-01-18T10:30:45.123Z] POST /webhook/camera-motion
```

---

```javascript
app.post('/webhook/camera-motion', (req, res) => {
```
**What it does:** Creates a specific route for camera motion webhooks.
**Why:**
- Separates different webhook types into different endpoints
- Makes it easier to handle camera-specific data
- More organized than one generic endpoint

---

```javascript
  const { cameraId, timestamp, eventType, boundingBoxes } = req.body;
```
**What it does:** Uses destructuring to extract specific fields from the webhook payload.
**Why:**
- **Destructuring** is a shorthand syntax
- Instead of writing:
  ```javascript
  const cameraId = req.body.cameraId;
  const timestamp = req.body.timestamp;
  // etc...
  ```
- You write it all in one line
- Only extracts the fields you need, ignoring others

**Example:**
If `req.body` is:
```json
{
  "cameraId": "CAM-001",
  "timestamp": "2025-01-18T10:30:00Z",
  "eventType": "motion",
  "boundingBoxes": [[100, 200, 300, 400]],
  "otherData": "ignored"
}
```
Then:
- `cameraId` = `"CAM-001"`
- `timestamp` = `"2025-01-18T10:30:00Z"`
- `eventType` = `"motion"`
- `boundingBoxes` = `[[100, 200, 300, 400]]`

---

```javascript
  console.log('Motion detected:', {
    cameraId,
    timestamp,
    eventType,
    boundingBoxes
  });
```
**What it does:** Logs the extracted camera data in a structured format.
**Why:**
- Helps you see exactly what data the camera is sending
- Object shorthand notation: `{cameraId}` is same as `{cameraId: cameraId}`
- Makes debugging easier

---

```javascript
  res.status(200).json({ 
    received: true,
    processedAt: new Date().toISOString()
  });
```
**What it does:** Sends confirmation with timestamp.
**Why:**
- Acknowledges receipt
- Includes processing timestamp for audit trail
- `new Date().toISOString()` creates ISO 8601 timestamp (e.g., `"2025-01-18T10:30:45.123Z"`)

---

```javascript
app.post('/webhook/:source', (req, res) => {
```
**What it does:** Creates a dynamic route with a parameter.
**Why:**
- `:source` is a route parameter - it matches ANY value in that position
- Examples that would match:
  - `/webhook/hanwha` → `source = "hanwha"`
  - `/webhook/axis` → `source = "axis"`
  - `/webhook/anything` → `source = "anything"`
- Lets you handle multiple webhook sources with one route

---

```javascript
  const source = req.params.source;
```
**What it does:** Extracts the route parameter value.
**Why:**
- `req.params` contains all route parameters
- If URL is `/webhook/hanwha`, then `req.params.source` equals `"hanwha"`
- Lets you identify which system sent the webhook

---

```javascript
  console.log(`Webhook from ${source}:`, req.body);
```
**What it does:** Logs which source sent the webhook along with the data.
**Why:** Helps differentiate between different webhook sources in your logs.

---

```javascript
app.use((err, req, res, next) => {
```
**What it does:** Error-handling middleware (4 parameters instead of 3).
**Why:**
- Express recognizes functions with 4 parameters as error handlers
- Catches any errors that occur in your routes
- `err` is the error object
- Must be defined AFTER your routes

---

```javascript
  console.error('Error:', err);
  res.status(500).json({ 
    error: 'Internal server error',
    message: err.message 
  });
```
**What it does:** Logs the error and sends error response.
**Why:**
- `console.error()` prints to error stream (shows up red in some terminals)
- Status 500 = Internal Server Error
- Sends error details back to webhook sender
- `err.message` contains the error description

---

## Secure Webhooks with Signature Verification

### Complete Code

```javascript
const express = require('express');
const crypto = require('crypto');
const app = express();
const PORT = process.env.PORT || 3000;

const WEBHOOK_SECRET = process.env.WEBHOOK_SECRET || 'your-secret-key';

app.use(express.json());

function verifySignature(payload, signature) {
  const hash = crypto
    .createHmac('sha256', WEBHOOK_SECRET)
    .update(JSON.stringify(payload))
    .digest('hex');
  
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(hash)
  );
}

app.post('/webhook/secure', (req, res) => {
  const signature = req.headers['x-webhook-signature'];
  
  if (!signature || !verifySignature(req.body, signature)) {
    return res.status(401).json({ error: 'Invalid signature' });
  }
  
  console.log('Verified webhook:', req.body);
  res.status(200).json({ success: true });
});

app.listen(PORT, () => {
  console.log(`Secure webhook server running on port ${PORT}`);
});
```

### Line-by-Line Explanation

```javascript
const crypto = require('crypto');
```
**What it does:** Imports Node.js's built-in cryptography module.
**Why:**
- Provides functions for creating secure hashes
- No installation needed - comes with Node.js
- Used for verifying webhook signatures

---

```javascript
const WEBHOOK_SECRET = process.env.WEBHOOK_SECRET || 'your-secret-key';
```
**What it does:** Sets up a secret key for signature verification.
**Why:**
- This is a shared secret between you and the webhook sender
- Both sides use the same secret to create/verify signatures
- Should be stored in environment variables, not hardcoded
- **Security:** Change `'your-secret-key'` to a strong random value in production

---

```javascript
function verifySignature(payload, signature) {
```
**What it does:** Defines a function to verify webhook signatures.
**Why:**
- Separates verification logic from route handler
- Makes code reusable and testable
- `payload` = the data sent in the webhook
- `signature` = the signature sent by the webhook sender

---

```javascript
  const hash = crypto
    .createHmac('sha256', WEBHOOK_SECRET)
```
**What it does:** Creates an HMAC (Hash-based Message Authentication Code) using SHA-256 algorithm.
**Why:**
- HMAC combines a hash function with a secret key
- `'sha256'` is the hashing algorithm (very secure)
- `WEBHOOK_SECRET` is used to create the hash
- Only someone with the secret can create valid signatures

---

```javascript
    .update(JSON.stringify(payload))
```
**What it does:** Feeds the payload data into the hash function.
**Why:**
- `JSON.stringify(payload)` converts the JavaScript object to a string
- The hash needs string input, not an object
- This creates a hash of the exact payload you received

---

```javascript
    .digest('hex');
```
**What it does:** Generates the final hash in hexadecimal format.
**Why:**
- `'hex'` means the output will be a string like `"a3f5b8c9..."`
- This is the signature you'll compare against
- Alternative formats: 'base64', 'binary'

---

```javascript
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(hash)
  );
```
**What it does:** Compares the received signature with your calculated hash securely.
**Why:**
- `timingSafeEqual()` prevents timing attacks
- Regular `===` comparison could leak information through timing
- `Buffer.from()` converts strings to binary buffers for comparison
- Returns `true` if signatures match, `false` otherwise

**Security Note:** Timing attacks analyze how long comparisons take to guess the correct value. `timingSafeEqual()` always takes the same time regardless of input.

---

```javascript
app.post('/webhook/secure', (req, res) => {
  const signature = req.headers['x-webhook-signature'];
```
**What it does:** Extracts the signature from HTTP headers.
**Why:**
- Webhook senders typically send signatures in headers
- `req.headers` is an object containing all HTTP headers
- Header names are lowercase (Express normalizes them)
- `'x-webhook-signature'` is a common convention (GitHub, Stripe use similar)

---

```javascript
  if (!signature || !verifySignature(req.body, signature)) {
    return res.status(401).json({ error: 'Invalid signature' });
  }
```
**What it does:** Validates the signature and rejects invalid requests.
**Why:**
- `!signature` checks if signature header exists
- `!verifySignature(...)` checks if signature is valid
- `||` means reject if EITHER condition is true
- Status 401 = Unauthorized
- `return` stops execution - important for security
- Prevents processing webhooks from unauthorized sources

---

```javascript
  console.log('Verified webhook:', req.body);
  res.status(200).json({ success: true });
```
**What it does:** Processes verified webhooks.
**Why:**
- This code only runs if signature is valid
- Confirms the webhook came from a trusted source
- Safe to process the data now

---

## Testing Your Webhook Server

### Starting the Server

```bash
# Install dependencies first
npm install express

# Run the server
node your-server-file.js
```

### Testing with cURL

#### Basic webhook test:
```bash
curl -X POST http://localhost:3000/webhook \
  -H "Content-Type: application/json" \
  -d '{"event":"test","data":"hello"}'
```

**Explanation:**
- `curl` - command-line tool for making HTTP requests
- `-X POST` - use POST method
- `http://localhost:3000/webhook` - your webhook URL
- `-H "Content-Type: application/json"` - tells server you're sending JSON
- `-d '{...}'` - the JSON data to send

---

#### Camera motion webhook test:
```bash
curl -X POST http://localhost:3000/webhook/camera-motion \
  -H "Content-Type: application/json" \
  -d '{
    "cameraId": "CAM-001",
    "timestamp": "2025-01-18T10:30:00Z",
    "eventType": "motion",
    "boundingBoxes": [[100, 200, 300, 400]]
  }'
```

---

#### Dynamic source webhook test:
```bash
curl -X POST http://localhost:3000/webhook/hanwha \
  -H "Content-Type: application/json" \
  -d '{"camera":"lobby","motion":true}'
```

---

#### Health check test:
```bash
curl http://localhost:3000/health
```

---

### Testing with Postman

1. **Create new request**
2. **Set method to POST**
3. **Enter URL:** `http://localhost:3000/webhook`
4. **Go to Body tab**
5. **Select "raw" and "JSON"**
6. **Enter JSON data:**
   ```json
   {
     "event": "test",
     "data": "hello"
   }
   ```
7. **Click Send**

---

### Testing Secure Webhooks

To generate a valid signature for testing:

```javascript
// test-signature.js
const crypto = require('crypto');

const secret = 'your-secret-key';
const payload = { event: 'test', data: 'hello' };

const signature = crypto
  .createHmac('sha256', secret)
  .update(JSON.stringify(payload))
  .digest('hex');

console.log('Signature:', signature);
```

Run it:
```bash
node test-signature.js
```

Then use the signature in your curl request:
```bash
curl -X POST http://localhost:3000/webhook/secure \
  -H "Content-Type: application/json" \
  -H "x-webhook-signature: YOUR_GENERATED_SIGNATURE" \
  -d '{"event":"test","data":"hello"}'
```

---

## Key Concepts Summary

### Middleware
- Functions that run BEFORE your route handlers
- Used for parsing, logging, authentication
- Must call `next()` to continue to next middleware/route

### Routes
- Define endpoints (URLs) your server responds to
- Can use parameters (`:source`) for dynamic values
- Different routes for different webhook types

### Request Object (`req`)
- `req.body` - parsed request data
- `req.params` - route parameters
- `req.headers` - HTTP headers

### Response Object (`res`)
- `res.status()` - set HTTP status code
- `res.json()` - send JSON response
- `res.send()` - send plain text response

### HTTP Status Codes
- `200` - OK (success)
- `401` - Unauthorized (invalid credentials/signature)
- `500` - Internal Server Error (something went wrong)

---

## Next Steps

1. **Add database integration** to store webhook data
2. **Implement retry logic** for failed processing
3. **Add authentication** beyond just signatures
4. **Set up logging** to files, not just console
5. **Deploy** to a cloud platform (Heroku, AWS, etc.)
6. **Add rate limiting** to prevent abuse
7. **Integrate with n8n** for workflow automation

---

## Common Issues & Solutions

### Webhook hangs/never responds
**Problem:** Forgot to send a response
**Solution:** Always include `res.status(200).json(...)` or similar

### "Cannot POST /webhook" error
**Problem:** Route not defined or wrong HTTP method
**Solution:** Verify route exists and uses `app.post()`

### `req.body` is undefined
**Problem:** Missing `express.json()` middleware
**Solution:** Add `app.use(express.json())` before routes

### Port already in use
**Problem:** Another process using port 3000
**Solution:** Change PORT or kill the other process
```bash
# Linux/Mac
lsof -ti:3000 | xargs kill

# Windows
netstat -ano | findstr :3000
taskkill /PID <PID_NUMBER> /F
```

---

## Additional Resources

- [Express.js Documentation](https://expressjs.com/)
- [Node.js Crypto Module](https://nodejs.org/api/crypto.html)
- [HTTP Status Codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
- [Webhook Security Best Practices](https://webhooks.fyi/)

---

**Created for:** Tom's Node.js learning journey
**Focus areas:** Webhook handling, camera integrations, n8n automation
