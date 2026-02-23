---
layout: article
title: Setting Up SSL with Let's Encrypt and Certbot on Ubuntu + Nginx
description: How to obtain and configure free SSL/TLS certificates using Certbot and Let's Encrypt on Ubuntu with Nginx
category: Server Setup
date: 2026-02-22
---

## Overview

Let's Encrypt is a free, automated, and open Certificate Authority that provides SSL/TLS certificates. Certbot is the official client that automates the process of obtaining and renewing certificates, including configuring Nginx automatically.

**Prerequisites:**
- Ubuntu 20.04+ server
- Nginx installed and running
- A domain name pointing to your server's IP
- Port 80 and 443 open in your firewall

## Install Certbot

Certbot is available via the official snap package, which is the recommended installation method.

```bash
# Remove any existing certbot package installed via apt
sudo apt remove certbot

# Install certbot via snap
sudo snap install --classic certbot

# Create symlink so certbot is available system-wide
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

Verify the installation:

```bash
certbot --version
```

## Obtain a Certificate

### Option 1: Automatic Nginx Configuration (Recommended)

Certbot can obtain the certificate and configure Nginx automatically:

```bash
sudo certbot --nginx -d example.com -d www.example.com
```

During the process, Certbot will ask for:
- An email address (for renewal reminders and urgent notices)
- Agreement to the Terms of Service
- Whether to share your email with the EFF (optional)

Certbot will then:
1. Validate domain ownership via HTTP challenge
2. Obtain the certificate from Let's Encrypt
3. Modify your Nginx config to enable HTTPS
4. Set up automatic HTTP → HTTPS redirect

### Option 2: Standalone (Manual Nginx Configuration)

Use this if you prefer to configure Nginx yourself:

```bash
# Stop nginx temporarily so certbot can use port 80
sudo systemctl stop nginx

sudo certbot certonly --standalone -d example.com -d www.example.com

# Restart nginx after obtaining the certificate
sudo systemctl start nginx
```

### Option 3: Webroot (Nginx Stays Running)

Use this if you need Nginx to keep running during certificate issuance:

```bash
sudo certbot certonly --webroot -w /var/www/html -d example.com -d www.example.com
```

## Certificate Files

After a successful issuance, certificates are stored at:

| File | Description |
|------|-------------|
| `/etc/letsencrypt/live/example.com/fullchain.pem` | Full certificate chain (use this for `ssl_certificate`) |
| `/etc/letsencrypt/live/example.com/privkey.pem` | Private key (use this for `ssl_certificate_key`) |
| `/etc/letsencrypt/live/example.com/cert.pem` | Server certificate only |
| `/etc/letsencrypt/live/example.com/chain.pem` | Intermediate certificates only |

## Manual Nginx HTTPS Configuration

If you used `certonly`, configure Nginx manually. Edit your site config:

```bash
sudo nano /etc/nginx/sites-available/example.com
```

```nginx
server {
    listen 80;
    server_name example.com www.example.com;

    # Redirect all HTTP to HTTPS
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name example.com www.example.com;

    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

    # Recommended SSL settings
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_prefer_server_ciphers off;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 1d;

    root /var/www/example.com;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
```

Test and reload Nginx:

```bash
sudo nginx -t
sudo systemctl reload nginx
```

## Certificate Renewal

Let's Encrypt certificates are valid for **90 days**. Certbot installs a systemd timer (or cron job) that runs twice daily to renew certificates automatically when they are within 30 days of expiry.

### Check Renewal Timer Status

```bash
# Check systemd timer
sudo systemctl status snap.certbot.renew.timer

# Or check cron
sudo cat /etc/cron.d/certbot
```

### Test Renewal (Dry Run)

Always test the renewal process without actually renewing:

```bash
sudo certbot renew --dry-run
```

If the dry run succeeds, automatic renewal is configured correctly.

### Force Renewal Manually

```bash
sudo certbot renew --force-renewal
```

After renewal, reload Nginx to apply the new certificate:

```bash
sudo systemctl reload nginx
```

## Firewall Configuration

Make sure ports 80 and 443 are open:

```bash
sudo ufw allow 'Nginx Full'
sudo ufw status
```

`Nginx Full` allows both HTTP (80) and HTTPS (443). You can remove the HTTP-only rule if it exists:

```bash
sudo ufw delete allow 'Nginx HTTP'
```

## Verify Your SSL Certificate

### Using curl

```bash
curl -I https://example.com
```

### Using OpenSSL

```bash
openssl s_client -connect example.com:443 -servername example.com
```

### Online Tools

- [SSL Labs Server Test](https://www.ssllabs.com/ssltest/) — comprehensive SSL analysis
- [Why No Padlock](https://www.whynopadlock.com/) — checks for mixed content issues

## Common Commands Reference

```bash
# List all certificates managed by certbot
sudo certbot certificates

# Obtain a new certificate (interactive)
sudo certbot --nginx

# Obtain a certificate for a specific domain
sudo certbot --nginx -d example.com -d www.example.com

# Renew all certificates
sudo certbot renew

# Renew with a dry run
sudo certbot renew --dry-run

# Revoke a certificate
sudo certbot revoke --cert-path /etc/letsencrypt/live/example.com/cert.pem

# Delete a certificate from certbot management
sudo certbot delete --cert-name example.com
```

## Troubleshooting

### Domain Validation Fails

- Ensure DNS A record points to your server's public IP
- Confirm port 80 is open and Nginx is serving the domain
- Check that no firewall or CDN is blocking Let's Encrypt's validation servers

```bash
# Check if port 80 is accessible
sudo ufw status
curl http://example.com
```

### Certificate Not Renewing

```bash
# Check certbot logs
sudo cat /var/log/letsencrypt/letsencrypt.log

# Run renewal manually with verbose output
sudo certbot renew --dry-run --verbose
```

### Nginx Fails After Renewal

If Nginx is not reloaded after renewal, the old certificate stays in memory. Add a deploy hook:

```bash
# Create a post-renewal hook to reload nginx automatically
sudo nano /etc/letsencrypt/renewal-hooks/deploy/reload-nginx.sh
```

```bash
#!/bin/bash
systemctl reload nginx
```

```bash
sudo chmod +x /etc/letsencrypt/renewal-hooks/deploy/reload-nginx.sh
```

### "Too Many Certificates" Error

Let's Encrypt has rate limits: 5 duplicate certificates per week per domain. Use `--dry-run` to test without consuming the limit.

## Resources

- [Let's Encrypt Documentation](https://letsencrypt.org/docs/)
- [Certbot Documentation](https://certbot.eff.org/docs/)
- [Certbot Instructions by Server & OS](https://certbot.eff.org/instructions)
- [SSL Labs Server Test](https://www.ssllabs.com/ssltest/)
