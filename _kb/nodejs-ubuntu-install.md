---
layout: article
title: Installing Node.js on Ubuntu Server
description: Complete guide to installing Node.js on Ubuntu using NodeSource distributions
category: Server Setup
date: 2025-10-28
---

## Overview

This guide walks you through installing Node.js on Ubuntu Server using the official NodeSource distributions. This method ensures you get the latest stable versions and easy updates.

## Prerequisites

- Ubuntu Server (20.04 or later recommended)
- sudo privileges
- Active internet connection

## Installation Steps

### 1. Install curl

First, ensure curl is installed on your system:

```bash
sudo apt update
sudo apt install -y curl
```

### 2. Download the NodeSource Setup Script

Download the setup script for your desired Node.js version. For Node.js 23.x (latest):

```bash
curl -fsSL https://deb.nodesource.com/setup_23.x -o nodesource_setup.sh
```

**Available versions:**
- Node.js 23.x: `setup_23.x`
- Node.js 22.x: `setup_22.x`
- Node.js 20.x: `setup_20.x` (LTS)
- Node.js 18.x: `setup_18.x` (LTS)
- Current: `setup_current.x`
- LTS: `setup_lts.x`

### 3. Run the Setup Script

Execute the setup script with sudo privileges:

```bash
sudo -E bash nodesource_setup.sh
```

The `-E` flag preserves your environment variables during execution.

### 4. Install Node.js

Now install Node.js using apt:

```bash
sudo apt install -y nodejs
```

This command installs both Node.js and npm (Node Package Manager).

### 5. Verify Installation

Check that Node.js and npm are installed correctly:

```bash
node -v
npm -v
```

You should see version numbers for both commands.

## Example Output

```bash
$ node -v
v23.0.0

$ npm -v
10.2.0
```

## Installing Build Tools (Optional)

Some npm packages require compilation. Install build tools if needed:

```bash
sudo apt install -y build-essential
```

## Upgrading Node.js

To upgrade to a different version:

1. Download the setup script for the new version
2. Run the setup script
3. Update Node.js: `sudo apt update && sudo apt install nodejs`

## Common Issues

### Permission Errors

If you encounter permission errors with npm global packages:

```bash
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

### Multiple Node Versions

If you need to manage multiple Node.js versions, consider using [nvm (Node Version Manager)](https://github.com/nvm-sh/nvm).

## Alternative: Using nvm

For development environments where you need multiple Node versions:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
source ~/.bashrc
nvm install node
nvm install --lts
```

## Resources

- [NodeSource Distributions GitHub](https://github.com/nodesource/distributions)
- [Official Node.js Documentation](https://nodejs.org/docs/)
- [npm Documentation](https://docs.npmjs.com/)

## Next Steps

- Install a process manager like [PM2](https://pm2.keymetrics.io/)
- Set up a reverse proxy with nginx
- Configure firewall rules for your Node.js applications
