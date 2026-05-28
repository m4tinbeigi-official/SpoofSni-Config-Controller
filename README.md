# 🚀 ArvanSpoof – Auto Server Manager + SNI Spoof

**Deploy the cheapest ArvanCloud server (ir-thr-fr1) with automatic 3x-ui panel, SSL, DNS record, and one-click SNI Spoof – all from your browser.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![JavaScript](https://img.shields.io/badge/JavaScript-ES6+-blue)](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
[![ArvanCloud API](https://img.shields.io/badge/ArvanCloud-API-orange)](https://docs.arvancloud.ir/)

---

## ✨ Features

- **Zero backend** – Pure HTML/CSS/JS, runs entirely in your browser.
- **Auto server creation** – Cheapest plan (`g5-small2`, ~11€/month) in `ir-thr-fr1` region.
- **Automatic SSH key & balance validation** before deployment.
- **Domain support** – If your domain is already on ArvanCloud CDN, the tool will:
  - Add A record automatically.
  - Install a **free Let's Encrypt SSL** certificate with auto-renewal (cron job).
- **3x-ui (Senai panel)** installed with a **random 12‑character admin password**.
- **SNI Spoof (hcaptcha)** – Optional but persistent:
  - Starts on port `40443`.
  - Runs as a **systemd service** – survives reboots.
  - Status check & on‑demand activation from the UI.
- **Advanced progress** – Real‑time progress bar, 5‑step visual workflow, and coloured logs.
- **LocalStorage** – Saves API key (base64 encoded), SSH key name, and domain for next visits.
- **Cancel operation** – You can abort the whole process at any time.
- **Desktop notifications** – Alerts when the server is ready.
- **RTL & responsive** – Persian Vazir font, mobile‑friendly layout.

---

## 📋 Prerequisites

1. **ArvanCloud account** with sufficient credit (at least ~11€).
2. **API key** – From ArvanCloud panel → *Settings* → *IAM* → *Machine users* → *Create machine user*.  
   Format: `apikey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`
3. **SSH key** – Create a key pair in ArvanCloud panel (*Settings* → *SSH keys*) and remember its **name**.
4. **Domain (optional but recommended)** – Must already be added to ArvanCloud CDN/DNS service.

---

## 🛠️ Quick Start

1. Clone the repository or download `index.html`.
2. Open `index.html` in your browser (Chrome, Firefox, Edge – any modern browser).
3. Enter your **API key**, **SSH key name**, and **domain** (if any).
4. Click **✨ Start Auto Deployment**.
5. Wait 3–4 minutes – watch the steps and logs.
6. After completion, you'll receive:
   - Panel URL (HTTPS if domain provided)
   - Auto‑generated admin password
   - Server IP address
7. (Optional) Click **Activate SNI Spoof** to start the fake‑SNI tunnel on port `40443`.

> **Note:** The tool never sends your API key to any third‑party server – all requests go directly to ArvanCloud APIs via a public CORS proxy.

---

## 🖥️ What happens under the hood?

1. **Validation**  
   - Checks if the SSH key exists.  
   - Verifies account balance by attempting a dummy server creation (deleted immediately).

2. **Server creation**  
   - POST `/ecc/v1/regions/ir-thr-fr1/servers` with `g5-small2` flavor, Ubuntu 24.04, 25GB disk.

3. **IP polling**  
   - Waits until the server gets a public IP (up to 5 minutes).

4. **DNS & SSL (if domain provided)**  
   - Adds an A record to the existing domain on ArvanCloud CDN.  
   - Installs `acme.sh` and obtains a Let's Encrypt certificate.  
   - Configures the 3x-ui panel to use the certificate.  
   - Sets up a cron job to renew the certificate every 60 days.

5. **3x-ui (Senai) installation**  
   - Installs the latest version of [3x-ui](https://github.com/mhsanaei/3x-ui).  
   - Changes the default `admin` password to a random 12‑character string.  
   - The panel listens on port `54321` (or the random port assigned by 3x-ui).

6. **SNI Spoof preparation**  
   - Downloads the `wp` binary from a predefined source.  
   - Creates a `config.json` with `FAKE_SNI: "www.hcaptcha.com"`.  
   - Runs the binary inside a `screen` session.  
   - Creates two systemd services:  
     - `sni-spoof.service` → runs the tunnel.  
     - `sni-api.service` → a small HTTP API on port `8090` to check status and restart the tunnel.  
   - The tunnel automatically restarts after server reboot.

7. **LocalStorage & UI**  
   - Saves configuration (encrypted API key) for next visits.  
   - Shows final server details and allows deletion (with automatic A record cleanup).

---

## 📁 File Structure

├── index.html # Single‑file application (all CSS/JS included)
└── README.md # This file


No build step, no dependencies – just open the HTML file.

---

## 🔒 Security Notes

- The API key is stored in **localStorage** using simple Base64 encoding – **not** strong encryption.  
  Do **not** use this tool on a shared or public computer.
- All requests to ArvanCloud are proxied through a public CORS proxy (`cors-proxy.htmldriven.com`).  
  The proxy does **not** log or store your data, but be aware of the trust boundary.
- The `wp` binary used for SNI Spoof is downloaded from a third‑party source (`178.239.158.97`).  
  Inspect the script before using it in production.

---

## ❗ Troubleshooting

| Problem | Likely cause | Solution |
|---------|--------------|----------|
| `SSH key not found` | Wrong key name | Go to ArvanCloud panel → SSH keys → check the exact name. |
| `Insufficient balance` | Not enough credit | Add funds to your ArvanCloud account. |
| `Domain not found in CDN` | Domain not added to ArvanCloud DNS | First add the domain via ArvanCloud panel. |
| `Server IP not received` | Timeout or network issue | Wait a few minutes and click "Refresh servers". |
| `CORS proxy error` | Proxy blocked or rate‑limited | Refresh the page and try again. |
| `SNI Spoof not working` | Binary not compatible with Ubuntu 24.04 | Check logs on server: `journalctl -u sni-spoof` |

---

## 🧪 Development & Customization

You can change:

- **Server flavor** – modify `flavor_ref` inside `startFullProcess()` (default: `g5-small2`).
- **Region** – change `REGION` constant (default: `ir-thr-fr1`).
- **Startup script** – edit `getStartupScript()` (e.g., add your own panel or tools).

All code is self‑contained in `index.html` – no external libraries except the Vazir font and the CORS proxy.

---

## 🤝 Contributing

Pull requests are welcome! For major changes, please open an issue first to discuss what you would like to improve.

---

## 📄 License

[MIT](https://choosealicense.com/licenses/mit/) – free to use, modify, and distribute.

---

## 🌟 Show your support

If this tool saved you time, give it a ⭐ on GitHub!
