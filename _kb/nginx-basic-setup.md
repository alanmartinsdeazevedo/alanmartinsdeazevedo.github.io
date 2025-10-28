---
layout: article
title: Nginx Basic Configuration
description: Getting started with nginx web server on Ubuntu
category: Server Setup
date: 2025-10-28
---

## Overview

nginx is a high-performance web server and reverse proxy. This guide covers basic installation and configuration on Ubuntu.

## Installation

### Install nginx

```bash
sudo apt update
sudo apt install nginx
```

### Start and Enable nginx

```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```

### Verify Installation

Check nginx status:

```bash
sudo systemctl status nginx
```

Visit `http://your_server_ip` in a browser. You should see the nginx welcome page.

## Basic Configuration

### Configuration Files

- Main config: `/etc/nginx/nginx.conf`
- Site configs: `/etc/nginx/sites-available/`
- Enabled sites: `/etc/nginx/sites-enabled/`
- Default site: `/etc/nginx/sites-available/default`

### Creating a Basic Site

Create a new site configuration:

```bash
sudo nano /etc/nginx/sites-available/mysite
```

Add basic configuration:

```nginx
server {
    listen 80;
    server_name example.com www.example.com;

    root /var/www/mysite;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

### Enable the Site

```bash
sudo ln -s /etc/nginx/sites-available/mysite /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

## Reverse Proxy Configuration

To proxy requests to a Node.js application:

```nginx
server {
    listen 80;
    server_name example.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

## Common Commands

```bash
# Test configuration
sudo nginx -t

# Reload configuration
sudo systemctl reload nginx

# Restart nginx
sudo systemctl restart nginx

# Stop nginx
sudo systemctl stop nginx

# View error logs
sudo tail -f /var/log/nginx/error.log

# View access logs
sudo tail -f /var/log/nginx/access.log
```

## Firewall Configuration

Allow HTTP and HTTPS traffic:

```bash
sudo ufw allow 'Nginx Full'
sudo ufw status
```

## Security Best Practices

### Hide nginx Version

Edit `/etc/nginx/nginx.conf`:

```nginx
http {
    server_tokens off;
    # ... other settings
}
```

### Rate Limiting

Add to your server block:

```nginx
limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;

server {
    location / {
        limit_req zone=one burst=5;
        # ... other settings
    }
}
```

## Performance Tuning

### Enable Gzip Compression

In `/etc/nginx/nginx.conf`:

```nginx
gzip on;
gzip_vary on;
gzip_min_length 1024;
gzip_types text/plain text/css text/xml text/javascript
           application/x-javascript application/xml+rss
           application/javascript application/json;
```

### Browser Caching

Add to your server block:

```nginx
location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
    expires 1y;
    add_header Cache-Control "public, immutable";
}
```

## Troubleshooting

### Check Configuration Syntax

```bash
sudo nginx -t
```

### Common Issues

**403 Forbidden**: Check file permissions and SELinux settings

**502 Bad Gateway**: Backend service is down or unreachable

**Connection Refused**: nginx is not running or firewall is blocking

## Resources

- [Official nginx Documentation](https://nginx.org/en/docs/)
- [nginx Beginner's Guide](https://nginx.org/en/docs/beginners_guide.html)
- [DigitalOcean nginx Tutorials](https://www.digitalocean.com/community/tags/nginx)
