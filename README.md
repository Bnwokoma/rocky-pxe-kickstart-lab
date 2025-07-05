# Rocky Linux PXE Boot + Kickstart Lab

A step-by-step lab to build a fully automated PXE server for installing Rocky Linux 9.6 over the network using Kickstart. This lab is designed to be reproducible and beginner-friendly, with config examples and screenshots.

---

## Features
- PXE boot via `dnsmasq` (DHCP + TFTP)
- HTTP-based installer with mounted Rocky Linux 9.6 ISO
- Kickstart automation for headless installs
- Walkthrough-style documentation with screenshots
- Secure sample configs that hide sensitive data

---

##  The Setup
- OS: Rocky Linux 9.5+ (PXE Server)
- Network: Bridge interface (`br0`) on the PXE server [setup](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/blob/main/network-bridge/setup-bridge.md)
- PXE Client: Any physical machine that supports network boot

---

## Repo Structure
- [`walkthrough.md`](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/blob/main/walkthrough.md)  
- [`mirror-configs/`](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/tree/main/mirror-configs)– sample configurationg


---

## 🧰 Getting Started
1. Clone this repo
2. Follow [`walkthrough.md`](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/blob/main/walkthrough.md) & ['network-bridge.md'](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/tree/main/network-bridge) full step-by-step setup.
3. Customize the sample configs in [`mirror-configs/`](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/tree/main/mirror-configs) to fit your environment.

---

## Security Notice
Do **not** push actual IP addresses or sensitive network config.


## Set Secure Passwords for [ks.cfg](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/blob/main/mirror-configs/ks.cfg.sample)

Before using this Kickstart file, generate a secure SHA-512 password hash on your PXE server or any Linux machine:

```
openssl passwd -6
```

You'll be prompted to enter your desired password. Copy the resulting hash and paste it in place of the `$6$REPLACE_WITH_HASHED_PASSWORD` placeholders inside `ks.cfg`:
