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

### 3. Install Core tools

# Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
. "$HOME/.cargo/env"
rustup update
apt install cargo -y
cargo --version

# RISC Zero
curl -L https://risczero.com/install | bash
source ~/.bashrc
rzup --version
rzup install rust
cargo install cargo-risczero
rzup install cargo-risczero

# Bento Client
cargo install --git https://github.com/risc0/risc0 bento-client --bin bento_cli
echo 'export PATH="$HOME/.cargo/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
bento_cli --version

# Boundless CLI
cargo install --locked boundless-cli
source ~/.bashrc
boundless -h


### 4. ⚙️ Compose Optimization (Single GPU)

```bash
nano compose.yml
```

- Increase CPU/RAM for:
  - `x-exec-agent-common`
  - `gpu_prove_agent0`

Example:
```yaml
  cpus: 4
  mem_limit: 6G
```

