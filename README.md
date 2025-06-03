# Proxmox Production Setup

## Overview
This repository documents my Proxmox-based production server setup, built to demonstrate my skills in virtualization, networking, and system administration. The project includes a web server, firewall, VPN, reverse proxy, and a future Kubernetes cluster, all running on Proxmox VE.

## Setup Components
- **Proxmox VE Base**: 1GB RAM, 10GB storage (strong password, auto-updates, backup compression)
- **Web Service VM**: Ubuntu + Nginx, 2GB RAM, 20GB storage, 1 vCPU (non-root user, logs, permissions 644, backups)
- **Firewall Container**: Alpine + iptables, 512MB RAM, 5GB storage, 1 vCPU (allow ports 80/443/1194, log dropped packets)
- **VPN Server VM**: Ubuntu + OpenVPN, 1GB RAM, 10GB storage, 1 vCPU (non-root user, strong encryption, logs, backups)
- **Reverse Proxy VM**: Ubuntu + Nginx, 2GB RAM, 20GB storage, 1 vCPU (non-root user, logs, backups)
- **Future Kubernetes VM**: Reserved, 4GB RAM, 40GB storage, 2 vCPUs
- **Remaining Resources**: 5.5GB RAM, 407GB storage (for backups, logs)

## Purpose
This project showcases my ability to:
- Set up and manage a virtualized environment using Proxmox VE
- Configure secure networking with a firewall and VPN
- Deploy a web server with a reverse proxy
- Prepare for container orchestration with Kubernetes
- Document the process for a portfolio to apply for DevOps/sysadmin roles

## Documentation
I will document each component as I build it, including setup steps, configurations, and screenshots (e.g., Proxmox interface, web page, logs).

## Contact
For questions, reach out to kevigjonaj1@gmail.com.
