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


#### Install Packages & start/enable them
```
sudo dnf update -y # applies updates to your system

sudo dnf install -y dnsmasq httpd syslinux tftp-server  # installs the necessary packages 

sudo dnf systemctl enable --now (dnsmasq,tftp,httpd)
```

#### Create a bridge interface named (`br0`) using `nmcli`:
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


sudo cp -r /usr/share/syslinux/* /var/lib/tftpboot


#### Create a directory for the PXE boot menu we will create

```
sudo mkdir -p /var/lib/tftpboot/pxelinux.cfg
```

#### Now lets create the PXE boot menu. 
```
sudo mkdir /var/lib/tftpboot/pxelinux.cfg
sudo vim /var/lib/tftpboot/pxelinux.cfg/default
```
You can grab the sample file here pxelinux.cfg.default.sample{insert link}.


---

## TFTP Boot Setup
Create the TFTP directory:

```
mkdir -p /var/lib/tftpboot/pxe-boot
```

**Copy `vmlinuz` and `initrd.img` from the ISO's `/images/pxeboot/` to the TFTP folder.**

```
 sudo cp /var/www/html/rocky/images/pxeboot/{vmlinuz,initrd.img} /var/lib/tftpboot/pxe-boot/
```

## Mounting
**Mount the Rocky 9.6 ISO:**

```
sudo mkdir -p /var/www/html/rocky
sudo mount -o loop ~/Downloads/Rocky-9.6-x86_64-dvd.iso /mnt
sudo cp -av /mnt/* /var/www/html/rocky/
```

## To check the files are being served over http
```
curl http://pxeserver-ip/rocky/.treeinfo

or

curl http://localhost/rocky/.treeinfo
```
---

## Configure dnsmasq
**Sample: `mirror-configs/dnsmasq.conf.sample`**

Move existing dnsmasq configuration so we can create a clean version for our environment

```
sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.backup

sudo vim /etc/dnsmasq.conf
```
---

## Open Ports

``` 
sudo firewall-cmd --add-service=http --permanent  # 
sudo firewall-cmd --add-port=69/udp  --permanent  # TFTP port
sudo firewall-cmd --add-port=67/udp  --permanent  # DHCP port
sudo firewall-cmd --reload
```
### To check the services and ports added:
```
sudo firewall-cmd --list-all
```

## Kickstart Setup
Sample config: `mirror-configs/ks.cfg.sample`

Place it in the HTTP root and reference it in the PXE menu:

`inst.ks=http://<server-ip>/ks.cfg`

---

## Boot the Client
- Set client to boot from network (PXE)
- Confirm boot menu loads
- Select install option or let it auto-start
- Watch Kickstart begin automatically


Ready to start? Enjoy & follow along!

## Have questions?

Feel free to start a [Discussion](https://github.com/YOUR_USERNAME/YOUR_REPO/discussions) if you have questions, ideas, or need help.  

