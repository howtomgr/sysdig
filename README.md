# sysdig Installation Guide

sysdig is a free and open-source system exploration. Sysdig provides system exploration and troubleshooting tool

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 2+ cores
  - RAM: 2GB minimum
  - Storage: 10GB for captures
  - Network: System calls
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port N/A (default sysdig port)
  - None
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install sysdig
sudo dnf install -y sysdig

# Enable and start service
sudo systemctl enable --now sysdig

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
sysdig --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install sysdig
sudo apt install -y sysdig

# Enable and start service
sudo systemctl enable --now sysdig

# Configure firewall
sudo ufw allow N/A

# Verify installation
sysdig --version
```

### Arch Linux

```bash
# Install sysdig
sudo pacman -S sysdig

# Enable and start service
sudo systemctl enable --now sysdig

# Verify installation
sysdig --version
```

### Alpine Linux

```bash
# Install sysdig
apk add --no-cache sysdig

# Enable and start service
rc-update add sysdig default
rc-service sysdig start

# Verify installation
sysdig --version
```

### openSUSE/SLES

```bash
# Install sysdig
sudo zypper install -y sysdig

# Enable and start service
sudo systemctl enable --now sysdig

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
sysdig --version
```

### macOS

```bash
# Using Homebrew
brew install sysdig

# Start service
brew services start sysdig

# Verify installation
sysdig --version
```

### FreeBSD

```bash
# Using pkg
pkg install sysdig

# Enable in rc.conf
echo 'sysdig_enable="YES"' >> /etc/rc.conf

# Start service
service sysdig start

# Verify installation
sysdig --version
```

### Windows

```bash
# Using Chocolatey
choco install sysdig

# Or using Scoop
scoop install sysdig

# Verify installation
sysdig --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/sysdig

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
sysdig --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable sysdig

# Start service
sudo systemctl start sysdig

# Stop service
sudo systemctl stop sysdig

# Restart service
sudo systemctl restart sysdig

# Check status
sudo systemctl status sysdig

# View logs
sudo journalctl -u sysdig -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add sysdig default

# Start service
rc-service sysdig start

# Stop service
rc-service sysdig stop

# Restart service
rc-service sysdig restart

# Check status
rc-service sysdig status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'sysdig_enable="YES"' >> /etc/rc.conf

# Start service
service sysdig start

# Stop service
service sysdig stop

# Restart service
service sysdig restart

# Check status
service sysdig status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start sysdig
brew services stop sysdig
brew services restart sysdig

# Check status
brew services list | grep sysdig
```

### Windows Service Manager

```powershell
# Start service
net start sysdig

# Stop service
net stop sysdig

# Using PowerShell
Start-Service sysdig
Stop-Service sysdig
Restart-Service sysdig

# Check status
Get-Service sysdig
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream sysdig_backend {
    server 127.0.0.1:N/A;
}

server {
    listen 80;
    server_name sysdig.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name sysdig.example.com;

    ssl_certificate /etc/ssl/certs/sysdig.example.com.crt;
    ssl_certificate_key /etc/ssl/private/sysdig.example.com.key;

    location / {
        proxy_pass http://sysdig_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName sysdig.example.com
    Redirect permanent / https://sysdig.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName sysdig.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/sysdig.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/sysdig.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:N/A/
    ProxyPassReverse / http://127.0.0.1:N/A/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend sysdig_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/sysdig.pem
    redirect scheme https if !{ ssl_fc }
    default_backend sysdig_backend

backend sysdig_backend
    balance roundrobin
    server sysdig1 127.0.0.1:N/A check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R sysdig:sysdig /etc/sysdig
sudo chmod 750 /etc/sysdig

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status sysdig

# View logs
sudo journalctl -u sysdig -f

# Monitor resource usage
top -p $(pgrep sysdig)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/sysdig"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/sysdig-backup-$DATE.tar.gz" /etc/sysdig /var/lib/sysdig

echo "Backup completed: $BACKUP_DIR/sysdig-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop sysdig

# Restore from backup
tar -xzf /backup/sysdig/sysdig-backup-*.tar.gz -C /

# Start service
sudo systemctl start sysdig
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u sysdig -n 100
sudo tail -f /var/log/sysdig/sysdig.log

# Check configuration
sysdig --version

# Check permissions
ls -la /etc/sysdig
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep N/A

# Test connectivity
telnet localhost N/A

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep sysdig)

# Check disk I/O
iotop -p $(pgrep sysdig)

# Check connections
ss -an | grep N/A
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  sysdig:
    image: sysdig:latest
    ports:
      - "N/A:N/A"
    volumes:
      - ./config:/etc/sysdig
      - ./data:/var/lib/sysdig
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update sysdig

# Debian/Ubuntu
sudo apt update && sudo apt upgrade sysdig

# Arch Linux
sudo pacman -Syu sysdig

# Alpine Linux
apk update && apk upgrade sysdig

# openSUSE
sudo zypper update sysdig

# FreeBSD
pkg update && pkg upgrade sysdig

# Always backup before updates
tar -czf /backup/sysdig-pre-update-$(date +%Y%m%d).tar.gz /etc/sysdig

# Restart after updates
sudo systemctl restart sysdig
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/sysdig

# Clean old logs
find /var/log/sysdig -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/sysdig
```

## Additional Resources

- Official Documentation: https://docs.sysdig.org/
- GitHub Repository: https://github.com/sysdig/sysdig
- Community Forum: https://forum.sysdig.org/
- Best Practices Guide: https://docs.sysdig.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
