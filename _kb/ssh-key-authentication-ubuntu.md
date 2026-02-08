---
layout: article
title: SSH Key Authentication on Ubuntu Server
description: How to set up SSH access to an Ubuntu server using RSA key authentication
category: Server Setup
date: 2026-02-08
---

## Overview

SSH key authentication provides a more secure and convenient way to log into your Ubuntu server compared to password-based login. This guide covers generating an RSA key pair, configuring the server, and disabling password authentication.

## Step 1: Generate the RSA Key Pair

On your **local machine** (the client), generate a new RSA key pair:

```bash
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```

You will be prompted for:

- **File location**: Press Enter to accept the default (`~/.ssh/id_rsa`)
- **Passphrase**: Enter a secure passphrase (recommended) or press Enter for none

This creates two files:

- `~/.ssh/id_rsa` — your **private key** (never share this)
- `~/.ssh/id_rsa.pub` — your **public key** (this goes on the server)

## Step 2: Copy the Public Key to the Server

### Method 1: Using ssh-copy-id (Recommended)

```bash
ssh-copy-id username@server_ip
```

You will be prompted for your password one last time. The command automatically appends your public key to `~/.ssh/authorized_keys` on the server.

### Method 2: Manual Copy

If `ssh-copy-id` is not available, copy the key manually:

```bash
# Display your public key
cat ~/.ssh/id_rsa.pub
```

Then on the **server**:

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
echo "paste_your_public_key_here" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

### Method 3: Using SCP

```bash
scp ~/.ssh/id_rsa.pub username@server_ip:/tmp/id_rsa.pub
```

Then on the server:

```bash
mkdir -p ~/.ssh
cat /tmp/id_rsa.pub >> ~/.ssh/authorized_keys
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
rm /tmp/id_rsa.pub
```

## Step 3: Test the Connection

Connect to the server using your key:

```bash
ssh username@server_ip
```

If you set a passphrase, you will be prompted to enter it. You should now be logged in without needing the server password.

## Step 4: Disable Password Authentication (Recommended)

Once key authentication is working, disable password login for improved security.

Edit the SSH daemon configuration on the **server**:

```bash
sudo nano /etc/ssh/sshd_config
```

Find and update these settings:

```
PubkeyAuthentication yes
PasswordAuthentication no
ChallengeResponseAuthentication no
UsePAM no
```

Restart the SSH service:

```bash
sudo systemctl restart sshd
```

> **Warning**: Before disabling password authentication, make sure your key-based login is working. Otherwise, you may lock yourself out of the server.

## Step 5: Configure SSH Client (Optional)

Create or edit `~/.ssh/config` on your local machine to simplify connections:

```
Host myserver
    HostName server_ip_or_domain
    User username
    IdentityFile ~/.ssh/id_rsa
    Port 22
```

Now you can connect with just:

```bash
ssh myserver
```

## Using a Custom Key Name

If you have multiple keys for different servers, generate a key with a custom name:

```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/my_server_key -C "my server key"
```

Then specify the key when connecting:

```bash
ssh -i ~/.ssh/my_server_key username@server_ip
```

Or add it to your `~/.ssh/config` as shown above.

## Managing SSH Agent

To avoid entering your passphrase repeatedly, use the SSH agent:

```bash
# Start the SSH agent
eval "$(ssh-agent -s)"

# Add your key
ssh-add ~/.ssh/id_rsa
```

To automatically start the agent, add to your `~/.bashrc`:

```bash
if [ -z "$SSH_AUTH_SOCK" ]; then
    eval "$(ssh-agent -s)"
    ssh-add ~/.ssh/id_rsa
fi
```

## Firewall Configuration

Ensure SSH traffic is allowed through the firewall:

```bash
sudo ufw allow ssh
sudo ufw status
```

If using a non-standard port:

```bash
sudo ufw allow 2222/tcp
```

## Troubleshooting

### Permission Denied (publickey)

Check file permissions on the server:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
chmod 644 ~/.ssh/id_rsa.pub   # local machine
chmod 600 ~/.ssh/id_rsa       # local machine
```

Verify the home directory is not group-writable:

```bash
chmod 755 ~
```

### Connection Timeout

- Verify the server IP is correct
- Check that SSH is running: `sudo systemctl status sshd`
- Confirm firewall allows SSH: `sudo ufw status`

### Verbose Mode for Debugging

Use verbose mode to debug connection issues:

```bash
ssh -v username@server_ip
```

For even more detail:

```bash
ssh -vvv username@server_ip
```

### Key Not Accepted

Ensure the public key is correctly added to `authorized_keys`:

```bash
# On the server
cat ~/.ssh/authorized_keys
```

Check SSH server logs:

```bash
sudo tail -f /var/log/auth.log
```

## Security Best Practices

- **Use a strong passphrase** on your private key
- **Use 4096-bit keys** for stronger encryption
- **Disable root login**: Set `PermitRootLogin no` in `/etc/ssh/sshd_config`
- **Change the default SSH port** to reduce automated attacks
- **Use fail2ban** to block brute-force attempts
- **Keep your private key secure** and never share it
- **Regularly rotate keys** and remove unused keys from `authorized_keys`

## Resources

- [OpenSSH Documentation](https://www.openssh.com/manual.html)
- [Ubuntu SSH Server Guide](https://ubuntu.com/server/docs/openssh-server)
- [SSH Academy](https://www.ssh.com/academy/ssh)
