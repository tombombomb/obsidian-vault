# Termux + Node.js Setup Guide

## Initial Setup

### Install Termux

**Important:** Install from **F-Droid**, not Google Play Store
- Google Play version is outdated and unsupported
- Download F-Droid app first, then install Termux through it

### First-time Setup

```bash
# Update package lists
pkg update && pkg upgrade
```

Accept all prompts to update packages.

## Installing Node.js

```bash
# Install Node.js (includes npm)
pkg install nodejs

# Verify installation
node --version
npm --version
```

## Storage Access

```bash
# Grant access to phone storage
termux-setup-storage
```

This creates `~/storage/` with access to:
- `~/storage/shared` - Internal storage
- `~/storage/downloads` - Downloads folder
- `~/storage/dcim` - Camera photos
- `~/storage/pictures` - Pictures

## Basic Node.js Usage

### Interactive REPL

```bash
# Start Node.js interactive mode
node

# Try some JavaScript
> console.log("Hello World")
> 2 + 2
> .exit  # Exit REPL
```

### Running JavaScript Files

```bash
# Create a test file
echo "console.log('Hello from Node');" > test.js

# Run it
node test.js

# Or make it executable
chmod +x test.js
./test.js  # Add #!/usr/bin/env node at top of file
```

### Using npm

```bash
# Install packages locally
npm install express

# Install globally
npm install -g nodemon

# Initialize a new project
npm init
npm init -y  # Skip questions

# Install from package.json
npm install
```

## Text Editors

```bash
# Install nano (beginner-friendly)
pkg install nano

# Use nano
nano myfile.js
# Ctrl+O to save, Ctrl+X to exit

# Or install vim
pkg install vim
```

## Git Integration

```bash
# Install git
pkg install git

# Configure
git config --global user.name "Your Name"
git config --global user.email "your@email.com"

# Clone repositories
git clone git@github.com:username/repo.git
cd repo
node script.js
```

## File Transfer Methods

### Method 1: Git (Recommended)

```bash
# On computer: push to GitHub
git push origin main

# On Termux: pull changes
git pull origin main
node updated-script.js
```

### Method 2: Shared Storage

```bash
# Access files from computer (USB/cloud sync)
cd ~/storage/shared
# Or
cd ~/storage/downloads

# Copy to Termux home for editing
cp ~/storage/downloads/script.js ~/
node script.js
```

### Method 3: SSH/SCP

```bash
# On Termux: start SSH server
pkg install openssh
sshd

# Get phone's IP address
ifconfig

# From computer:
scp script.js username@phone-ip:~/
```

## Useful Packages

```bash
# Essential tools
pkg install git
pkg install openssh
pkg install nano
pkg install wget
pkg install curl

# Development tools
pkg install python
pkg install nodejs
pkg install clang  # C/C++ compiler
```

## Common npm Packages for Practice

```bash
# Web server framework
npm install express

# Utilities
npm install lodash
npm install axios
npm install dotenv

# Development tools
npm install -g nodemon  # Auto-restart on changes
npm install -g http-server  # Simple HTTP server
```

## Example Projects

### Simple HTTP Server

```javascript
// server.js
const http = require('http');

const server = http.createServer((req, res) => {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Hello from Termux!\n');
});

server.listen(3000, () => {
  console.log('Server running at http://localhost:3000/');
});
```

```bash
node server.js
# Visit localhost:3000 in phone browser
```

### Express API

```javascript
// app.js
const express = require('express');
const app = express();

app.get('/', (req, res) => {
  res.json({ message: 'API running on Termux!' });
});

app.get('/data', (req, res) => {
  res.json({ 
    timestamp: new Date(),
    status: 'active'
  });
});

app.listen(3000, () => {
  console.log('API running on port 3000');
});
```

```bash
npm install express
node app.js
```

## Termux Shortcuts

### Navigation

```bash
# Home directory
cd ~

# Previous directory
cd -

# List files
ls
ls -la  # Detailed view with hidden files

# Current directory path
pwd
```

### Process Management

```bash
# Run in background
node server.js &

# List running processes
ps

# Kill process
kill PID

# Stop current process
Ctrl+C
```

### Terminal Tips

- **Ctrl+C** - Stop current command
- **Ctrl+D** - Exit shell/REPL
- **Ctrl+L** - Clear screen (or type `clear`)
- **Volume Down + C** - Copy
- **Volume Down + V** - Paste
- **Volume Down + Arrow Keys** - Navigate
- **Long press** - Select text

## Common Issues & Solutions

### "Permission denied" errors
```bash
# Fix script permissions
chmod +x script.js
```

### Storage not accessible
```bash
# Re-run storage setup
termux-setup-storage
```

### Package installation fails
```bash
# Update repositories
pkg update
pkg upgrade

# Try again
pkg install package-name
```

### Node module not found
```bash
# Install in current directory
npm install module-name

# Or globally
npm install -g module-name
```

## Useful Commands Reference

| Command | Description |
|---------|-------------|
| `pkg search name` | Search for packages |
| `pkg show name` | Show package details |
| `pkg list-installed` | List installed packages |
| `pkg uninstall name` | Remove package |
| `termux-info` | Show system info |
| `termux-setup-storage` | Access phone storage |
| `exit` | Close Termux |

## Best Practices

1. **Always update before installing:**
   ```bash
   pkg update && pkg upgrade
   ```

2. **Use git for code sync** - cleanest workflow between computer and phone

3. **Keep projects organized:**
   ```bash
   mkdir ~/projects
   cd ~/projects
   mkdir project-name
   ```

4. **Use version control** - commit frequently when learning

5. **Test on phone first** - verify scripts work before pushing to GitHub

## Project Structure Example

```
~/projects/
├── learning-js/
│   ├── basics/
│   ├── node-practice/
│   └── README.md
├── scripts/
│   ├── automation/
│   └── utilities/
└── repos/
    └── (cloned repositories)
```

## Next Steps

- Practice basic JavaScript in Node REPL
- Clone a repository and run existing code
- Create simple scripts and sync via git
- Build a small Express API
- Experiment with npm packages
- Connect to databases (SQLite works great in Termux)

## Resources

- Termux Wiki: https://wiki.termux.com
- Node.js Docs: https://nodejs.org/docs
- npm Registry: https://npmjs.com
