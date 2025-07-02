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
```

---

### 2. Clone Repository

```bash
git clone https://github.com/boundless-xyz/boundless
cd boundless
git checkout release-0.11
```

---

### 3. Install Core Tools

```bash
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
```

---

### 4. ⚙️ Compose Optimization (Single GPU)

```bash
nano compose.yml
```

Increase CPU/RAM for:
- `x-exec-agent-common`
- `gpu_prove_agent0`

Example:

```yaml
  cpus: 4
  mem_limit: 6G
```

---

## 🚀 Run Bento for Test

```bash
just bento                  # Run local proving infra
just bento logs             # Logs
RUST_LOG=info bento_cli -c 32  # Run test proof
```

🔍 Use `nvtop` in another terminal to monitor GPU usage.

---

## 🌐 Network Configuration

```bash
nano .env.base
```

Add:

```env
export RPC_URL="https://YOUR_RPC_URL"
export PRIVATE_KEY=your_private_key
```

Inject environment:

```bash
source .env.base
```

📌 Tip: You can also use `.env.base-sepolia` or `.env.eth-sepolia` depending on your target network.

---

## 💸 Deposit USDC Stake

```bash
boundless account deposit-stake AMOUNT
boundless account stake-balance
```

---

## 🧑‍🚀 Run the Full Stack

```bash
just broker                   # Start Bento + Broker
just broker logs              # View logs
docker compose logs -f broker # View broker logs live
```

---

## 📏 Benchmark Your Prover

```bash
apt install postgresql postgresql-client -y
psql --version

# Benchmark using request ID(s)
boundless proving benchmark --request-ids <ID1>,<ID2>

# Benchmark using iteration count
RUST_LOG=info bento_cli -c 4096
```

---

## 🛠️ Broker Configuration

```bash
cp broker-template.toml broker.toml
nano broker.toml
```

Key parameters to tune:
- `mcycle_price`
- `peak_prove_khz`
- `max_concurrent_proofs`
- `min_deadline`
- `lockin_priority_gas`

---

## 🤖 Multi-Broker Setup

### 1. Add `broker2` service to `compose.yml`:

```yaml
  broker2:
    ...
    volumes:
      - ./broker2.toml:/app/broker.toml
      - broker2-data:/db/
    env_file:
      - .env.eth-sepolia
```

### 2. Add new volume at the bottom of `compose.yml`:

```yaml
volumes:
  ...
  broker2-data:
```

### 3. Create `broker2.toml` config:

```bash
cp broker.toml broker2.toml
nano broker2.toml
```

✅ Tips:
- Split `peak_prove_khz` between brokers (e.g., 250 + 250 for 500 total)
- Ensure `max_concurrent_preflights` ≤ number of exec agents
- Set `max_concurrent_proofs = 1` per GPU

---

## 🛑 Safe Update / Stop

```bash
# Optional: Pause proving first
nano broker.toml # set max_concurrent_proofs = 0

# Stop broker
just broker down

# Or clean the volumes before update
just broker clean

# Update to the latest release tag
git checkout <new_version_tag>

# Restart
just broker
```

---

## 🐞 Debug Tips

### Fix: Too many open files (os error 24)

```bash
nano /etc/security/limits.conf
# Add at the bottom:
* soft nofile 65535
* hard nofile 65535

nano /lib/systemd/system/docker.service
# Under [Service] add:
LimitNOFILE=65535

# Apply changes:
systemctl daemon-reload
systemctl restart docker
```

---

## 📚 Resources

- 🔗 Official Docs: https://github.com/boundless-xyz/boundless
- 🧪 USDC Testnet Faucet: https://faucet.circle.com/
- 🔍 Boundless Explorer: https://explorer.boundless.xyz
