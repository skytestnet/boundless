# 🧠 Boundless Prover Setup Guide

This guide helps you set up a Boundless Prover using either the **automated script** or a **manual step-by-step method**. It also includes broker configuration, GPU optimization, benchmarking, and multi-broker setup.

---

## ⚡ Automated Setup (Recommended)

### 1. Download and Run the Installer

```bash
# Update packages
apt update && apt upgrade -y

# Install wget if missing
apt install wget -y

# Download the installation script
wget https://raw.githubusercontent.com/0xmoei/boundless/main/install_prover.sh -O install_prover.sh

# Make it executable
chmod +x install_prover.sh

# Run the installer
./install_prover.sh

### 2. Clone

git clone https://github.com/boundless-xyz/boundless
cd boundless
git checkout release-0.11
