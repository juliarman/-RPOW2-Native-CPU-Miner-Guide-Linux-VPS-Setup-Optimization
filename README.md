# RPOW2 Native CPU Miner Guide | Linux VPS Setup & Optimization

Fast native CPU mining for RPOW2 using Linux VPS.

---

# Requirements

Recommended:
- Ubuntu 22/24
- 4+ vCPU
- Node.js
- GCC / build-essential

Higher vCPU = higher hash rate.

Example:
- 20 vCPU → ~50 MH/s stable

---

# Install Dependencies

```bash
sudo apt update
sudo apt install git nodejs npm build-essential -y
```

Check versions:

```bash
node -v
npm -v
gcc --version
```

---

# Clone Repository

```bash
git clone https://github.com/stablemarkk/rpow_cli_miner.git

cd rpow_cli_miner
```

---

# Build Native Miner

```bash
chmod +x build-native.sh

./build-native.sh
```

Expected output:

```text
Built ./rpow-native-miner
```

---

# Linux Fix (.exe issue)

By default the CLI searches for:

```text
rpow-native-miner.exe
```

Linux binary should be:

```text
rpow-native-miner
```

Edit:

```bash
nano rpow-cli.js
```

Replace:

```text
rpow-native-miner.exe
```

with:

```text
rpow-native-miner
```

Save:
- CTRL + O
- ENTER
- CTRL + X

---

# Login Using Browser Cookie

Direct CLI login may fail because of Cloudflare Turnstile:

```text
TURNSTILE_REQUIRED
```

Use browser session cookie instead.

---

# Get Session Cookie

Login normally:

https://rpow2.com

Open DevTools:
- F12
- Application
- Cookies

Copy:

```text
rpow_session
```

---

# Create State File

```bash
nano .rpow-cli-state.json
```

Paste:

```json
{
  "updated_at": "2026-05-09T06:44:33.616Z",
  "cookies": {
    "rpow_session": "PASTE_COOKIE_HERE"
  }
}
```

Replace:

```text
PASTE_COOKIE_HERE
```

with your real cookie value.

---

# Test Session

```bash
node rpow-cli.js me
```

If successful:

```text
INFO me email="..."
```

---

# Start Mining

```bash
node rpow-cli.js run --count 1000000 --engine native --workers 16 --verbose
```

---

# Recommended Workers

Example results on 20 vCPU VPS:

| Workers | Result |
|---|---|
| 20 | ~60 MH/s but unstable |
| 16 | ~50 MH/s stable |
| 12 | very stable |

Sweet spot:
- 16 workers

---

# Common Errors

## already claimed

```text
CHALLENGE_ALREADY_CLAIMED
```

Usually caused by:
- miner too fast
- server race condition

---

## 429 Too Many Requests

```text
Rate limit exceeded
```

Reduce workers count.

Recommended:
- 12–16 workers

---

# Run in Background (PM2)

Install PM2:

```bash
npm install -g pm2
```

Start miner:

```bash
pm2 start "node rpow-cli.js run --count 1000000 --engine native --workers 16 --verbose" --name rpow-native
```

Save:

```bash
pm2 save
pm2 startup
```

---

# Monitor Logs

```bash
pm2 logs rpow-native
```

Status:

```bash
pm2 status
```

---

# About LAST TOKEN

`LAST TOKEN` is not a new coin/token.

It is:
- the proof/token object ID from the latest successful mint.

Reward itself goes to your RPOW balance:

```text
0.001 RPOW
```

per successful mint.

---

# Notes

This native C miner is significantly faster than the older Rust miner:
- lower overhead
- better CPU utilization
- higher hash throughput
- more stable performance
