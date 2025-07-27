# Setting Up a Network Bridge (br0) for PXE Booting

# Step 1: Create the Bridge Interface

```
nmcli connection add type bridge autoconnect yes con-name br0 ifname br0
```

This creates a bridge named br0 and sets it to autoconnect on boot.

# Step 2: Assign a Static IP to the Bridge

#### Replace the IP settings below with values that match your lab subnet.

Use ip a and ip route | grep default to help find these values.

- Do not assign a the same IP to br0 as your physical NIC.
- Set the static IP only on br0
- br0 will become the new interface that routes traffic. 

```
nmcli connection modify br0 ipv4.addresses x.x.x.x/24
nmcli connection modify br0 ipv4.gateway x.x.x.1
nmcli connection modify br0 ipv4.dns 1.1.1.1
nmcli connection modify br0 ipv4.method manual
```

## Step 3: Attach Your Physical NIC to the Bridge

Replace enp1s0 with the name of your actual interface. You can run nmcli device status or ip a to find it.

```
nmcli connection add type bridge-slave autoconnect yes con-name br0-slave ifname enp1s0 master br0
```

> After attaching your NIC, you can run `ip a` to confirm it's now part of the bridge.
> You’ll see `master br0` next to the interface, showing it's successfully bridged.

![NIC showing master br0](./images/ip-a-bridge-master.png)


## Step 4: Bring Up the Bridge

```
nmcli connection up br0
```

## Step 5: Verify the Bridge is Active

```
nmcli con show
```


## When your PXE client boots, we can test connectivity with tcpdump

To verify that PXE clients can reach the PXE server over the bridge, run this command:

```
sudo tcpdump -i br0 port 67 or port 69 or port 4011
```

This listens for:

- DHCP (port 67)

- TFTP (port 69)

- PXE bootloader (port 4011)

You should see packets like DHCPDISCOVER and DHCPOFFER when a PXE client boots.


### Troubleshooting Note: PXE Boot Looped Back to Itself

If your PXE client appears to request TFTP files but the boot process hangs or loops, check if your PXE server is accidentally serving TFTP requests to itself.

This happened in our lab setup when `br0` was created but not properly attached to the physical NIC (`eno1`). As a result:

-   PXE clients sent DHCP/TFTP requests

-   But the PXE server's TFTP service looped those requests back into itself

-   It tried to fetch `/pxelinux.0` from its own TFTP service, as if it were the PXE client

* * * * *

### Fix

Make sure your physical NIC is correctly added as a bridge slave to `br0`. For example:

```
nmcli connection add type bridge-slave autoconnect yes con-name br0-slave ifname eno1 master br0
```

Use `ip a` and confirm that:

-   `eno1` shows `master br0`

-   `br0` has the static IP assigned

-   PXE traffic is visible using:


Then bring everything up:

```
nmcli connection up br0
```

Once fixed, PXE clients will successfully reach the server, and TFTP will serve files properly from `/var/lib/tftpboot`.
