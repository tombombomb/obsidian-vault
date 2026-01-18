# Complete ngrok Tutorial: Exposing Local Services to the Internet

## What is ngrok?

ngrok is a tool that creates secure tunnels from public URLs to services running on your local machine. It's perfect for:
- Testing webhooks from services like GitHub, Stripe, or your security cameras
- Sharing local development servers with clients or teammates
- Testing mobile apps against local APIs
- Bypassing firewall restrictions for development

## Installation

### Windows
1. Download ngrok from https://ngrok.com/download
2. Extract the ZIP file to a folder (e.g., `C:\ngrok`)
3. Add the folder to your PATH or run it directly from that location

### Linux/Mac
```bash
# Download and install
curl -s https://ngrok-agent.s3.amazonaws.com/ngrok.asc | sudo tee /etc/apt/trusted.gpg.d/ngrok.asc >/dev/null
echo "deb https://ngrok-agent.s3.amazonaws.com buster main" | sudo tee /etc/apt/sources.list.d/ngrok.list
sudo apt update && sudo apt install ngrok
```

### Termux (Android)
```bash
pkg install wget
wget https://bin.equinox.io/c/bNyj1mQVY4c/ngrok-v3-stable-linux-arm64.tgz
tar xvzf ngrok-v3-stable-linux-arm64.tgz
chmod +x ngrok
mv ngrok $PREFIX/bin/
```

## Setup and Authentication

### 1. Create a Free Account
- Go to https://dashboard.ngrok.com/signup
- Sign up for a free account
- This gives you better features than using without authentication

### 2. Get Your Auth Token
- Log into the ngrok dashboard
- Navigate to "Your Authtoken" section
- Copy your authtoken

### 3. Add Auth Token to ngrok
```bash
ngrok config add-authtoken YOUR_AUTH_TOKEN_HERE
```

This stores your token in `~/.ngrok2/ngrok.yml` (or `%USERPROFILE%\.ngrok2\ngrok.yml` on Windows)

## Basic Usage

### Expose a Web Server

If you have a local web server running on port 3000:
```bash
ngrok http 3000
```

You'll see output like:
```
ngrok                                                                    

Session Status                online
Account                       your-email@example.com
Version                       3.x.x
Region                        United States (us)
Latency                       50ms
Web Interface                 http://127.0.0.1:4040
Forwarding                    https://abc123.ngrok-free.app -> http://localhost:3000

Connections                   ttl     opn     rt1     rt5     p50     p90
                              0       0       0.00    0.00    0.00    0.00
```

Your local service is now accessible at `https://abc123.ngrok-free.app`

### Expose Different Ports

```bash
# Node.js app on port 8080
ngrok http 8080

# PostgreSQL database
ngrok tcp 5432

# SSH server
ngrok tcp 22

# Custom local domain
ngrok http myapp.local:3000
```

### Expose Multiple Services Simultaneously

You need to run separate terminal windows:
```bash
# Terminal 1
ngrok http 3000

# Terminal 2
ngrok http 8080
```

## Common Use Cases

### 1. Webhook Testing (Perfect for Your Camera Projects!)

When setting up webhooks from your security cameras to n8n:

```bash
# Start ngrok pointing to your n8n webhook port
ngrok http 5678

# Use the provided URL in your camera's webhook settings
# Example: https://abc123.ngrok-free.app/webhook/camera-motion
```

### 2. Local API Development

```bash
# Your Node.js backend running on port 3000
ngrok http 3000

# Now you can test from anywhere or share with others
# https://abc123.ngrok-free.app/api/endpoint
```

### 3. Testing on Mobile Devices

```bash
# Expose your local dev server
ngrok http 8080

# Access from your phone using the ngrok URL
```

## Advanced Features

### Custom Subdomain (Requires Paid Plan)

```bash
ngrok http --subdomain=myapp 3000
# Always get: https://myapp.ngrok-free.app
```

### Basic Authentication

Protect your tunnel with username/password:
```bash
ngrok http --basic-auth="username:password" 3000
```

### Reserve a Static Domain (Paid)

Instead of random URLs that change each restart, you can reserve a permanent domain in the ngrok dashboard.

### Inspect Traffic

ngrok provides a web interface at `http://127.0.0.1:4040` where you can:
- See all HTTP requests/responses
- Replay requests
- Debug webhook payloads
- View timing information

This is incredibly useful for debugging webhook integrations!

### Configuration File

Create `~/.ngrok2/ngrok.yml` for complex setups:

```yaml
version: 2
authtoken: YOUR_AUTH_TOKEN

tunnels:
  webapp:
    proto: http
    addr: 3000
    subdomain: myapp
    
  api:
    proto: http
    addr: 8080
    basic_auth:
      - "user:password"
      
  camera-webhook:
    proto: http
    addr: 5678
    # For your n8n webhook endpoint
```

Then start named tunnels:
```bash
# Start specific tunnel
ngrok start webapp

# Start multiple tunnels
ngrok start webapp api camera-webhook

# Start all tunnels
ngrok start --all
```

## Security Best Practices

### 1. Don't Expose Sensitive Data
- Never tunnel production databases or systems with real user data
- Be careful with authentication credentials during development

### 2. Use Authentication
```bash
# Add basic auth to any tunnel
ngrok http --basic-auth="user:pass" 3000

# Or use IP restrictions (paid plans)
ngrok http --cidr-allow="1.2.3.4/32" 3000
```

### 3. Temporary Use Only
- Stop ngrok when you're done testing
- Don't leave tunnels running indefinitely
- Treat ngrok URLs as temporary

### 4. Monitor Your Tunnels
- Check the web interface (http://127.0.0.1:4040) regularly
- Review who's accessing your tunnels
- Watch for suspicious activity

## Troubleshooting

### "Failed to listen on port 4040"
Another ngrok instance is running. Either:
```bash
# Kill existing ngrok process
pkill ngrok

# Or use a different web interface port
ngrok http --web-addr=4041 3000
```

### "Tunnel not found"
Check if your local service is actually running:
```bash
# Test locally first
curl http://localhost:3000
```

### Connection Refused
- Verify the port number is correct
- Make sure your service is listening on `0.0.0.0` not just `127.0.0.1`
- Check firewall settings

### Free Tier Limitations
- 1 online ngrok process at a time
- Random URLs that change on restart
- Limited requests per minute

Consider upgrading if you need:
- Multiple simultaneous tunnels
- Custom/reserved domains
- Higher request limits

## Alternatives to Consider

For production or self-hosted solutions:
- **Cloudflare Tunnel**: Free, persistent tunnels
- **localhost.run**: SSH-based, no installation needed
- **Tailscale**: Mesh VPN, great for private access
- **Self-hosted**: Set up your own reverse proxy on a VPS

## Quick Reference Commands

```bash
# Basic tunnel
ngrok http 3000

# With authentication
ngrok http --basic-auth="user:pass" 3000

# TCP tunnel (databases, SSH)
ngrok tcp 5432

# Use config file
ngrok start --config ~/ngrok.yml tunnel-name

# Check version
ngrok version

# Update ngrok
ngrok update

# View help
ngrok help
```

## Useful for Your Projects

### n8n Webhook Testing
```bash
# Start tunnel to your n8n instance
ngrok http 5678

# Use the URL for your camera webhooks
# https://abc123.ngrok-free.app/webhook/camera-motion
```

### Node.js Backend Development
```bash
# Your backend on port 3000
ngrok http 3000

# Test API endpoints remotely
# https://abc123.ngrok-free.app/api/users
```

### Database Access (Use with Caution!)
```bash
# PostgreSQL
ngrok tcp 5432

# Then connect using the ngrok TCP address
# Host: 0.tcp.ngrok.io
# Port: (shown in ngrok output)
```

## Additional Resources

- Official Documentation: https://ngrok.com/docs
- Dashboard: https://dashboard.ngrok.com
- Status Page: https://status.ngrok.com
- Community Forum: https://github.com/inconshreveable/ngrok/discussions

---

**Remember**: ngrok is a development tool. For production webhooks or permanent solutions, consider proper cloud hosting, VPS setups, or services designed for production use.
