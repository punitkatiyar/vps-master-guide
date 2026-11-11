# Hostinger VPS Deployment Guide
## Hosting React + Node.js on Debian with a Custom Domain

**VPS Provider:** Hostinger  
**Operating System:** Debian  
**Frontend:** React.js  
**Backend:** Node.js + Express  
**Web Server:** Nginx  
**Process Manager:** PM2  
**SSL:** Let's Encrypt  
**Domain:** Already purchased

---

# 1. Deployment Architecture

```text
                 Internet
                    |
                 Domain
              example.com
                    |
                 DNS A Record
                    |
                Hostinger VPS
                  Debian
                    |
                  Nginx
                 /     \
                /       \
       React Frontend   Node.js API
       example.com      api.example.com
       Port 80/443      Port 5000
                            |
                         Database
                      MongoDB/MySQL
```

### Technologies

| Technology | Purpose |
|---|---|
| Debian | VPS operating system |
| Node.js | Backend runtime |
| React | Frontend application |
| Nginx | Web server and reverse proxy |
| PM2 | Node.js process manager |
| Git | Source code management |
| Certbot | SSL certificate management |
| UFW | Firewall |
| MongoDB | Optional database |

---

# 2. Connect to Hostinger VPS

Open Hostinger hPanel.

1. Select VPS.
2. Open your VPS dashboard.
3. Find your VPS IP address.
4. Connect through SSH.

Windows PowerShell:

```bash
ssh root@YOUR_VPS_IP
```

Check Debian:

```bash
cat /etc/os-release
```

Check system resources:

```bash
hostnamectl
free -h
df -h
```

---

# 3. Update Debian

```bash
apt update
apt upgrade -y
```

Install essential packages:

```bash
apt install -y curl wget git vim nano unzip htop nginx ufw ca-certificates gnupg
```

Check Nginx:

```bash
nginx -v
```

Start Nginx:

```bash
systemctl enable nginx
systemctl start nginx
systemctl status nginx
```

Test in browser:

```text
http://YOUR_VPS_IP
```

---

# 4. Configure Firewall

Allow SSH, HTTP and HTTPS:

```bash
ufw allow OpenSSH
ufw allow 80/tcp
ufw allow 443/tcp
```

Enable firewall:

```bash
ufw enable
```

Check:

```bash
ufw status
```

**Important:** Also configure the Hostinger firewall, if enabled. Do not expose Node.js port 5000 publicly.

---

# 5. Install Node.js and npm

Install Node.js 22 using NodeSource:

```bash
curl -fsSL https://deb.nodesource.com/setup_22.x -o nodesource_setup.sh

bash nodesource_setup.sh

apt install -y nodejs
```

Verify:

```bash
node -v
npm -v
```

---

# 6. Configure Domain DNS

Open your domain provider's DNS management panel.

Create these records:

| Type | Name | Value |
|---|---|---|
| A | @ | YOUR_VPS_IP |
| A | www | YOUR_VPS_IP |
| A | api | YOUR_VPS_IP |

Example:

```text
A    @      123.45.67.89
A    www    123.45.67.89
A    api    123.45.67.89
```

Check DNS:

```bash
nslookup example.com
nslookup api.example.com
```

DNS propagation can take time.

Remove conflicting records pointing to an old server. Keep email-related MX and TXT records.

---

# 7. Create Project Directory

Create the application directory:

```bash
mkdir -p /var/www/myapp
```

Create a deployment user:

```bash
adduser deploy
```

Assign ownership:

```bash
chown -R deploy:deploy /var/www/myapp
```

Recommended project structure:

```text
/var/www/myapp/
│
├── frontend/
│   ├── src/
│   ├── public/
│   ├── package.json
│   └── dist/
│
└── backend/
    ├── server.js
    ├── package.json
    ├── .env
    └── node_modules/
```

---

# 8. Upload Project to VPS

## Option A: GitHub

Switch to deployment user:

```bash
su - deploy
```

Clone repository:

```bash
cd /var/www/myapp

git clone YOUR_GITHUB_REPOSITORY_URL .
```

For private repositories, configure secure SSH authentication.

## Option B: SFTP

Use FileZilla or WinSCP.

| Setting | Value |
|---|---|
| Protocol | SFTP |
| Host | VPS IP |
| Port | 22 |
| Username | deploy |
| Password | Deployment user password |

Upload your frontend and backend project files into:

```text
/var/www/myapp/
```

---

# 9. Deploy Node.js Backend

Navigate to backend:

```bash
cd /var/www/myapp/backend
```

Install dependencies:

```bash
npm install
```

For projects with package-lock.json:

```bash
npm ci
```

Create environment file:

```bash
nano .env
```

Example:

```env
PORT=5000
NODE_ENV=production
DATABASE_URL=your_database_connection_string
JWT_SECRET=your_long_random_secret
```

Never commit `.env` to a public Git repository.

## Create Express Server

Create:

```bash
nano server.js
```

Example:

```javascript
require("dotenv").config();

const express = require("express");
const cors = require("cors");

const app = express();

app.use(express.json());

app.use(cors({
    origin: [
        "https://example.com",
        "https://www.example.com"
    ]
}));

app.get("/", (req, res) => {
    res.json({
        message: "Node.js API is running successfully!"
    });
});

app.get("/api/users", (req, res) => {
    res.json([
        { id: 1, name: "John" },
        { id: 2, name: "David" }
    ]);
});

const PORT = process.env.PORT || 5000;

app.listen(PORT, "127.0.0.1", () => {
    console.log(`Server running on port ${PORT}`);
});
```

Install packages:

```bash
npm install express cors dotenv
```

Test:

```bash
node server.js
```

In another SSH session:

```bash
curl http://127.0.0.1:5000/
```

Expected response:

```json
{
  "message": "Node.js API is running successfully!"
}
```

Stop the test server:

```text
Ctrl + C
```

---

# 10. Install and Configure PM2

Install PM2:

```bash
npm install -g pm2
```

Start backend:

```bash
cd /var/www/myapp/backend

pm2 start server.js --name myapp-api
```

Check processes:

```bash
pm2 list
```

View logs:

```bash
pm2 logs myapp-api
```

Restart:

```bash
pm2 restart myapp-api
```

Stop:

```bash
pm2 stop myapp-api
```

Enable startup:

```bash
pm2 startup
```

Run the additional command printed by PM2.

Save process configuration:

```bash
pm2 save
```

---

# 11. Build React Frontend

Navigate to frontend:

```bash
cd /var/www/myapp/frontend
```

Install dependencies:

```bash
npm install
```

Create production environment configuration for Vite:

```env
VITE_API_URL=https://api.example.com
```

Example API request:

```javascript
const API_URL = import.meta.env.VITE_API_URL;

fetch(`${API_URL}/api/users`)
    .then(response => response.json())
    .then(data => console.log(data));
```

Build:

```bash
npm run build
```

Vite output:

```text
frontend/dist/
```

Create React App output:

```text
frontend/build/
```

---

# 12. Configure Nginx for React

Create configuration:

```bash
nano /etc/nginx/sites-available/myapp
```

Configuration for Vite:

```nginx
server {
    listen 80;
    server_name example.com www.example.com;

    root /var/www/myapp/frontend/dist;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }
}
```

For Create React App, replace `frontend/dist` with `frontend/build`.

Enable configuration:

```bash
ln -s /etc/nginx/sites-available/myapp \
/etc/nginx/sites-enabled/myapp
```

Remove default configuration if unused:

```bash
rm -f /etc/nginx/sites-enabled/default
```

Test:

```bash
nginx -t
```

Reload:

```bash
systemctl reload nginx
```

Test:

```text
http://example.com
```

---

# 13. Configure Nginx for Node.js API

Create:

```bash
nano /etc/nginx/sites-available/api
```

Configuration:

```nginx
server {
    listen 80;
    server_name api.example.com;

    location / {
        proxy_pass http://127.0.0.1:5000;

        proxy_http_version 1.1;

        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

Enable:

```bash
ln -s /etc/nginx/sites-available/api \
/etc/nginx/sites-enabled/api
```

Test:

```bash
nginx -t
```

Reload:

```bash
systemctl reload nginx
```

Test API:

```bash
curl http://api.example.com/
```

---

# 14. Install SSL Certificate

Install Certbot:

```bash
apt update

apt install certbot python3-certbot-nginx -y
```

Generate certificate:

```bash
certbot --nginx \
-d example.com \
-d www.example.com \
-d api.example.com
```

Follow the instructions and enter your email address.

Test automatic renewal:

```bash
certbot renew --dry-run
```

Test:

```text
https://example.com
https://www.example.com
https://api.example.com
```

---

# 15. Secure Production VPS

## A. Use a deployment user

Avoid running applications as root.

## B. Protect environment variables

```bash
chmod 600 /var/www/my
