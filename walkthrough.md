# PXE Server Walkthrough – Rocky Linux 9.6 + Kickstart

This guide walks through setting up a PXE server that installs Rocky Linux 9.6 over the network with optional Kickstart automation.

--- 
## Customizing `.sample` Files

All PXE and Kickstart config examples are available as [sample files](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/tree/main/mirror-configs).

To make the `.sample` config files work for your environment:

- Replace every `x.x.x.x` with values matching your lab environment.
- For example, if your PXE server is `192.168.50.10`, replace the DHCP range and `dhcp-boot` lines accordingly.
- Make sure that your subnet mask, broadcast address, gateway, and DNS server match your actual network layout.

---

## System Prep
- PXE Server OS: Rocky Linux 9.6 DVD ISO
- Ensure internet access for installing packages.
- Baremetal machine to serve as the PXE client.
- If your environment is NAT, you will need to set up a bridge. 



#### Install Packages & start/enable them
```
sudo dnf update -y # applies updates to your system

sudo dnf install -y dnsmasq httpd syslinux tftp-server  # installs the necessary packages 

sudo dnf systemctl enable --now (dnsmasq,tftp,httpd)
```
---

## Copy contents of syslinux to /var/lib/tftpboot

 <pre> ```bash sudo cp -r /usr/share/syslinux/* /var/lib/tftpboot ``` </pre>

#### Create a directory for the PXE boot menu we will create

```
sudo mkdir -p /var/lib/tftpboot/pxelinux.cfg
```

#### Now lets create the PXE boot menu. 

- 📁 PXE Config Sample: [pxelinux.cfg.default.sample](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/blob/main/mirror-configs/pxelinux.cfg.default.sample)

```
sudo mkdir /var/lib/tftpboot/pxelinux.cfg
sudo vim /var/lib/tftpboot/pxelinux.cfg/default
```

---

## TFTP Boot Setup
Create the TFTP directory:

```
mkdir -p /var/lib/tftpboot/pxe-boot
```

Copy `vmlinuz` and `initrd.img` from the ISO's `/images/pxeboot/` to the TFTP folder.

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
**Sample: mirror-configs/dnsmasq.conf.sample**

Move existing dnsmasq configuration so we can create a clean version for our environment

```
sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.backup

sudo vim /etc/dnsmasq.conf
```
---

## Open Ports

``` 
sudo firewall-cmd --add-service=http --permanent   
sudo firewall-cmd --add-port=69/udp  --permanent  # TFTP port
sudo firewall-cmd --add-port=67/udp  --permanent  # DHCP port
sudo firewall-cmd --reload
```
### To check the services and ports added:
```
sudo firewall-cmd --list-all
```
---

## Boot the Client
- Set client to boot from network (PXE) via bios
- Confirm boot menu loads
- Select install option
- The install will begin


# Kickstart option

## If you want to add a kickstart file that automatically runs the install we pre-configurations, please continue following along

### Generate password hash. This is optional in your lab environment. I created two ks.cfg sample files, one is for plaintext while the other has the password hash.

```
openssl passwd -6
```

### Create the kickstart file, then copy and paste contents from mirror-configs/ks.cfg.sample

```
sudo vim /var/www/html/ks.cfg
```

#### If you want a headless install just change @^workstation-product-environment to @^minimal-environment in the ks.cfg.sample file

### Change selinux contexts to allow Apache(httpd) to serve the file

```
sudo chcon -t httpd_sys_content_t /var/www/html/ks.cfg
```

## Have questions?

Feel free to start a [Discussion](https://github.com/YOUR_USERNAME/YOUR_REPO/discussions) if you have questions, ideas, or need help.  

