# edison-tools
Scripts, Tools and Commands For Development On The Intel Edison.

------------------------
## Setup
### Fixing "Unable To Resolve Host Ubilinux"
Alias localhost to ubilinux, replace /etc/hosts with the following
````
#/etc/hosts
127.0.0.1       localhost       ubilinux
::1             localhost ip6-localhost ip6-loopback
fe00::0         ip6-localnet
ff00::0         ip6-mcastprefix
ff02::1         ip6-allnodes
ff02::2         ip6-allrouters
````
### Install Sudo
apt-get install sudo
### Making More Room In Root (Symlink, Not Repartition)
Moving /usr Folder
```
$ mkdir -p /home/usr
$ mv /usr/* /home/usr/
$ rm -r /usr
$ ln -s /home/usr/ /usr
```
------------------------
## Scripts
### USB Internet Bridge:
Host Script (Computer With Internet)
```bash
#!/bin/bash
# ensure running as root
if [ "$(id -u)" != "0" ]; then
  exec sudo "$0" "$@"
fi
ifconfig usb0 192.168.2.14
#wlan0 is the network interface with internet on the main computer
iptables --table nat --append POSTROUTING --out-interface wlan0 -j MASQUERADE
#usb0 is network interface Edison is connected on
iptables --append FORWARD --in-interface usb0 -j ACCEPT k
echo 1 > /proc/sys/net/ipv4/ip_forward
```
Edison Script (Setup, Run Only Once)
```bash
#!/bin/bash
#usb0 is network interface Edison is connected on
ifconfig usb0 192.168.2.15
route add default gw 192.168.2.14
echo "nameserver 8.8.8.8" > '/etc/resolv.conf'
```
Edison Script (Run Everytime)
```bash
#!/bin/bash
#usb0 is network interface Edison is connected on
ifconfig usb0 192.168.2.15
route add default gw 192.168.2.14
```
### Connect To Unsecured Wireless
```bash
#!/bin/bash
ifup wlan0 #enables wlan0
iwlist wlan0 scanning essid robotics  #scans and finds network
iwconfig wlan0 essid robotics	#configures and connects to network
```
------------------------
## Commands
### Edison Serial Connection
```
sudo screen /dev/ttyUSB0 115200
```
