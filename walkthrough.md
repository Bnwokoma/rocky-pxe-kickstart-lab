# walkthrough.md

# PXE Server Walkthrough – Rocky Linux 9.6 + Kickstart

This guide walks through setting up a PXE server that installs Rocky Linux 9.6 over the network with optional Kickstart automation.

---

## 1️⃣ System Prep
**PXE Server OS**: Rocky Linux 9.5 or 9.6  
**Ensure internet access** for installing packages.

```
sudo dnf update -y
sudo dnf install -y dnsmasq httpd syslinux xinetd
```

Create a bridge interface (`br0`) using `nmcli`:
PXE isnt a fan of NAT

```
nmcli con add type bridge ifname br0 con-name br0 autoconnect yes
nmcli con add type ethernet ifname enp1s0 master br0
nmcli con up br0
```

Verify with:

```
nmcli con show
```

---

## 2️⃣ ISO Mounting
Mount the Rocky 9.6 ISO:

```
mkdir -p /var/www/html/rocky
mount -o loop Rocky-9.6-x86_64-dvd.iso /var/www/html/rocky
```

Make sure Apache (`httpd`) is running:

```
sudo systemctl enable --now httpd
```

---

## 3️⃣ TFTP Boot Setup
Create the TFTP directory:

```
mkdir -p /var/lib/tftpboot/rocky96
```

Copy `vmlinuz` and `initrd.img` from the ISO's `/images/pxeboot/` to the TFTP folder.

---

## 4️⃣ Configure dnsmasq
Sample: `mirror-configs/dnsmasq.conf.sample`

Make sure it provides:
- IP addresses
- TFTP boot file (pxelinux.0)

Start the service:

bash bash
sudo systemctl enable --now dnsmasq
bash bash

Check logs:

bash bash
journalctl -u dnsmasq
bash bash

---

## 5️⃣ PXE Boot Menu
Create `pxelinux.cfg/default` inside the TFTP folder.  
Sample in `mirror-configs/pxelinux.cfg.default.sample`

---

## 6️⃣ Kickstart Setup
Sample config: `mirror-configs/ks.cfg.sample`

Place it in the HTTP root and reference it in the PXE menu:

`inst.ks=http://<server-ip>/ks.cfg`

---

## 7️⃣ Boot the Client
- Set client to boot from network (PXE)
- Confirm boot menu loads
- Select install option or let it auto-start
- Watch Kickstart begin automatically


Ready to start? Begin at section 1️⃣ and follow along!


