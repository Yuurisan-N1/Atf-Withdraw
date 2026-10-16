<div align="center">

<img width="100%" alt="header" src="https://capsule-render.vercel.app/api?type=waving&height=210&text=ATF%20Withdraw%20Bot&fontAlign=50&fontAlignY=36&fontSize=56&desc=Auto%20Withdraw%20%7C%20Captcha%20Solver%20%7C%20TON%20Address%20%7C%20Multi-Account&descAlign=50&descAlignY=58"/>

<img alt="typing" src="https://readme-typing-svg.demolab.com?font=Inter&size=18&duration=3000&pause=650&center=true&vCenter=true&width=900&lines=Auto+Withdraw+ATF+%7C+Above+500+ATF+Threshold;Built-in+Captcha+Solver+%7C+No+External+Service;TON+Wallet+Address+Converter+%7C+Raw+to+Friendly;Per-Account+Device+ID+Cached;Proxy+Support+%7C+Multi-Account"/>

<p>
  <img alt="ruby" src="https://img.shields.io/badge/Ruby-3.2+-CC342D?logo=ruby&logoColor=white"/>
  <img alt="platform" src="https://img.shields.io/badge/Platform-ATF%20Miners-111111"/>
  <img alt="multi-account" src="https://img.shields.io/badge/Multi--Account-Supported-111111"/>
  <img alt="proxy" src="https://img.shields.io/badge/Proxy-Supported-111111"/>
  <img alt="author" src="https://img.shields.io/badge/by-Yuurisandesu-111111"/>
</p>

<p>
  <b>ATF Withdraw Bot</b> has two components: a withdrawal automation bot written in binary downloader written in Ruby.<br/>
  The main bot signs into each account, checks the ATF balance against a 500 ATF minimum threshold, fetches the withdrawal captcha, solves it locally using a built-in bitmap OCR engine with no external service, and submits the withdrawal. The TON wallet address is converted from raw format to the friendly URL-safe format before display. All results are classified and reported per account.<br/>
  Built and distributed by <b>Yuurisandesu</b>.
</p>

</div>

---

## Table of Contents

- [Requirements](#requirements)
- [Install Ruby](#install-ruby)
- [Installation](#installation)
- [Configuration](#configuration)
- [Running the Bot](#running-the-bot)
- [Features](#features)
- [File Structure](#file-structure)
- [Disclaimer](#disclaimer)

---

## Requirements

- Ruby `3.2+` (for the downloader only)
- Git

---

## Install Ruby

Ruby is only needed to run the downloader script. Skip this section if you download the binary manually.

### Linux

```bash
sudo apt update && sudo apt install -y ruby git
```

### macOS

```bash
brew install ruby
```

### Windows

Download and run the installer from https://rubyinstaller.org. Check **Add Ruby executables to your PATH** during installation.

### Termux (Android)

```bash
pkg update && pkg install ruby git
```

---

## Installation

**Clone the repository:**

```bash
git clone https://github.com/Yuurisan-N1/Atf-Withdraw.git
cd Atf-Withdraw
```

**Download the binary:**

```bash
ruby bot.rb
```

The script shows a numbered menu:

```
1. ATF Linux ARM64
2. ATF Linux AMD64
3. Windows (PowerShell / CMD)
```

Enter the number for your platform. The binary downloads with a live progress bar and is set to executable automatically on Linux.

Or download manually from the Releases page:
https://github.com/Yuurisan-N1/Atf-Withdraw/releases/latest

| File | Platform |
|---|---|
| `atf-linux-amd64` | Linux x86_64 |
| `atf-linux-arm64` | Linux ARM64 |
| `atf.exe` | Windows x86_64 |

**Linux after manual download:**

```bash
chmod +x atf-linux-amd64
```

---

## Configuration

### 1. Accounts (data.txt)

Fill `data.txt` with Telegram WebApp `initData` for each account, one per line:

```
user=%7B%22id%22...&hash=abc123
user=%7B%22id%22...&hash=def456
```

> `initData` can be obtained from the browser DevTools when opening ATF Miners on Telegram Web.

### 2. Proxy (proxy.txt)

Fill `proxy.txt` with proxies, one per line (optional, leave empty to run without proxy):

```
host:port
host:port:user:pass
http://user:pass@host:port
```

Proxies are assigned to accounts by index in round-robin order.

### 3. Bot Settings (config.json)

`sleep_seconds` controls how many seconds the bot waits between cycles. If `config.json` is missing, the bot falls back to a default of `3600` seconds.


---

## Features

### Auto Withdrawal
The bot signs into each account, reads the ATF balance, and compares it against the 500 ATF minimum threshold. If the balance is above the threshold and the account has a wallet address linked, it proceeds with the withdrawal. If the balance is too low or the wallet is not set, the withdrawal is skipped and the reason is logged.

### Built-in Captcha Solver
The withdrawal flow requires solving a 5-character image captcha. The bot solves it locally with no external service or API call. It decodes the captcha image, extracts each character by isolating connected pixel components, denoises them, and matches each one against a built-in set of bitmap glyph templates covering all uppercase letters and digits. If an exact match is not found, the closest template by pixel distance is used. Multiple brightness thresholds are tried automatically until exactly 5 components are detected.

### TON Address Converter
The wallet address stored on the server is in raw `workchain:hex` format. The bot converts it to the standard URL-safe friendly format for display by encoding the workchain, the 32-byte hash, and a CRC-16 checksum.

### Withdrawal Classification
Every withdrawal response is classified into one of four outcomes: `accepted` (confirmed), `submitted` (queued for admin approval), `verify` (account not yet verified), or `refused`. Each outcome is reported with its own log message. If a payout receipt hash is included in the response, it is logged separately.

### Per-Account Device ID
Each account gets a unique device ID on first run, generated from a UUID. The ID is saved to `.data.log` keyed by Telegram user ID and reused on every subsequent cycle to keep the device identity consistent.

### Multi Account
All accounts in `data.txt` are processed sequentially within every cycle. The cycle number is logged at the start and end of each round.

### Proxy Support
Proxies are loaded from `proxy.txt` and assigned to accounts by position in round-robin order. All common proxy formats are normalized automatically. Proxy credentials are masked in log output. Running without proxies is fully supported.

### Auto Countdown
After all accounts complete a cycle, the bot displays a live `HH:MM:SS` countdown until the next cycle starts.

---

## File Structure

```text
Atf-Withdraw/
├── atf-linux-amd64      # Linux x86_64 binary
├── atf-linux-arm64      # Linux ARM64 binary
├── atf.exe              # Windows binary
├── bot.rb               # Interactive downloader script (Ruby)
├── config.json          # Sleep duration between cycles
├── data.txt             # Account initData, one per line
├── proxy.txt            # Proxy list, one per line (optional)
├── .data.log            # Per-account device ID cache (auto-generated)
├── LICENSE              # License file
└── utils/
    └── banner.rb        # Banner using yuurisan module
```

---

## Disclaimer

This tool is built for educational and technical exploration purposes. Use it wisely and at your own responsibility.

---

<div align="center">
<img width="100%" alt="footer" src="https://capsule-render.vercel.app/api?type=waving&height=120&section=footer"/>
</div>