# Proxmox VE Base Setup

This document outlines the steps taken to set up the Proxmox VE Base on my laptop.

## Steps

### 1. Install Proxmox VE
- Installed Proxmox VE on my laptop using a USB drive with the `proxmox-ve_8.4-1.iso` image.
- Configured a static IP address for the Proxmox server: `192.168.1.163/24`.

### 2. Remove Enterprise Repositories
- Removed the Proxmox enterprise repositories since I don’t have a subscription, as they were causing update errors:

Err:5 https://enterprise.proxmox.com/debian/ceph-quincy bookworm InRelease
401  Unauthorized [IP: 45.84.67.184 443]
E: Failed to fetch https://enterprise.proxmox.com/debian/ceph-quincy/dists/bookworm/InRelease 401 Unauthorized
E: The repository 'https://enterprise.proxmox.com/debian/ceph-quincy bookworm InRelease' is not signed.


### 3. Configure Lid Close Behavior
- Ensured the laptop keeps running even when the lid is closed to prevent dust accumulation.
- Edited the configuration file `/etc/systemd/logind.conf`:
- Uncommented `HandleLidSwitchDocked=ignore` by removing the `#`.
- Added the line `HandleLidSwitch=ignore`.
- Restarted the `systemd-logind` service to apply changes:
```bash
systemctl restart systemd-logind piggy

Tested by closing the laptop lid multiple times to confirm the system remains active.


4. Set Up Daily Updates and Backups
Created a bash script to automate daily updates and backups for Proxmox, including future VMs and containers.
Created the script file with root permissions:

nano /root/proxmox-update-backup.sh

Pasted the following script (provided by Grok by xAI):

#!/bin/bash
# Script to update Proxmox and create compressed backups of VMs and containers

# Log file for tracking updates and backups
LOGFILE="/var/log/proxmox-update-backup.log"
BACKUP_DIR="/var/lib/vz/dump" # Default Proxmox backup directory
DATE=$(date '+%Y-%m-%d_%H-%M-%S')

# Redirect output to log file
exec >> $LOGFILE 2>&1
echo "=== Proxmox Update and Backup started at $DATE ==="

# Step 1: Update package lists and upgrade Proxmox
echo "Updating Proxmox..."
apt update
apt full-upgrade -y
echo "Proxmox update completed."

# Step 2: Get list of all VMs and containers
VMS=$(qm list | awk 'NR>1 {print $1}') # Get VM IDs
CTS=$(pct list | awk 'NR>1 {print $1}') # Get Container IDs

# Step 3: Create compressed backups for each VM and container
echo "Starting backups..."
for ID in $VMS; do
    echo "Backing up VM ID: $ID"
    vzdump $ID --compress zstd --mode snapshot --dumpdir $BACKUP_DIR --mailto root
    if [ $? -eq 0 ]; then
        echo "Backup of VM $ID completed successfully."
    else
        echo "Backup of VM $ID failed."
    fi
done

for ID in $CTS; do
    echo "Backing up Container ID: $ID"
    vzdump $ID --compress zstd --mode snapshot --dumpdir $BACKUP_DIR --mailto root
    if [ $? -eq 0 ]; then
        echo "Backup of Container $ID completed successfully."
    else
        echo "Backup of Container $ID failed."
    fi
done



# Step 4: Clean up old backups (optional, keep last 3 backups)
echo "Cleaning up old backups (keeping last 3)..."
find $BACKUP_DIR -name "vzdump-*.tar.zst" -type f -mtime +7 -delete
echo "Backup cleanup completed."

echo "=== Proxmox Update and Backup finished at $(date '+%Y-%m-%d_%H-%M-%S') ==="


Made the script executable:

chmod +x /root/proxmox-update-backup.sh

Scheduled the script to run daily at 2 AM using cron:
Edited the crontab:

crontab -e

Selected nano as the editor (option 1).
Added the following line to run the script daily at 2 AM:

0 2 * * * /root/proxmox-update-backup.sh


Notes
Proxmox web interface is accessible at https://192.168.1.163:8006.
The backup script logs its activity to /var/log/proxmox-update-backup.log.
Backups are stored in /var/lib/vz/dump with zstd compression.

Executed the command to restart the systemd-logind:
systemctl restart systemd-logind 
