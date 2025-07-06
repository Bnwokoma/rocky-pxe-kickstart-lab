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

## Requirements needed
- Download the Rocky Linux 9.6 DVD ISO from official Rocky Linux site
- Ensure internet access for installing packages.
- Baremetal machine to serve as the PXE client.
- If your environment is NAT based, you will need to set up a bridge [here](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/blob/main/network-bridge/setup-bridge.md)

### Directory Layouts

You can view the full directory structures here of tftp boot and http:

- [Tree md](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/blob/main/directory_layout/tree-views.md)
- [`tftpboot.tree.txt`](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/blob/main/directory_layout/tftpboot.tree.txt) – PXE boot file layout
- [`http-root.tree.txt`](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/blob/main/directory_layout/http-root.tree.txt) – HTTP Kickstart and mirror layout

---

# Lets Begin! =)

---
## 1. Install Packages & start/enable them
```
sudo dnf update -y # applies updates to your system

sudo dnf install -y dnsmasq httpd syslinux tftp-server  # installs the necessary packages 

sudo dnf systemctl enable --now (dnsmasq,tftp,httpd)
```
---

## 2. Copy Syslinux Boot Files to /var/lib/tftpboot

 <pre> ``` sudo cp -r /usr/share/syslinux/* /var/lib/tftpboot ``` </pre>

#### 3. Create a directory for PXE Boot Menu

```
sudo mkdir -p /var/lib/tftpboot/pxelinux.cfg
```

#### Now lets create the PXE boot menu. 

- 📁 PXE Config Sample: [pxelinux.cfg.default.sample](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/blob/main/mirror-configs/pxelinux.cfg.default.sample)

```
sudo vim /var/lib/tftpboot/pxelinux.cfg/default
```

---

## 4. TFTP Boot Setup
Create the TFTP directory:

```
mkdir -p /var/lib/tftpboot/pxe-boot
```

Copy `vmlinuz` and `initrd.img` from the ISO's `/images/pxeboot/` to the TFTP folder.

```
 sudo cp /var/www/html/rocky/images/pxeboot/{vmlinuz,initrd.img} /var/lib/tftpboot/pxe-boot/
```

## 5. Mounting
**Mount the Rocky 9.6 ISO:**

```
sudo mkdir -p /var/www/html/rocky
sudo mount -o loop ~/Downloads/Rocky-9.6-x86_64-dvd.iso /mnt #Use the path to wherever your Rocky iso is located
sudo cp -av /mnt/* /var/www/html/rocky/
```

### To check that the files are being served over http
```
curl http://<pxeserver-ip>/rocky/.treeinfo

or

curl http://localhost/rocky/.treeinfo
```
---

## 6. Configure dnsmasq
Use this sample: [mirror-configs/dnsmasq.conf.sample](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/blob/main/mirror-configs/dnsmasq.conf.sample)

Move the existing dnsmasq configuration so we can create a clean version for our environment

```
sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.backup # moves existing conf so we can create one for our environment

sudo vim /etc/dnsmasq.conf
```
---

## 7. Open Ports

``` 
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --add-service=dns --permanent 
sudo firewall-cmd --add-service=dhcp --permanent  
sudo firewall-cmd --add-port=69/udp --permanent  # TFTP port
sudo firewall-cmd --add-port=4011/udp --permanent  # PXE
sudo firewall-cmd --reload
```
### To check the services and ports added:
```
sudo firewall-cmd --list-all
```
---

## 8. Boot the Client
- Set client to boot from network (PXE) via Bios
- Confirm boot menu loads
- Select install option
- The install will begin

When installation is complete, boot back into bios and change the boot order back to local/internal HDD

Rocky Linux is now the OS of your machine!


# Kickstart option

If you want to add a kickstart file that automatically runs the install we pre-configurations, please continue following along

### Generate password hash. This is optional in your lab environment. I created two ks.cfg sample files, one is for plaintext while the other has the password hash.

- [Sample Kickstart file (plaintext)](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/blob/main/mirror-configs/ks.cfg.sample.txt)  
- [Sample Kickstart file (with hashed passwords)](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/blob/main/mirror-configs/ks.cfg.sample.hashed)

If you are using the Kickstart file with hashed passwords, follow the command below

```
openssl passwd -6
```

### Create the kickstart file, using sample from one of the options above

```
sudo vim /var/www/html/ks.cfg
```

### Update PXE boot configuration to add a path to ks.cfg
- 📁 PXE Config Sample with Kickstart: [here](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/blob/main/mirror-configs/kickstart.pxe.cfg.sample)



### Change selinux contexts to allow Apache(httpd) to serve the file

```
sudo chcon -t httpd_sys_content_t /var/www/html/ks.cfg
```

## Have questions?

If you have any questions, comments or suggestions, please feel free to reach out to me on LinkedIn or add a comment in [Discussions](https://github.com/Bnwokoma/rocky-pxe-kickstart-lab/discussions/1)

