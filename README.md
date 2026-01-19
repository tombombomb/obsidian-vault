# Lenel OnGuard OpenAccess Express.js API

A Node.js Express.js middleware application that provides a simplified REST API for integrating with Lenel OnGuard access control systems via the OpenAccess API. This application acts as a bridge between your applications (such as n8n workflows, custom dashboards, or other systems) and the Lenel OnGuard server.

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [How It Works](#how-it-works)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Configuration](#configuration)
- [Running the Application](#running-the-application)
- [API Reference](#api-reference)
- [Usage Examples](#usage-examples)
- [Understanding Lenel Concepts](#understanding-lenel-concepts)
- [Error Handling](#error-handling)
- [Security Considerations](#security-considerations)
- [Troubleshooting](#troubleshooting)

## Overview

### What is Lenel OnGuard?

Lenel OnGuard is an enterprise access control system used to manage physical security - controlling who can enter buildings, rooms, and secure areas through card readers, badges, and access permissions.

### What is OpenAccess?

OpenAccess is Lenel's REST API that allows external applications to interact with the OnGuard system programmatically. It enables you to:

- Manage cardholders (people in the system)
- Issue and manage badges (access credentials)
- Control hardware (open doors, lock/unlock readers)
- Query access events and logs
- Manage access levels and permissions

### Why This Middleware?

The OpenAccess API has some complexities:
- Requires session token management with keepalive
- Uses a specific query syntax
- Requires an Application-Id for all requests
- Uses non-standard endpoint patterns

This Express.js application simplifies all of that into a clean, familiar REST API that's easy to integrate with any system.

## Architecture

```
┌─────────────────┐      ┌─────────────────────┐      ┌─────────────────┐
│                 │      │                     │      │                 │
│  Your App       │──────│  This Express API   │──────│  Lenel OnGuard  │
│  (n8n, custom)  │ HTTP │  (Middleware)       │HTTPS │  Server         │
│                 │      │                     │      │                 │
└─────────────────┘      └─────────────────────┘      └─────────────────┘
                               │
                               │ Handles:
                               │ - Session management
                               │ - Token refresh
                               │ - Request formatting
                               │ - Error normalization
```

## How It Works

### 1. Authentication Flow

When the application starts (or when you call `/api/auth/login`):

1. The app sends credentials to the Lenel OpenAccess `/authentication` endpoint
2. Lenel returns a session token (valid for 8 hours)
3. The app stores this token in memory
4. All subsequent requests include this token automatically
5. A keepalive runs every 5 minutes to prevent the 15-minute inactivity timeout

```
App Start → Login to Lenel → Store Token → Start Keepalive Timer
                                              ↓
                                    Every 5 min: Send keepalive
                                              ↓
                              Token expires after 8h → Re-authenticate
```

### 2. Request Flow

When you make a request to this API:

```
Your Request                    This Middleware                 Lenel Server
     │                               │                               │
     │  GET /api/cardholders         │                               │
     │ ─────────────────────────────>│                               │
     │                               │                               │
     │                               │  GET /instances?type_name=    │
     │                               │  Lnl_Cardholder&version=1.0   │
     │                               │  + Headers (App-Id, Token)    │
     │                               │ ─────────────────────────────>│
     │                               │                               │
     │                               │     Raw Lenel Response        │
     │                               │ <─────────────────────────────│
     │                               │                               │
     │   Normalized JSON Response    │                               │
     │ <─────────────────────────────│                               │
```

### 3. Data Types in Lenel

Lenel uses "type names" to identify different data entities. This middleware translates friendly endpoints to these types:

| This API Endpoint | Lenel Type Name | Description |
|-------------------|-----------------|-------------|
| `/api/cardholders` | `Lnl_Cardholder` | People in the system |
| `/api/badges` | `Lnl_Badge` | Access credentials/cards |
| `/api/readers` | `Lnl_Reader` | Card reader devices |
| `/api/panels` | `Lnl_Panel` | Access control panels |
| `/api/access-levels` | `Lnl_AccessLevel` | Permission groups |
| `/api/events` | `Lnl_LoggedEvent` | Access event history |

## Project Structure

```
lenel-express-api/
├── src/
│   ├── app.js                    # Application entry point
│   │                             # - Initializes Express
│   │                             # - Sets up middleware (helmet, cors)
│   │                             # - Mounts routes
│   │                             # - Handles graceful shutdown
│   │
│   ├── config/
│   │   └── index.js              # Configuration management
│   │                             # - Loads environment variables
│   │                             # - Provides typed config object
│   │
│   ├── services/                 # Business logic layer
│   │   ├── lenelClient.js        # Axios HTTP client
│   │   │                         # - Configures base URL
│   │   │                         # - Adds headers automatically
│   │   │                         # - Handles SSL for self-signed certs
│   │   │                         # - Transforms errors
│   │   │
│   │   ├── authService.js        # Authentication management
│   │   │                         # - Login/logout
│   │   │                         # - Token storage
│   │   │                         # - Automatic keepalive
│   │   │                         # - Session status
│   │   │
│   │   ├── cardholderService.js  # Cardholder operations
│   │   ├── badgeService.js       # Badge operations
│   │   ├── readerService.js      # Reader queries
│   │   ├── panelService.js       # Panel queries
│   │   ├── accessLevelService.js # Access level queries
│   │   ├── hardwareService.js    # Hardware commands
│   │   │                         # - Open/lock/unlock doors
│   │   │                         # - Control outputs
│   │   │
│   │   └── eventService.js       # Event log queries
│   │
│   ├── routes/                   # HTTP route handlers
│   │   ├── index.js              # Route aggregator
│   │   ├── auth.js               # /api/auth/* routes
│   │   ├── cardholders.js        # /api/cardholders/* routes
│   │   ├── badges.js             # /api/badges/* routes
│   │   ├── readers.js            # /api/readers/* routes
│   │   ├── panels.js             # /api/panels/* routes
│   │   ├── accessLevels.js       # /api/access-levels/* routes
│   │   ├── commands.js           # /api/commands/* routes
│   │   ├── events.js             # /api/events/* routes
│   │   └── system.js             # /api/system/* routes
│   │
│   ├── middleware/
│   │   ├── errorHandler.js       # Global error handling
│   │   │                         # - Catches all errors
│   │   │                         # - Normalizes error responses
│   │   │                         # - Logs errors
│   │   │
│   │   ├── logger.js             # Request logging
│   │   │                         # - Logs incoming requests
│   │   │                         # - Logs response times
│   │   │
│   │   └── validateRequest.js    # Request validation
│   │                             # - Validates required fields
│   │                             # - Type checking
│   │
│   └── utils/
│       ├── logger.js             # Winston logger setup
│       │                         # - Console logging
│       │                         # - File logging in production
│       │
│       └── helpers.js            # Utility functions
│                                 # - Filter string building
│                                 # - Pagination formatting
│                                 # - Data masking for logs
│
├── .env.example                  # Example environment config
├── .gitignore                    # Git ignore rules
├── package.json                  # Dependencies and scripts
└── README.md                     # This file
```

## Installation

1. **Clone or copy the project:**
   ```bash
   cd lenel-express-api
   ```

2. **Install dependencies:**
   ```bash
   npm install
   ```

3. **Create your environment file:**
   ```bash
   cp .env.example .env
   ```

4. **Edit `.env` with your settings** (see Configuration section)

## Configuration

### Environment Variables

| Variable | Required | Default | Description |
|----------|----------|---------|-------------|
| `PORT` | No | `3000` | Port the API server listens on |
| `NODE_ENV` | No | `development` | Environment (`development` or `production`) |
| `LENEL_HOST` | **Yes** | - | Hostname of your OnGuard server |
| `LENEL_PORT` | No | `8080` | OpenAccess API port |
| `LENEL_APP_ID` | **Yes** | - | Your Application ID from LenelS2 |
| `LENEL_USERNAME` | **Yes** | - | OnGuard username for API access |
| `LENEL_PASSWORD` | **Yes** | - | OnGuard password |
| `LENEL_DIRECTORY_ID` | No | `""` | Directory ID (for AD authentication) |
| `REJECT_UNAUTHORIZED` | No | `false` | Set to `true` in production with valid SSL |
| `SESSION_KEEPALIVE_INTERVAL` | No | `300000` | Keepalive interval in ms (5 min) |
| `TOKEN_REFRESH_BUFFER` | No | `1800000` | Re-auth buffer before expiry (30 min) |
| `LOG_LEVEL` | No | `info` | Logging level (`debug`, `info`, `warn`, `error`) |

### Example `.env` File

```env
# Server
PORT=3000
NODE_ENV=development

# Lenel OnGuard Connection
LENEL_HOST=onguard.yourcompany.com
LENEL_PORT=8080
LENEL_APP_ID=abc123-your-app-id-from-lenel
LENEL_USERNAME=api_user
LENEL_PASSWORD=your_secure_password
LENEL_DIRECTORY_ID=

# SSL (set to true in production)
REJECT_UNAUTHORIZED=false

# Session
SESSION_KEEPALIVE_INTERVAL=300000
TOKEN_REFRESH_BUFFER=1800000

# Logging
LOG_LEVEL=info
```

### Getting Your Application ID

You must obtain an Application ID from LenelS2 to use the OpenAccess API. This is typically done through:
1. Contacting LenelS2 support
2. Registering your application in the OnGuard System Administration

## Running the Application

### Development Mode

Uses Node.js `--watch` for auto-reload on file changes:

```bash
npm run dev
```

### Production Mode

```bash
npm start
```

### What Happens on Startup

1. Loads environment configuration
2. Initializes Express with security middleware (Helmet, CORS)
3. Sets up request logging
4. Mounts all API routes
5. Attempts auto-login to Lenel (if credentials configured)
6. Starts the HTTP server
7. Begins automatic keepalive timer

You'll see output like:

```
2024-01-15 10:30:00 [info]: Attempting auto-login to Lenel OnGuard...
2024-01-15 10:30:01 [info]: Successfully authenticated with Lenel OnGuard
2024-01-15 10:30:01 [info]: Server running on port 3000
2024-01-15 10:30:01 [info]: Environment: development
2024-01-15 10:30:01 [info]: Lenel host: onguard.yourcompany.com:8080
```

## API Reference

### Response Format

All successful responses:

```json
{
  "success": true,
  "data": { },
  "pagination": {
    "page": 1,
    "pageSize": 20,
    "totalItems": 150,
    "totalPages": 8
  }
}
```

All error responses:

```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error description"
  }
}
```

### Authentication Endpoints

#### Login
```http
POST /api/auth/login
Content-Type: application/json

{
  "username": "optional_override",
  "password": "optional_override",
  "directoryId": "optional_for_ad"
}
```

If no body is provided, uses credentials from environment variables.

#### Logout
```http
POST /api/auth/logout
```

#### Get Session Status
```http
GET /api/auth/session
```

Response:
```json
{
  "success": true,
  "data": {
    "hasSession": true,
    "sessionToken": "***ACTIVE***",
    "tokenExpiration": "2024-01-15T18:30:00.000Z",
    "isExpired": false,
    "expiresIn": 28800000
  }
}
```

#### List Authentication Directories
```http
GET /api/auth/directories
```

#### Send Keepalive
```http
POST /api/auth/keepalive
```

### Cardholder Endpoints

#### List Cardholders
```http
GET /api/cardholders?page=1&pageSize=20
GET /api/cardholders?filter=LastName = "Smith"
GET /api/cardholders?firstName=John&lastName=Smith
```

#### Get Cardholder by ID
```http
GET /api/cardholders/12345
```

#### Create Cardholder
```http
POST /api/cardholders
Content-Type: application/json

{
  "FIRSTNAME": "John",
  "LASTNAME": "Smith",
  "EMAIL": "john.smith@example.com"
}
```

#### Update Cardholder
```http
PUT /api/cardholders/12345
Content-Type: application/json

{
  "EMAIL": "john.smith.new@example.com"
}
```

#### Delete Cardholder
```http
DELETE /api/cardholders/12345
```

#### Get Cardholder's Badges
```http
GET /api/cardholders/12345/badges
```

### Badge Endpoints

#### List Badges
```http
GET /api/badges?page=1&pageSize=20
GET /api/badges?filter=STATUS = 1
```

#### Get Badge Types
```http
GET /api/badges/types
```

#### Get Badge Statuses
```http
GET /api/badges/statuses
```

#### Get Badge by Key
```http
GET /api/badges/98765
```

#### Create Badge
```http
POST /api/badges
Content-Type: application/json

{
  "ID": 12345678,
  "PERSONID": 12345,
  "TYPE": 1,
  "STATUS": 1
}
```

#### Update Badge
```http
PUT /api/badges/98765
Content-Type: application/json

{
  "STATUS": 2
}
```

#### Delete Badge
```http
DELETE /api/badges/98765
```

#### Get Badge Access Levels
```http
GET /api/badges/98765/access-levels
```

#### Assign Access Level to Badge
```http
POST /api/badges/98765/access-levels
Content-Type: application/json

{
  "accessLevelId": 5
}
```

#### Remove Access Level from Badge
```http
DELETE /api/badges/98765/access-levels/5
```

### Reader Endpoints

#### List Readers
```http
GET /api/readers?page=1&pageSize=20
```

#### Get Reader
```http
GET /api/readers/1/1
```
(Panel ID 1, Reader ID 1)

#### Get Reader Status
```http
GET /api/readers/1/1/status
```

### Panel Endpoints

#### List Panels
```http
GET /api/panels
```

#### Get Panel
```http
GET /api/panels/1
```

#### Get Panel Status
```http
GET /api/panels/1/status
```

#### Get Readers on Panel
```http
GET /api/panels/1/readers
```

### Access Level Endpoints

#### List Access Levels
```http
GET /api/access-levels
```

#### Get Access Level
```http
GET /api/access-levels/5
```

### Hardware Command Endpoints

#### Open Door (Momentary)
```http
POST /api/commands/door/open
Content-Type: application/json

{
  "panelId": 1,
  "readerId": 1
}
```

#### Lock Door
```http
POST /api/commands/door/lock
Content-Type: application/json

{
  "panelId": 1,
  "readerId": 1
}
```

#### Unlock Door
```http
POST /api/commands/door/unlock
Content-Type: application/json

{
  "panelId": 1,
  "readerId": 1
}
```

#### Set Reader Mode
```http
POST /api/commands/reader/mode
Content-Type: application/json

{
  "panelId": 1,
  "readerId": 1,
  "mode": 0
}
```

Reader Modes:
| Mode | Name | Description |
|------|------|-------------|
| 0 | Card Only | Requires valid card |
| 1 | PIN Only | Requires PIN entry |
| 2 | Card and PIN | Requires both |
| 3 | Card or PIN | Either works |
| 4 | Facility Code | Facility code only |
| 5 | Cipher Mode | Special cipher |
| 6 | Disabled | No access control (open) |
| 7 | Locked | No entry allowed |

#### Activate Output
```http
POST /api/commands/output/activate
Content-Type: application/json

{
  "panelId": 1,
  "outputId": 1
}
```

#### Deactivate Output
```http
POST /api/commands/output/deactivate
Content-Type: application/json

{
  "panelId": 1,
  "outputId": 1
}
```

#### Pulse Output
```http
POST /api/commands/output/pulse
Content-Type: application/json

{
  "panelId": 1,
  "outputId": 1,
  "duration": 5
}
```

### Event Endpoints

#### Query Events
```http
GET /api/events?startTime=2024-01-01T00:00:00Z&endTime=2024-01-15T23:59:59Z&page=1&pageSize=50
```

#### Get Event Types
```http
GET /api/events/types
```

#### Get Recent Events
```http
GET /api/events/recent?hours=24&page=1&pageSize=50
```

### System Endpoints

#### Get OnGuard Version
```http
GET /api/system/version
```

#### Health Check
```http
GET /api/system/health
```

Response:
```json
{
  "success": true,
  "data": {
    "status": "healthy",
    "uptime": {
      "ms": 3600000,
      "formatted": "1h 0m 0s"
    },
    "lenel": {
      "status": "connected",
      "session": {
        "active": true,
        "expiresIn": 25200000
      }
    },
    "timestamp": "2024-01-15T11:30:00.000Z"
  }
}
```

#### List All Data Types
```http
GET /api/system/types
```

#### Get Type Schema
```http
GET /api/system/type/Lnl_Cardholder
```

## Usage Examples

### Using with cURL

```bash
# Check health
curl http://localhost:3000/api/system/health

# Login (if not auto-logged in)
curl -X POST http://localhost:3000/api/auth/login

# Get all cardholders
curl http://localhost:3000/api/cardholders

# Search for cardholders
curl "http://localhost:3000/api/cardholders?lastName=Smith"

# Create a cardholder
curl -X POST http://localhost:3000/api/cardholders \
  -H "Content-Type: application/json" \
  -d '{"FIRSTNAME":"Jane","LASTNAME":"Doe","EMAIL":"jane@example.com"}'

# Open a door
curl -X POST http://localhost:3000/api/commands/door/open \
  -H "Content-Type: application/json" \
  -d '{"panelId":1,"readerId":1}'
```

### Using with JavaScript/Node.js

```javascript
const axios = require('axios');

const api = axios.create({
  baseURL: 'http://localhost:3000/api',
});

// Get all cardholders
async function getCardholders() {
  const response = await api.get('/cardholders');
  return response.data.data;
}

// Create a cardholder and assign access
async function createCardholderWithAccess(person, badgeId, accessLevelId) {
  // Create cardholder
  const chResponse = await api.post('/cardholders', {
    FIRSTNAME: person.firstName,
    LASTNAME: person.lastName,
    EMAIL: person.email,
  });
  const cardholderId = chResponse.data.data.ID;

  // Create badge
  const badgeResponse = await api.post('/badges', {
    ID: badgeId,
    PERSONID: cardholderId,
    TYPE: 1,
    STATUS: 1,
  });
  const badgeKey = badgeResponse.data.data.BADGEKEY;

  // Assign access level
  await api.post(`/badges/${badgeKey}/access-levels`, {
    accessLevelId: accessLevelId,
  });

  return { cardholderId, badgeKey };
}

// Open a door
async function openDoor(panelId, readerId) {
  await api.post('/commands/door/open', { panelId, readerId });
}
```

### Using with n8n

In n8n, use the HTTP Request node:

1. **Method**: GET/POST/PUT/DELETE as needed
2. **URL**: `http://your-server:3000/api/cardholders`
3. **Authentication**: None (handled internally)
4. **Headers**: `Content-Type: application/json` for POST/PUT

## Understanding Lenel Concepts

### Cardholders vs Badges

- **Cardholder**: A person in the system (employee, contractor, visitor)
- **Badge**: A credential assigned to a cardholder (physical card, fob, mobile credential)
- One cardholder can have multiple badges
- Badges have a unique `BADGEKEY` (system-assigned) and `ID` (badge number)

### Access Levels

Access levels define what doors/areas a badge can access and when. They contain:
- Reader assignments (which readers grant access)
- Timezone restrictions (when access is allowed)

### Panels and Readers

- **Panel**: Hardware controller that connects to readers, inputs, outputs
- **Reader**: Device that reads badge credentials (card reader, keypad, biometric)
- Readers are identified by `PanelID` + `ReaderID` combination

### Event Flow

When someone badges at a reader:
1. Reader reads credential
2. Panel validates against access levels
3. Access granted/denied
4. Event logged in `Lnl_LoggedEvent`
5. Door unlocks (if granted)

## Error Handling

### Common Error Codes

| HTTP Status | Code | Description |
|-------------|------|-------------|
| 400 | `VALIDATION_ERROR` | Missing or invalid parameters |
| 401 | `UNAUTHORIZED` | Invalid or expired session |
| 403 | `FORBIDDEN` | Insufficient permissions |
| 404 | `NOT_FOUND` | Resource not found |
| 500 | `INTERNAL_ERROR` | Server error |
| 500 | `LENEL_API_ERROR` | Error from Lenel server |

### Automatic Session Recovery

If a request fails with 401:
1. The middleware attempts re-authentication
2. Retries the original request
3. Returns error only if re-auth fails

## Security Considerations

1. **Network Security**
   - Run this API on an internal network only
   - Use a reverse proxy (nginx) with authentication if external access needed
   - Consider API keys or OAuth for your clients

2. **SSL/TLS**
   - Set `REJECT_UNAUTHORIZED=true` in production
   - Use valid SSL certificates on the OnGuard server
   - Run this API behind HTTPS in production

3. **Credentials**
   - Never commit `.env` to version control
   - Use a service account with minimum required permissions
   - Rotate passwords regularly

4. **Logging**
   - Sensitive data (passwords, tokens) are automatically masked
   - Review logs don't contain sensitive information

5. **Rate Limiting**
   - OpenAccess is not designed for high-volume requests
   - Add delays between bulk operations
   - Consider implementing rate limiting on this API

## Troubleshooting

### Connection Refused

```
Error: connect ECONNREFUSED
```

- Verify `LENEL_HOST` and `LENEL_PORT` are correct
- Check firewall rules allow connection
- Verify OpenAccess service is running on the OnGuard server

### SSL Certificate Errors

```
Error: self signed certificate
```

- For development: Set `REJECT_UNAUTHORIZED=false`
- For production: Install valid SSL certificate or add the CA to trusted roots

### Authentication Failed

```
Error: 401 Unauthorized
```

- Verify username/password are correct
- Check the user has API access permissions in OnGuard
- Verify `LENEL_APP_ID` is valid

### Session Timeout

If you see frequent re-authentication:
- Ensure keepalive interval is less than 15 minutes
- Check network stability between this app and OnGuard server

### No Data Returned

```json
{ "success": true, "data": [] }
```

- Check user has read permissions for the data type
- Verify filter syntax is correct
- Confirm data exists in OnGuard

### Debug Logging

Enable debug logging for more details:

```env
LOG_LEVEL=debug
```
