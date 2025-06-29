# Rocky Linux PXE Boot + Kickstart Lab

A step-by-step lab to build a fully automated PXE server for installing Rocky Linux 9.6 over the network using Kickstart. This lab is designed to be reproducible and beginner-friendly, with config examples and screenshots.

---

## 📌 Features
- PXE boot via `dnsmasq` (DHCP + TFTP)
- HTTP-based installer with mounted Rocky Linux 9.6 ISO
- Kickstart automation for headless installs
- Walkthrough-style documentation with screenshots
- Secure sample configs that hide sensitive data

---

##  The Setup
- OS: Rocky Linux 9.5+ (PXE Server)
- Network: Bridge interface (`br0`) on the PXE server
- PXE Client: Any physical machine that supports network boot

---

## Repo Structure
- `walkthrough.md` – full step-by-step setup
- `mirror-configs/` – sample configs (no real IPs)
- `scripts/` – optional helper scripts like ISO mounting
- `screenshots/` – proof-of-work images for each stage

---

## 📸 Screenshots Preview
- PXE boot menu
- GUI installer loading
- Kickstart auto-install in progress
- DHCP logs showing PXE handshake

---

## 🧰 Getting Started
1. Clone this repo
2. Follow `walkthrough.md` step by step
3. Customize the sample configs in `mirror-configs/`

---

## Security Notice
Do **not** push actual IP addresses or sensitive network config. Use `.sample` files instead. 
