# MTR MCP Server Setup Tutorial
## Simplified Docker Deployment Guide using project-1-13.eduhk.hk

**Target Domain**: `project-1-13.eduhk.hk`  
**Version**: 2.0 (Simplified)

---

## 📑 Table of Contents

**Quick Start:**
1. [Step 1: Clone Repository](#step-1-clone-repository) - Git clone
2. [Step 2: Environment Setup](#step-2-environment-setup-optional) - Optional `.env` file
3. [Step 3: Docker Deployment](#step-3-docker-deployment-simple) - `docker-compose up -d`
4. [Step 4: MCP Inspector Setup](#step-4-mcp-inspector-setup) - Test your server

**Production Setup (Optional):**
6. [Step 5: NGINX Configuration](#step-5-nginx-configuration-optional) - For SSL/domain
7. [Troubleshooting](#troubleshooting) - Common issues

---

## Overview

This tutorial provides a **simplified deployment** of the MTR MCP Server using only Docker and the `fastapi_mcp_integration.py` file. No complex setup required!

### What You'll Get

- ✅ **Complete MCP Server** with FastAPI integration
- ✅ **Docker containerization** for easy deployment
- ✅ **Server-Sent Events (SSE)** for real-time MCP communication
- ✅ **All MCP features**: Tools, Resources, and Prompts
- ✅ **Production-ready** endpoints under `/mcp/`
- ✅ **Optional NGINX** setup for SSL/TLS

### Simplified Architecture

```
Internet (Optional HTTPS:443)
    ↓
Optional: NGINX Reverse Proxy
    ├─ SSL/TLS termination
    └─ Location: /mcp/ → http://localhost:8080/mcp/
    ↓
Docker Container (localhost:8080)
    ├─ fastapi_mcp_integration.py (Single File!)
    ├─ All MCP functionality built-in
    └─ Auto-imports mcp_server.py functions
    ↓
Hong Kong MTR API (data.gov.hk)
```

### Key Benefits

- **Single Container**: Just run `docker-compose up -d`
- **No Complex Setup**: Everything works out of the box
- **Full MCP Protocol**: All tools, resources, and prompts included
- **Easy Testing**: Direct access via `http://localhost:8080/mcp/sse`

---

## Step 1: Clone Repository

### 1.1 Clone the Repository

```bash
# Navigate to your preferred directory
cd /opt

# Clone the MTR MCP repository
sudo git clone https://github.com/enoch-sit/mtr-mcp-example-fastapi.git

# Navigate to project directory
cd mtr-mcp-example-fastapi

# Verify repository contents
ls -la
```

**Expected files:**
```
-rw-r--r-- README.md
-rw-r--r-- MCP_GUIDE.md
-rw-r--r-- MTR_API.md
-rw-r--r-- docker-compose.yml
-rw-r--r-- Dockerfile
-rw-r--r-- fastapi_mcp_integration.py
-rw-r--r-- mcp_server.py
-rw-r--r-- requirements.txt
-rw-r--r-- nginx-mcp-location-block.conf
```

### 1.2 Project Structure

```
mtr-mcp-example-fastapi/
├── fastapi_mcp_integration.py    # FastAPI + MCP integration
├── mcp_server.py                 # Core MCP server logic
├── docker-compose.yml            # Docker deployment config
├── Dockerfile                    # Container definition
├── requirements.txt              # Python dependencies
├── nginx-mcp-location-block.conf # NGINX configuration template
├── README.md                     # Project documentation
├── MCP_GUIDE.md                  # MCP protocol guide
└── MTR_API.md                    # MTR API documentation
```

---

## Step 2: Environment Setup (Optional)

The MTR MCP Server works **out of the box** without any environment configuration. However, you can create a `.env` file for customization:

### 2.1 Basic Configuration (Optional)

```bash
# Create .env file (completely optional - server works without it)
cp .env.example .env

# Or create a minimal .env file (only if you need to change defaults):
cat > .env << 'EOF'
# Server configuration (all optional - has working defaults)
HOST_PORT=8080              # Docker port mapping (default: 8080)
LOG_LEVEL=info              # Logging level (default: info)
PYTHONUNBUFFERED=1          # Python logging (default: 1)

# Optional: AWS credentials (only for LangGraph demo)
# AWS_ACCESS_KEY_ID=your-aws-key
# AWS_SECRET_ACCESS_KEY=your-aws-secret
# AWS_REGION=us-east-1
EOF
```

**Note**: The `fastapi_mcp_integration.py` server does **not** use domain or URL environment variables. It runs on whatever port you specify (default 8080) and is accessible via that port.

### 2.2 Quick Start (No Configuration Needed)

**Skip this step entirely if you want to start immediately!** The server will run with default settings:

- **Server Port**: 8080
- **MCP Endpoint**: `http://localhost:8080/mcp/sse`
- **Health Check**: `http://localhost:8080/mcp/health`

---

## Step 3: Docker Deployment (Simple!)

### 3.1 One Command Deployment

```bash
# Navigate to project directory  
cd mtr-mcp-example-fastapi

# Start the MCP server (single command!)
sudo docker-compose up -d

# That's it! The server is now running.
```

### 3.2 Verify Deployment

```bash
# Check if container is running
docker ps

# Expected: You should see 'mtr-mcp-server' container running on port 8080
```

### 3.3 Test the Server

```bash
# Test health endpoint
curl http://localhost:8080/mcp/health

# Expected response:
# {"status":"healthy","service":"mtr-mcp-server",...}

# Test MCP endpoint (should stream data)  
curl -N -H "Accept: text/event-stream" http://localhost:8080/mcp/sse

# Expected: Streaming JSON-RPC messages (Press Ctrl+C to stop)
```

### 3.4 View Server Logs (Optional)

```bash
# Check server logs
sudo docker logs -f mtr-mcp-server

# Expected output:
# 🚀 Starting MTR MCP Server under /mcp
# 📡 SSE Endpoint: http://0.0.0.0:8080/mcp/sse  
# 📖 API Docs: http://0.0.0.0:8080/mcp/docs
# 🔍 Health Check: http://0.0.0.0:8080/mcp/health
```

**✅ Your MCP Server is now running at: `http://localhost:8080/mcp/sse`**

---

## Step 4: MCP Inspector Setup

### 4.1 Install and Launch MCP Inspector

```bash
# Install and run MCP Inspector (one command)
npx @modelcontextprotocol/inspector

# This will:
# 1. Install MCP Inspector (if not already installed)
# 2. Start the web interface on http://localhost:6274
```

### 4.2 Connect to Your Local MCP Server

1. **Open Inspector**: Navigate to `http://localhost:6274` in your browser

2. **Configure Connection**:
   - **Connection Type**: Direct
   - **Server URL**: `http://localhost:8080/mcp/sse`
   - Click **Connect**

3. **Expected Result**: ✅ Connected successfully

### 4.3 Test MCP Functions

#### Test Tools Tab

1. Click **Tools** tab
2. Select `get_next_train_schedule`
3. Fill parameters:
   - `line`: `TKL`
   - `sta`: `TKO`
   - `lang`: `EN`
4. Click **Execute**
5. **Expected**: Real MTR train schedule data

#### Test Resources Tab

1. Click **Resources** tab
2. Select `mtr://stations/list`
3. Click **Read Resource**
4. **Expected**: List of all 93 MTR stations

#### Test Prompts Tab

1. Click **Prompts** tab
2. Select `check_next_train`
3. Fill arguments:
   - `line`: `Island Line`
   - `station`: `Central`
4. Click **Get Prompt**
5. **Expected**: Formatted prompt template

> **🎉 Quick Start Complete!** Your MCP server is ready to use. The remaining steps are **optional** for production deployment with SSL/domain access.

---

## Step 5: NGINX Configuration (Optional)

**⚠️ Important**: This step is **only needed** if you want:
- **Custom domain** access (like `project-1-13.eduhk.hk`)
- **SSL/HTTPS** certificates  
- **Production deployment**

**Skip this section** if you're just testing locally with `http://localhost:8080/mcp/sse`

### 4.1 Backup Existing Configuration

### 4.2 Add MCP Location Block to Existing NGINX Configuration

Since you already have NGINX configured for `project-1-13` with SSL, you just need to add the MCP location block:

```bash
# Edit your existing NGINX configuration
sudo nano /etc/nginx/sites-available/$HOSTNAME

# Add this location block inside your existing server { } block:
```

```nginx
# Add this location block to your existing server configuration
location /mcp/ {
    # Proxy to Docker container on localhost:8080
    proxy_pass http://localhost:8080/mcp/;
    
    # Essential for SSE (Server-Sent Events)
    proxy_buffering off;           # CRITICAL: Must disable buffering for SSE
    proxy_cache off;               # Disable caching for real-time streams
    
    # Connection headers
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Forwarded-Proto $scheme;
    
    # SSE-specific headers
    proxy_set_header Connection '';  # Keep connection open
    proxy_http_version 1.1;          # Required for SSE
    
    # Timeouts for long-lived connections
    proxy_read_timeout 86400s;       # 24 hours
    proxy_send_timeout 86400s;       # 24 hours
    
    # Disable compression for SSE
    gzip off;
    
    # CORS headers (if Inspector is accessed from different domain)
    add_header 'Access-Control-Allow-Origin' '*' always;
    add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS' always;
    add_header 'Access-Control-Allow-Headers' 'Content-Type, Accept' always;
    
    # Handle OPTIONS preflight requests
    if ($request_method = 'OPTIONS') {
        return 204;
    }
}
```

**Your complete server block should look like this:**

```nginx
server {
    listen 80;
    server_name project-1-13;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name project-1-13;
    ssl_certificate /etc/nginx/ssl/dept-wildcard.eduhk/fullchain.crt;
    ssl_certificate_key /etc/nginx/ssl/dept-wildcard.eduhk/dept-wildcard.eduhk.hk.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers on;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_stapling on;
    ssl_stapling_verify on;
    resolver 8.8.8.8 8.8.4.4 valid=300s;
    ssl_trusted_certificate /etc/nginx/ssl/dept-wildcard.eduhk/fullchain.crt;

    client_max_body_size 100M;

    # ADD THIS NEW LOCATION BLOCK:
    location /mcp/ {
        # Proxy to Docker container on localhost:8080
        proxy_pass http://localhost:8080/mcp/;
        
        # Essential for SSE (Server-Sent Events)
        proxy_buffering off;           # CRITICAL: Must disable buffering for SSE
        proxy_cache off;               # Disable caching for real-time streams
        
        # Connection headers
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        
        # SSE-specific headers
        proxy_set_header Connection '';  # Keep connection open
        proxy_http_version 1.1;          # Required for SSE
        
        # Timeouts for long-lived connections
        proxy_read_timeout 86400s;       # 24 hours
        proxy_send_timeout 86400s;       # 24 hours
        
        # Disable compression for SSE
        gzip off;
        
        # CORS headers (if Inspector is accessed from different domain)
        add_header 'Access-Control-Allow-Origin' '*' always;
        add_header 'Access-Control-Allow-Methods' 'GET, POST, OPTIONS' always;
        add_header 'Access-Control-Allow-Headers' 'Content-Type, Accept' always;
        
        # Handle OPTIONS preflight requests
        if ($request_method = 'OPTIONS') {
            return 204;
        }
    }
    # Your existing location blocks...
    # ... 
}
```

### 4.3 Test and Reload NGINX Configuration

```bash
# Test NGINX configuration for syntax errors
sudo nginx -t

# Expected output:
# nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
# nginx: configuration file /etc/nginx/nginx.conf test is successful

# If configuration is valid, reload NGINX
sudo systemctl reload nginx

# Check NGINX status
sudo systemctl status nginx
```

---

## Testing & Validation (Production)

### 6.1 Test HTTP to HTTPS Redirect

```bash
# Test HTTP redirect
curl -I http://project-1-13.eduhk.hk

# Expected output:
# HTTP/1.1 301 Moved Permanently
# Location: https://project-1-13.eduhk.hk/
```

### 6.2 Test HTTPS Health Endpoint

```bash
# Test HTTPS health check
curl https://project-1-13.eduhk.hk/mcp/health

# Expected response:
# {
#   "status": "healthy",
#   "service": "mtr-mcp-server",
#   "version": "1.0.0",
#   "timestamp": "2025-10-25T10:30:00Z"
# }
```

### 6.3 Test SSE Endpoint

```bash
# Test HTTPS SSE endpoint (should stream events)
curl -N -H "Accept: text/event-stream" https://project-1-13.eduhk.hk/mcp/sse

# Expected output (streaming):
# event: message
# data: {"jsonrpc":"2.0","method":"notifications/message","params":{"level":"info","data":{"type":"heartbeat","status":"running"}}}
#
# (Press Ctrl+C to stop)
```

### 6.4 Test MCP Info Endpoint

```bash
# Test MCP server info
curl https://project-1-13.eduhk.hk/mcp/info

# Expected response:
# {
#   "server": {
#     "name": "mtr-mcp-server",
#     "version": "1.0.0"
#   },
#   "protocol_version": "2025-06-18",
#   "capabilities": {
#     "tools": {"listChanged": true},
#     "resources": {"subscribe": true},
#     "prompts": {"listChanged": true}
#   }
# }
```

### 6.5 Test API Documentation

```bash
# Access API documentation in browser
# Visit: https://project-1-13.eduhk.hk/mcp/docs

# Or test with curl
curl https://project-1-13.eduhk.hk/mcp/docs
```

---

## LangGraph Demo (Optional)

### 8.1 Prepare Python Environment

```bash
# Navigate to project directory
cd /opt/mtr-mcp-example-fastapi

# Create Python virtual environment
python3 -m venv venv

# Activate virtual environment
source venv/bin/activate

# Install Python dependencies
pip install -r requirements.txt
```

### 8.2 Configure Environment for LangGraph

```bash
# Edit .env file to add AWS credentials (if you have them)
nano .env

# Add these lines (uncomment and fill in your credentials):
# AWS_ACCESS_KEY_ID=your-aws-access-key-here
# AWS_SECRET_ACCESS_KEY=your-aws-secret-access-key-here
# AWS_REGION=us-east-1
# BEDROCK_MODEL=amazon.nova-lite-v1:0

# Add MCP server URL
echo "MCP_SERVER_URL=https://project-1-13.eduhk.hk/mcp/sse" >> .env
```

### 8.3 Run LangGraph Demo

```bash
# Activate virtual environment
source venv/bin/activate

# Run the LangGraph demo
python langgraph_demo_full_mcp.py

# Expected output:
# ✓ MCP Server URL: https://project-1-13.eduhk.hk/mcp/sse
# ✓ Environment variables loaded
# ✓ Using model: amazon.nova-lite-v1:0
# 
# 🚀 Connecting to MCP server...
#    ✓ Connected to https://project-1-13.eduhk.hk/mcp/sse
# 
# 📚 Loading MCP Resources...
#    Found 2 resources:
#    - mtr://stations/list: MTR Stations List
#    - mtr://lines/map: MTR Lines Map
# 
# 🔧 Loading MCP Tools...
#    Found 2 tools:
#    - get_next_train_schedule: Get MTR train schedule
#    - get_next_train_structured: Get structured JSON data
# 
# 🤖 Building LangGraph Agent...
#    ✓ Agent ready with Tools, Resources, and Prompts
# 
# 💬 Starting Multi-turn Conversation with Memory
```

### 8.4 Test Without AWS (Optional)

If you don't have AWS credentials, you can still test the MCP connection:

```bash
# Test basic MCP connection
python test_connection.py

# Expected output:
# Testing MCP connection to https://project-1-13.eduhk.hk/mcp/sse
# ✓ Connected successfully
# ✓ Server info: mtr-mcp-server v1.0.0
# ✓ Tools available: 2
# ✓ Resources available: 2
# ✓ Prompts available: 3
```

---

## Troubleshooting

### Issue 1: Container Won't Start

**Symptoms**: Docker container exits immediately

**Diagnosis**:
```bash
# Check container logs
docker logs mtr-mcp-server

# Check container status
docker ps -a
```

**Solutions**:
```bash
# Rebuild container
docker-compose down
docker-compose build --no-cache
docker-compose up -d

# Check port conflicts
sudo netstat -tlnp | grep :8080
```

### Issue 2: NGINX 502 Bad Gateway

**Symptoms**: NGINX returns 502 error

**Diagnosis**:
```bash
# Check NGINX error logs
sudo tail -f /var/log/nginx/error.log

# Check if container is running
docker ps | grep mtr-mcp-server

# Test direct container connection
curl http://localhost:8080/mcp/health
```

**Solutions**:
```bash
# Restart container
docker-compose restart

# Check NGINX configuration
sudo nginx -t

# Restart NGINX
sudo systemctl restart nginx
```

### Issue 3: SSL Certificate Issues

**Symptoms**: Browser shows SSL warnings

**Diagnosis**:
```bash
# Check certificate status
sudo certbot certificates

# Test SSL configuration
echo | openssl s_client -servername project-1-13.eduhk.hk -connect project-1-13.eduhk.hk:443
```

**Solutions**:
```bash
# Renew certificate
sudo certbot renew

# Reload NGINX
sudo systemctl reload nginx
```

### Issue 4: MCP Inspector Connection Failed

**Symptoms**: Inspector shows "Connection Error"

**Diagnosis**:
```bash
# Test SSE endpoint manually
curl -N -H "Accept: text/event-stream" https://project-1-13.eduhk.hk/mcp/sse

# Check browser console for errors
# Check CORS headers
```

**Solutions**:
1. Verify server URL: `https://project-1-13.eduhk.hk/mcp/sse`
2. Use **Direct** connection type (not Proxy)
3. Clear browser cache
4. Check firewall settings

### Issue 5: DNS Not Resolving

**Symptoms**: Domain doesn't resolve to your server

**Diagnosis**:
```bash
# Check DNS resolution
nslookup project-1-13.eduhk.hk
dig project-1-13.eduhk.hk

# Check if domain points to your server IP
curl -I http://project-1-13.eduhk.hk
```

**Solutions**:
1. Contact domain administrator to update DNS records
2. Ensure A record points to your server's public IP
3. Wait for DNS propagation (up to 24 hours)

---

## Maintenance

### Daily Monitoring

```bash
# Check container status
docker ps | grep mtr-mcp-server

# Check NGINX status
sudo systemctl status nginx

# Check disk space
df -h

# Check memory usage
free -h

# Check recent logs
docker logs --tail 50 mtr-mcp-server
sudo tail -20 /var/log/nginx/access.log
```

### Weekly Tasks

```bash
# Update system packages
sudo apt update && sudo apt upgrade -y

# Restart services (if needed)
sudo systemctl restart nginx
docker-compose restart

# Check SSL certificate expiry
sudo certbot certificates

# Clean up Docker images
docker image prune -f
```

### Monthly Tasks

```bash
# Update Docker images
docker-compose pull
docker-compose up -d

# Backup configuration
sudo tar -czf /backup/nginx-config-$(date +%Y%m%d).tar.gz /etc/nginx/sites-available/
sudo tar -czf /backup/mtr-mcp-$(date +%Y%m%d).tar.gz /opt/mtr-mcp-example-fastapi/

# Check log file sizes
sudo du -sh /var/log/nginx/
docker exec mtr-mcp-server du -sh /app/logs/
```

### Updating the Application

```bash
# Navigate to project directory
cd /opt/mtr-mcp-example-fastapi

# Pull latest changes
git pull origin main

# Rebuild and restart container
docker-compose down
docker-compose build --no-cache
docker-compose up -d

# Verify deployment
curl https://project-1-13.eduhk.hk/mcp/health
```

---

## Summary

### What You've Accomplished

**✅ 5-Minute Setup Complete:**
- **Cloned** repository with `git clone`
- **Started** MCP server with `docker-compose up -d`  
- **Tested** endpoint: `http://localhost:8080/mcp/sse`
- **Verified** with MCP Inspector

**🚀 Your Working MCP Server Features:**
- **Complete MCP Protocol**: Tools, Resources, Prompts
- **Real MTR Data**: Live Hong Kong train schedules
- **FastAPI Integration**: Production-ready server
- **Docker Deployment**: Easy management and updates

### For Production (Optional)

The remaining steps (NGINX, SSL, domain setup) are **only needed** for production deployment with custom domains. Your MCP server is **fully functional** at `http://localhost:8080/mcp/sse`.

---

**🎉 Success!** Your MTR MCP Server is ready to use.

**Repository**: [github.com/enoch-sit/mtr-mcp-example-fastapi](https://github.com/enoch-sit/mtr-mcp-example-fastapi)  
**Documentation**: `README.md`, `MCP_GUIDE.md`, `MTR_API.md`

**Document Version**: 2.0 (Simplified Docker Approach)