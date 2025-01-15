#!/bin/bash
# Script to upgrade all DNF/YUM packages and apply Ksplice updates on Oracle Linux

# Exit immediately if a command exits with a non-zero status
set -e

# Log file
LOG_FILE="/var/log/yum_ksplice_update.log"

# Function to log messages
log_message() {
    echo "$(date +"%Y-%m-%d %H:%M:%S") - $1" | tee -a "$LOG_FILE"
}

# Ensure the script is run as root
if [[ $EUID -ne 0 ]]; then
   echo "This script must be run as root." >&2
   exit 1
fi

# Detect OS version
OS_VERSION=$(grep -oP '(?<=release )\d+' /etc/redhat-release)

# Restart Oracle agent to fix common issues
log_message "Restarting Oracle Cloud Agent..."
sudo systemctl restart oracle-cloud-agent

# Clean up YUM/DNF cache
log_message "Cleaning package manager cache..."
if [[ $OS_VERSION -eq 8 ]]; then
    dnf clean all
else
    yum clean all
fi

# Update package metadata and upgrade all packages
log_message "Upgrading packages..."
if [[ $OS_VERSION -eq 8 ]]; then
    dnf -y upgrade
else
    yum -y upgrade
fi

# Apply Ksplice updates
log_message "Applying Ksplice updates..."
sudo uptrack-upgrade -y

log_message "System update and Ksplice upgrade completed successfully."
