# Rocky Linux PXE Boot + Kickstart Lab

A step-by-step lab to build a fully automated PXE server for installing Rocky Linux 9.6 over the network using using PXE with Kickstart as an optional feature. This lab is designed to be reproducible and beginner-friendly, with config examples and a step-by-step walkthrough

---

## Features
- PXE boot via `dnsmasq` (DHCP + TFTP)
- HTTP-based installer with a mounted Rocky Linux 9.6 ISO
- Kickstart automation
- Walkthrough-style documentation 
- Secure sample configs that hide sensitive data

---

##  The Setup
- OS: Rocky Linux 9.5+ (PXE Server)
- Network: Bridge interface (`br0`) on the PXE server [setup](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/blob/main/network-bridge/setup-bridge.md)
- PXE Client: Any physical machine that supports network boot

---

## Getting Started
1. Clone this repo
2. Follow [`walkthrough.md`](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/blob/main/walkthrough.md) full step-by-step setup.
3. If you environment is NAT based, follow steps to create a bridge [here](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/tree/main/network-bridge).
3. Customize the sample configs in [`mirror-configs/`](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/tree/main/mirror-configs) to fit your environment.

---

## Security Notice
Do **not** push actual IP addresses or sensitive network config.


## Set Secure Passwords for [ks.cfg](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/blob/main/mirror-configs/ks.cfg.sample)

Before using this Kickstart file, generate a secure SHA-512 password hash 

```
openssl passwd -6
```

You'll be prompted to enter your desired password. Copy the resulting hash and paste it in place of the `$6$REPLACE_WITH_HASHED_PASSWORD` placeholders inside `ks.cfg`:

# Ready,Set, Build!

Follow the steps [here](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/blob/main/walkthrough.md) to begin.

---

If you have any questions, comments or suggestions, please feel free to reach out to me on LinkedIn or add a comment in [Discussions](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/discussions/1) 
