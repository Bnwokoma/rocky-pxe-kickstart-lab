# PXE Server Walkthrough – Rocky Linux 9.6 + Kickstart

This guide walks through setting up a PXE server that installs Rocky Linux 9.6 over the network with optional Kickstart automation.

--- 
## Customizing `.sample` Files
To make the `.sample` config files work for your environment:

- Replace every `x.x.x.x` with values matching your lab environment.
- For example, if your PXE server is `192.168.50.10`, replace the DHCP range and `dhcp-boot` lines accordingly.
- Make sure that your subnet mask, broadcast address, gateway, and DNS server match your actual network layout.

---

## System Prep
**PXE Server OS**: Rocky Linux 9.6  
**Ensure internet access** for installing packages.
**Baremetal machine***


####Install Packages & start/enable them
```
sudo dnf update -y # applies updates to your system

sudo dnf install -y dnsmasq httpd syslinux tftp-server  # installs the necessary packages 

sudo dnf systemctl enable --now (nameOfService)
```

####Create a bridge interface named (`br0`) using `nmcli`:
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

## Copy contents of syslinux to /var/lib/tftpboot

```bash
sudo cp -r /usr/share/syslinux/* /var/lib/tftpboot
``

####Create a directory for the PXE boot menu we will create

```
sudo mkdir -p /var/lib/tftpboot/pxelinux.cfg
```

####Now lets create the PXE boot menu. 
You can grab the sample file here pxelinux.cfg.default.sample{insert link}.


##Mounting
Mount the Rocky 9.6 ISO:

```
mkdir -p /var/www/html/rocky
sudo mount -o loop /home/admin/Downloads/Rocky-9.6-x86_64-dvd.iso /var/www/html/rocky
```

####Make sure Apache (`httpd') is running. We enabled it in the system prep but double check.

```
sudo systemctl status httpd

```

---

##TFTP Boot Setup
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

```
sudo systemctl enable --now dnsmasq
```

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


