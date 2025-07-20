# Proxmox ADS Passthough and permissioning
## Find usb stick
Plug the RTL-SDR USB dongle into the Proxmox host, find its bus and device ID and set its permissions
````
root@proxmox:~# lsusb
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 007: ID 0bda:2838 Realtek Semiconductor Corp. RTL2838 DVB-T
````
The above indicates Bus 001 and Device 007 for my dongle.
## Set and check permissions on this bus and device
Adjusting for your bus/device values.
````
root@proxmox:~# chmod 0666 /dev/bus/usb/001/007
Check permissions of this bus and device:
root@proxmox:~# ls -l /dev/bus/usb/001/007
crw-rw-rw- 1 root root 189, 6 Aug  8 21:41 /dev/bus/usb/001/007
````
## Adjust LXC configuration - Pass through the dongle from the host to the container.
Adjusting for your container id values.
````
root@proxmox:~# nano /etc/pve/lxc/156.conf
Add the following two lines to the configuration file:
lxc.cgroup2.devices.allow: c 189:* rwm
lxc.mount.entry: /dev/bus/usb/001/007 dev/bus/usb/001/007 none bind,optional,create=file
````
The 189 should match the 189 when checking the permissions of the bus and device previously. Adjust the bus and device values to suit your setup.

## LXC container conf example
Below is my full LXC configuration file. Note that I have keyctl=1,nesting=1 to allow me to install and use Docker within this container, as I use the ultrafeeder Docker image from ADS-B Enthusiasts.
````
root@proxmox:~# cat /etc/pve/lxc/156.conf
arch: amd64
cores: 1
features: keyctl=1,nesting=1
hostname: sdr
memory: 512
net0: name=eth0,bridge=vmbr0,firewall=1,gw=192.168.1.1,hwaddr=76:E6:C9:E1:E2:D4,ip=192.168.1.156/24,type=veth
ostype: debian
rootfs: local-lvm:vm-156-disk-0,size=4G
swap: 512
unprivileged: 1
lxc.cgroup2.devices.allow: c 189:* rwm
lxc.mount.entry: /dev/bus/usb/001/007 dev/bus/usb/001/007 none bind,optional,create=file
````