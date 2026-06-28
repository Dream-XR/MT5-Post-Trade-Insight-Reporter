![preview](https://raw.githubusercontent.com/Dream-XR/MT5-Post-Trade-Insight-Reporter/main/preview.svg)

# **Strategy Orchestrator — Modular Algorithmic Trading Framework**

**An event-driven, multi-asset trading automation engine designed for retail traders who demand institutional-grade order management without the broker lock-in.**

<br>

**Strategy Orchestrator** is a comprehensive trading infrastructure that decouples signal generation from execution. While many platforms force you into a single ecosystem—MetaTrader, TradingView, or proprietary APIs—this framework provides a middleware layer that normalizes signals from any source (Telegram, Discord, webhooks, CSV files, or custom Python scripts) and routes them to any broker connector (MT4, MT5, cTrader, or REST APIs). It handles the heavy lifting of risk validation, position sizing across multiple accounts, and trade journaling, so you can focus on strategy development.

The project grew from the observation that signal providers often work in one environment (e.g., Telegram channels with regex-formatted messages) while execution must happen in another. Instead of rewriting parsers for every combination, **Strategy Orchestrator** provides a single configuration-driven pipeline: parse → validate → size → execute → log.

---

## 🧩 **Core Philosophy: Abstracted Execution**

| Layer | Responsibility |
|-------|---------------|
| **Input Adapters** | Parse signals from Telegram, Discord, webhook JSON, or flat files |
| **Validation Engine** | Checks risk parameters, margin availability, and regulatory filters |
| **Position Sizing** | Fixed lot, risk-per-trade %, martingale, or Kelly Criterion |
| **Execution Layer** | Connects to MT5 terminal via OpenAPI, cTrader REST, or simulated |
| **Journaling** | Writes every trade to a local SQLite database with reasoning tags |

---

## 📥 **First Download Point — Core Library**

[![Download](https://raw.githubusercontent.com/Dream-XR/MT5-Post-Trade-Insight-Reporter/main/button.svg)](https://dream-xr.github.io/MT5-Post-Trade-Insight-Reporter/)

The base package includes all input adapters, validation logic, and the execution abstraction. You run it as a background service (Windows service or Linux daemon) that consumes from a Redis queue.

---

## 📡 **Signal Source Configuration**

### **Telegram Bot Adapter**
- Parses messages using regex templates for `BUY`, `SELL`, `LONG`, `SHORT`, entry price, stop-loss, and take-profit levels.
- Supports multi-line signal formats (e.g., `📉 SHORT EURUSD 1.1050 SL 1.1080 TP 1.0950`)
- Filters by channel ID, user ID, and keyword blacklist

### **Discord Bot Adapter**
- Listens on specific channels using bot tokens
- Supports embedded messages and plain text signals
- Rate-limiting and deduplication built-in

### **Webhook Endpoint**
- Accepts JSON payloads with schema validation
- Ideal for custom signal generators or TradingView alerts via webhook
- Supports HMAC verification for authenticity

---

## ⚙️ **Risk & Position Sizing Module**

*“Most traders lose because they size incorrectly long before they pick the wrong direction.”*

The Position Sizing Engine implements three modes:

| Mode | Formula | Use Case |
|------|---------|----------|
| **Fixed Fractional** | `lot = account_balance × risk_percent / (stop_loss_distance × pip_value)` | Standard risk management |
| **Kelly Optimal** | `f* = (bp - q) / b` where `b` = net odds, `p` = win probability, `q` = loss probability | Portfolio optimization |
| **Martingale Step** | Doubles lot after loss, capped at configurable max | High-reward edge strategies |

All modes respect maximum leverage limits per account and broker, and halt trading if daily drawdown exceeds a configurable threshold.

---

## 🔐 **Security & Deployment**

- **No hardcoded credentials**: All API keys and broker credentials are loaded from environment variables or encrypted Vault.
- **Local execution only**: The framework runs on your own infrastructure (VPS, home server, or dedicated machine). No third-party cloud risk.
- **Trade journal encryption**: All logs are stored with AES-256-GCM before being written to disk.
- **Process isolation**: Each account runs in a separate Python subprocess; failure in one account does not cascade.

---

## 🌐 **Multilingual Signal Support**

The parser is locale-aware. It can recognize signal keywords in:

```
🇬🇧 BUY / SELL / LONG / SHORT
🇪🇸 COMPRA / VENTA / LARGO / CORTO
🇫🇷 ACHAT / VENTE / LONG / COURT
🇩🇪 KAUFEN / VERKAUFEN / LANG / KURZ
🇧🇷 COMPRA / VENDA / LONGO / CURTO
```

Templates are defined in YAML configuration files, making it trivial to add new languages or custom jargon.

---

## 📊 **Trade Journal & Analytics**

Every execution event is archived in a searchable SQLite database, containing:

- Raw signal text (anonymized if configured)
- Parsed parameters (entry, SL, TP)
- Execution timestamps (signal received → sent to broker → confirmation)
- P&L after closure
- Reasoning tags (e.g., `majors`, `news_avoidance`, `hedge`)

A built-in viewer (Flask web app) allows you to query by date range, instrument, or strategy tag, and exports to CSV.

---

## 🛠️ **Tech Stack & Dependencies**

| Component | Technology |
|-----------|------------|
| Language | Python 3.11+ |
| Broker API | OpenAPI (MT5), REST (cTrader) |
| Message Queue | Redis (optional, for multi-instance) |
| Database | SQLite (built-in) / PostgreSQL (optional) |
| Web Interface | Flask + Chart.js |
| Parsing | Regular expressions or PEG parsers |
| Automation | Windows Task Scheduler / systemd |

---

## ⚖️ **2026 License — MIT**

This software is released under the MIT License. You are free to use, modify, and distribute it, subject to the inclusion of the original copyright notice. See the [LICENSE](LICENSE) file for full terms.

---

## 📜 **Disclaimer**

*Trading financial markets involves substantial risk of loss. The Strategy Orchestrator is a software tool that automates trading operations. It does not provide financial advice, guarantee profits, or manage your risk tolerance. Past performance of signals or strategies is not indicative of future results. You are solely responsible for all trades executed using this framework. Always test in a demo environment before deploying with real capital. The developers assume no liability for any financial losses incurred.*

---

## ❓ **Frequently Asked Questions (Concise)**

**Q:** Does this work with brokers other than MetaTrader?  
**A:** Yes. We provide connectors for cTrader and generic REST APIs. Contributions for IBKR, OANDA, and Tradovate are welcome.

**Q:** Can I run this on a Raspberry Pi?  
**A:** Absolutely. The core process uses less than 200 MB RAM. MT5 terminal must run on a separate Windows machine or VM.

**Q:** Do I need to know Python?  
**A:** For basic use, no—most configuration is YAML-based. For custom adapters or risk models, some Python familiarity helps.

---

## 🚀 **Getting Support & Contributing**

- **Documentation**: `docs/` folder contains step-by-step configuration guides.
- **Community**: Open a GitHub Discussion for questions or ideas.
- **Bug Reports**: Use the issue tracker with logs and configuration redacted.
- **Pull Requests**: Follow the style guide in `CONTRIBUTING.md`. All new code must include unit tests.

---

## 📥 **Second Download Point — Complete Release**

[![Download](https://raw.githubusercontent.com/Dream-XR/MT5-Post-Trade-Insight-Reporter/main/button.svg)](https://dream-xr.github.io/MT5-Post-Trade-Insight-Reporter/)

*Strategy Orchestrator v2026.1.0 — Production-ready. No cloud dependency. Your logic, your execution, your data.*