# PXE Directory Trees

This folder contains text-based `tree` output of key directories used in the PXE + Kickstart lab project. These views help you understand how files should be organized on the TFTP and HTTP servers for a successful network installation.

---

## `tftpboot.tree.txt`

This shows the structure of the **TFTP server directory** at `/var/lib/tftpboot`.

It includes:

- Bootloader files like `pxelinux.0`, `ldlinux.c32`, and optional menu modules
- The `pxelinux.cfg/` directory with the `default` PXE config
- The `images/rocky/` directory holding `vmlinuz` and `initrd.img`

**Used by PXE clients to download boot files over the network.**

---

## `http-root.tree.txt`

This shows the layout of the **HTTP server root** at `/var/www/html`.

It includes:

- The Kickstart file (`ks.cfg`) hosted over HTTP
- The Rocky Linux mirror content (`/rocky/BaseOS`, `/rocky/AppStream`, `.treeinfo`)

**Used by Anaconda (the installer) to load the Kickstart and fetch installation packages.**

---

## How to Generate These

If you want to regenerate or verify the structure:

```
sudo tree -L 3 /var/lib/tftpboot > tftpboot.tree.txt
sudo tree -L 3 /var/www/html > http-root.tree.txt
```
