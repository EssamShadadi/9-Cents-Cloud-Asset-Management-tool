# NGO Asset Management Platform on Azure

## Overview

This repository documents the deployment, configuration, and operational setup of a cloud-hosted NGO Asset Management platform using Microsoft Azure, Docker, Ubuntu Linux, MariaDB, and Snipe-IT.

The project focuses on building a production-oriented infrastructure capable of supporting real-world NGO asset workflows including:

- Asset lifecycle management
- QR-based asset tracking
- Assignment history and audit logging
- Arabic localization support
- Multi-category asset management
- Backup automation
- Production deployment practices

---

# Project Goals

The main goals of this project were:

- Deploy an operational asset management environment in the cloud
- Gain practical experience with Azure infrastructure
- Learn Linux server administration
- Implement Docker-based application deployment
- Configure secure remote access and firewall rules
- Automate backup and recovery workflows
- Explore production-oriented DevOps concepts

---

# Architecture

## High-Level Infrastructure

```text
Users
   ↓
Azure Public IP
   ↓
Ubuntu Linux VM
   ↓
Docker Engine
   ├── Snipe-IT Container
   └── MariaDB Container
```

---

# Technology Stack

| Area | Technology |
|---|---|
| Cloud Platform | Microsoft Azure |
| Operating System | Ubuntu Linux |
| Containerization | Docker & Docker Compose |
| Asset Platform | Snipe-IT |
| Database | MariaDB |
| Security | SSH Keys, UFW Firewall |
| Backup | Automated MariaDB Dumps |
| Localization | Arabic / RTL Support |

---

# Features

## Asset Management

- Asset lifecycle tracking
- Asset categories
- Status labels
- Assignment history
- Asset metadata support
- Multi-location support

---

## QR & Labeling

- QR code generation
- Printable labels
- Asset identification workflows
- Quick asset verification

---

## Security

- SSH key authentication
- Firewall configuration using UFW
- Environment variable configuration
- Isolated Docker containers

---

## Backup Automation

- Automated MariaDB backups
- Scheduled backup jobs
- Persistent Docker volumes
- Recovery-ready database exports

---

# Repository Structure

```text
ngo-asset-management-azure/
│
├── README.md
├── LICENSE
├── .gitignore
│
├── architecture/
│   ├── infrastructure-diagram.png
│   ├── docker-architecture.png
│   └── asset-lifecycle-diagram.png
│
├── screenshots/
│   ├── azure-vm.png
│   ├── docker-containers.png
│   ├── snipeit-dashboard.png
│   ├── qr-labels.png
│   ├── arabic-localization.png
│   └── backup-automation.png
│
├── docker/
│   ├── docker-compose.yml
│   └── .env.example
│
├── scripts/
│   ├── backup_snipeit.sh
│   └── restore_backup.sh
│
├── docs/
│   ├── azure-vm-setup.md
│   ├── docker-installation.md
│   ├── snipeit-deployment.md
│   ├── backup-automation.md
│   ├── security-hardening.md
│   └── troubleshooting.md
│
└── assets/
    └── sample-qr-labels/
```

---

# Azure Infrastructure Setup

## 1. Create Azure Resource Group

Example:

```text
RG-NGO-ASSET-PROD
```

---

## 2. Create Ubuntu Virtual Machine

Recommended:

| Setting | Value |
|---|---|
| OS | Ubuntu Server 24.04 LTS |
| VM Size | B2s or B2ms |
| Authentication | SSH Key |
| Open Ports | 22, 80, 443 |

---

## 3. Configure Networking

Allow inbound rules for:

| Port | Purpose |
|---|---|
| 22 | SSH |
| 80 | HTTP |
| 443 | HTTPS |

---

# Docker Deployment

## Install Docker

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

---

## Install Docker Compose

```bash
sudo apt install docker-compose-plugin -y
```

---

## Start Containers

```bash
docker compose up -d
```

---

# Docker Compose Configuration

```yaml
services:

  snipeit:
    image: snipe/snipe-it:latest
    container_name: snipeit
    restart: unless-stopped

    ports:
      - "80:80"

    env_file:
      - .env

    depends_on:
      - mariadb

    volumes:
      - snipeit_data:/var/lib/snipeit

  mariadb:
    image: mariadb:11
    container_name: snipeit_db
    restart: unless-stopped

    environment:
      MYSQL_ROOT_PASSWORD: CHANGE_ME
      MYSQL_DATABASE: snipeit
      MYSQL_USER: snipeit
      MYSQL_PASSWORD: CHANGE_ME

    volumes:
      - db_data:/var/lib/mysql

volumes:
  snipeit_data:
  db_data:
```

---

# Environment Variables Example

```env
APP_ENV=production
APP_DEBUG=false
APP_URL=http://YOUR_PUBLIC_IP

MYSQL_PORT_3306_TCP_ADDR=mariadb
MYSQL_PORT_3306_TCP_PORT=3306
MYSQL_DATABASE=snipeit
MYSQL_USERNAME=snipeit
MYSQL_PASSWORD=CHANGE_ME

APP_LOCALE=ar-SA
APP_TIMEZONE=Asia/Damascus
```

---

# Backup Automation

## Backup Script

```bash
#!/bin/bash

BACKUP_DIR=/home/azureuser/backups
DATE=$(date +%F_%H-%M-%S)

mkdir -p $BACKUP_DIR

sudo docker exec snipeit_db mysqldump -u root -pYOUR_PASSWORD snipeit > $BACKUP_DIR/snipeit_$DATE.sql

find $BACKUP_DIR -type f -mtime +7 -delete
```

---

## Make Script Executable

```bash
chmod +x backup_snipeit.sh
```

---

## Schedule Daily Backups

```bash
crontab -e
```

Example:

```bash
0 2 * * * /home/azureuser/backup_snipeit.sh
```

---

# Security Hardening

## Enable Firewall

```bash
sudo ufw allow OpenSSH
sudo ufw allow 80
sudo ufw allow 443
sudo ufw enable
```

---

## System Updates

```bash
sudo apt update && sudo apt upgrade -y
```

---

# Arabic Localization

The deployment includes Arabic localization support using:

```env
APP_LOCALE=ar-SA
```

Supported:

- Arabic asset names
- Arabic categories
- Arabic labels
- Mixed Arabic/English operational data

---

# Lessons Learned

This project provided hands-on experience with:

- Azure infrastructure provisioning
- Linux administration
- Docker networking and persistence
- Cloud security basics
- Backup automation
- Production deployment workflows
- Operational system design

---

# Future Improvements

Potential future enhancements:

- HTTPS reverse proxy
- Custom domain configuration
- Azure AD SSO integration
- Monitoring and logging stack
- Power BI dashboards
- Automated deployment pipelines
- Disaster recovery improvements

---

# Screenshots To Include

Recommended screenshots:

- Azure VM overview
- SSH terminal connection
- Docker containers running
- docker-compose deployment
- Snipe-IT dashboard
- QR labels
- Arabic localization
- Backup automation
- Firewall configuration

---

# Disclaimer

This repository documents the deployment and customization of the open-source Snipe-IT platform.

Snipe-IT was not developed by me.

Official project:
https://snipeitapp.com

---

# Author

Essam Shadadi

Cloud / Azure / DevOps Learning Journey

