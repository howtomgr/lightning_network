# lightning-network Installation Guide

lightning-network is a free and open-source Bitcoin layer 2. LND provides Lightning Network daemon for Bitcoin

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
  - Storage: 50GB for channels
  - Network: P2P/gRPC
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 9735 (default lightning-network port)
  - RPC on 10009
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

# Install lightning-network
sudo dnf install -y lightning_network

# Enable and start service
sudo systemctl enable --now lightning-network

# Configure firewall
sudo firewall-cmd --permanent --add-port=9735/tcp
sudo firewall-cmd --reload

# Verify installation
lightning-network --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install lightning-network
sudo apt install -y lightning_network

# Enable and start service
sudo systemctl enable --now lightning-network

# Configure firewall
sudo ufw allow 9735

# Verify installation
lightning-network --version
```

### Arch Linux

```bash
# Install lightning-network
sudo pacman -S lightning_network

# Enable and start service
sudo systemctl enable --now lightning-network

# Verify installation
lightning-network --version
```

### Alpine Linux

```bash
# Install lightning-network
apk add --no-cache lightning_network

# Enable and start service
rc-update add lightning-network default
rc-service lightning-network start

# Verify installation
lightning-network --version
```

### openSUSE/SLES

```bash
# Install lightning-network
sudo zypper install -y lightning_network

# Enable and start service
sudo systemctl enable --now lightning-network

# Configure firewall
sudo firewall-cmd --permanent --add-port=9735/tcp
sudo firewall-cmd --reload

# Verify installation
lightning-network --version
```

### macOS

```bash
# Using Homebrew
brew install lightning_network

# Start service
brew services start lightning_network

# Verify installation
lightning-network --version
```

### FreeBSD

```bash
# Using pkg
pkg install lightning_network

# Enable in rc.conf
echo 'lightning-network_enable="YES"' >> /etc/rc.conf

# Start service
service lightning-network start

# Verify installation
lightning-network --version
```

### Windows

```bash
# Using Chocolatey
choco install lightning_network

# Or using Scoop
scoop install lightning_network

# Verify installation
lightning-network --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/lightning_network

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
lightning-network --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable lightning-network

# Start service
sudo systemctl start lightning-network

# Stop service
sudo systemctl stop lightning-network

# Restart service
sudo systemctl restart lightning-network

# Check status
sudo systemctl status lightning-network

# View logs
sudo journalctl -u lightning-network -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add lightning-network default

# Start service
rc-service lightning-network start

# Stop service
rc-service lightning-network stop

# Restart service
rc-service lightning-network restart

# Check status
rc-service lightning-network status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'lightning-network_enable="YES"' >> /etc/rc.conf

# Start service
service lightning-network start

# Stop service
service lightning-network stop

# Restart service
service lightning-network restart

# Check status
service lightning-network status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start lightning_network
brew services stop lightning_network
brew services restart lightning_network

# Check status
brew services list | grep lightning_network
```

### Windows Service Manager

```powershell
# Start service
net start lightning-network

# Stop service
net stop lightning-network

# Using PowerShell
Start-Service lightning-network
Stop-Service lightning-network
Restart-Service lightning-network

# Check status
Get-Service lightning-network
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream lightning_network_backend {
    server 127.0.0.1:9735;
}

server {
    listen 80;
    server_name lightning_network.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name lightning_network.example.com;

    ssl_certificate /etc/ssl/certs/lightning_network.example.com.crt;
    ssl_certificate_key /etc/ssl/private/lightning_network.example.com.key;

    location / {
        proxy_pass http://lightning_network_backend;
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
    ServerName lightning_network.example.com
    Redirect permanent / https://lightning_network.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName lightning_network.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/lightning_network.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/lightning_network.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:9735/
    ProxyPassReverse / http://127.0.0.1:9735/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend lightning_network_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/lightning_network.pem
    redirect scheme https if !{ ssl_fc }
    default_backend lightning_network_backend

backend lightning_network_backend
    balance roundrobin
    server lightning_network1 127.0.0.1:9735 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R lightning_network:lightning_network /etc/lightning_network
sudo chmod 750 /etc/lightning_network

# Configure firewall
sudo firewall-cmd --permanent --add-port=9735/tcp
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
sudo systemctl status lightning-network

# View logs
sudo journalctl -u lightning-network -f

# Monitor resource usage
top -p $(pgrep lightning_network)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/lightning_network"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/lightning_network-backup-$DATE.tar.gz" /etc/lightning_network /var/lib/lightning_network

echo "Backup completed: $BACKUP_DIR/lightning_network-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop lightning-network

# Restore from backup
tar -xzf /backup/lightning_network/lightning_network-backup-*.tar.gz -C /

# Start service
sudo systemctl start lightning-network
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u lightning-network -n 100
sudo tail -f /var/log/lightning_network/lightning_network.log

# Check configuration
lightning-network --version

# Check permissions
ls -la /etc/lightning_network
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 9735

# Test connectivity
telnet localhost 9735

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep lightning_network)

# Check disk I/O
iotop -p $(pgrep lightning_network)

# Check connections
ss -an | grep 9735
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  lightning_network:
    image: lightning_network:latest
    ports:
      - "9735:9735"
    volumes:
      - ./config:/etc/lightning_network
      - ./data:/var/lib/lightning_network
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update lightning_network

# Debian/Ubuntu
sudo apt update && sudo apt upgrade lightning_network

# Arch Linux
sudo pacman -Syu lightning_network

# Alpine Linux
apk update && apk upgrade lightning_network

# openSUSE
sudo zypper update lightning_network

# FreeBSD
pkg update && pkg upgrade lightning_network

# Always backup before updates
tar -czf /backup/lightning_network-pre-update-$(date +%Y%m%d).tar.gz /etc/lightning_network

# Restart after updates
sudo systemctl restart lightning-network
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/lightning_network

# Clean old logs
find /var/log/lightning_network -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/lightning_network
```

## Additional Resources

- Official Documentation: https://docs.lightning_network.org/
- GitHub Repository: https://github.com/lightning_network/lightning_network
- Community Forum: https://forum.lightning_network.org/
- Best Practices Guide: https://docs.lightning_network.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
