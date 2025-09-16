# domoticz Installation Guide

domoticz is a free and open-source home automation system. Domoticz provides lightweight home automation system

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
  - CPU: 1 core minimum
  - RAM: 512MB minimum
  - Storage: 2GB for data
  - Network: IoT protocols
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 8080 (default domoticz port)
  - HTTPS on 443
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

# Install domoticz
sudo dnf install -y domoticz

# Enable and start service
sudo systemctl enable --now domoticz

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Verify installation
domoticz --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install domoticz
sudo apt install -y domoticz

# Enable and start service
sudo systemctl enable --now domoticz

# Configure firewall
sudo ufw allow 8080

# Verify installation
domoticz --version
```

### Arch Linux

```bash
# Install domoticz
sudo pacman -S domoticz

# Enable and start service
sudo systemctl enable --now domoticz

# Verify installation
domoticz --version
```

### Alpine Linux

```bash
# Install domoticz
apk add --no-cache domoticz

# Enable and start service
rc-update add domoticz default
rc-service domoticz start

# Verify installation
domoticz --version
```

### openSUSE/SLES

```bash
# Install domoticz
sudo zypper install -y domoticz

# Enable and start service
sudo systemctl enable --now domoticz

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload

# Verify installation
domoticz --version
```

### macOS

```bash
# Using Homebrew
brew install domoticz

# Start service
brew services start domoticz

# Verify installation
domoticz --version
```

### FreeBSD

```bash
# Using pkg
pkg install domoticz

# Enable in rc.conf
echo 'domoticz_enable="YES"' >> /etc/rc.conf

# Start service
service domoticz start

# Verify installation
domoticz --version
```

### Windows

```bash
# Using Chocolatey
choco install domoticz

# Or using Scoop
scoop install domoticz

# Verify installation
domoticz --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/domoticz

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
domoticz --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable domoticz

# Start service
sudo systemctl start domoticz

# Stop service
sudo systemctl stop domoticz

# Restart service
sudo systemctl restart domoticz

# Check status
sudo systemctl status domoticz

# View logs
sudo journalctl -u domoticz -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add domoticz default

# Start service
rc-service domoticz start

# Stop service
rc-service domoticz stop

# Restart service
rc-service domoticz restart

# Check status
rc-service domoticz status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'domoticz_enable="YES"' >> /etc/rc.conf

# Start service
service domoticz start

# Stop service
service domoticz stop

# Restart service
service domoticz restart

# Check status
service domoticz status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start domoticz
brew services stop domoticz
brew services restart domoticz

# Check status
brew services list | grep domoticz
```

### Windows Service Manager

```powershell
# Start service
net start domoticz

# Stop service
net stop domoticz

# Using PowerShell
Start-Service domoticz
Stop-Service domoticz
Restart-Service domoticz

# Check status
Get-Service domoticz
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream domoticz_backend {
    server 127.0.0.1:8080;
}

server {
    listen 80;
    server_name domoticz.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name domoticz.example.com;

    ssl_certificate /etc/ssl/certs/domoticz.example.com.crt;
    ssl_certificate_key /etc/ssl/private/domoticz.example.com.key;

    location / {
        proxy_pass http://domoticz_backend;
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
    ServerName domoticz.example.com
    Redirect permanent / https://domoticz.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName domoticz.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/domoticz.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/domoticz.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:8080/
    ProxyPassReverse / http://127.0.0.1:8080/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend domoticz_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/domoticz.pem
    redirect scheme https if !{ ssl_fc }
    default_backend domoticz_backend

backend domoticz_backend
    balance roundrobin
    server domoticz1 127.0.0.1:8080 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R domoticz:domoticz /etc/domoticz
sudo chmod 750 /etc/domoticz

# Configure firewall
sudo firewall-cmd --permanent --add-port=8080/tcp
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
sudo systemctl status domoticz

# View logs
sudo journalctl -u domoticz -f

# Monitor resource usage
top -p $(pgrep domoticz)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/domoticz"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/domoticz-backup-$DATE.tar.gz" /etc/domoticz /var/lib/domoticz

echo "Backup completed: $BACKUP_DIR/domoticz-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop domoticz

# Restore from backup
tar -xzf /backup/domoticz/domoticz-backup-*.tar.gz -C /

# Start service
sudo systemctl start domoticz
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u domoticz -n 100
sudo tail -f /var/log/domoticz/domoticz.log

# Check configuration
domoticz --version

# Check permissions
ls -la /etc/domoticz
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 8080

# Test connectivity
telnet localhost 8080

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep domoticz)

# Check disk I/O
iotop -p $(pgrep domoticz)

# Check connections
ss -an | grep 8080
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  domoticz:
    image: domoticz:latest
    ports:
      - "8080:8080"
    volumes:
      - ./config:/etc/domoticz
      - ./data:/var/lib/domoticz
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update domoticz

# Debian/Ubuntu
sudo apt update && sudo apt upgrade domoticz

# Arch Linux
sudo pacman -Syu domoticz

# Alpine Linux
apk update && apk upgrade domoticz

# openSUSE
sudo zypper update domoticz

# FreeBSD
pkg update && pkg upgrade domoticz

# Always backup before updates
tar -czf /backup/domoticz-pre-update-$(date +%Y%m%d).tar.gz /etc/domoticz

# Restart after updates
sudo systemctl restart domoticz
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/domoticz

# Clean old logs
find /var/log/domoticz -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/domoticz
```

## Additional Resources

- Official Documentation: https://docs.domoticz.org/
- GitHub Repository: https://github.com/domoticz/domoticz
- Community Forum: https://forum.domoticz.org/
- Best Practices Guide: https://docs.domoticz.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
