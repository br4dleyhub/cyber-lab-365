# Day 1: Static IP Configuration

## 

## 

## \## Objective

## Today, we set up our Cyber Lab 365 environment by installing VMs and configuring static IP addresses. This ensures consistent networking for testing and cybersecurity exercises.

## 

## ---

## 

## \## Table of Contents

## 1\. \[VM Installation](#vm-installation)

## 2\. \[Ping Tests](#ping-tests)

## 3\. \[Static IP Configuration](#static-ip-configuration)

## &nbsp;  - \[Windows VM](#windows-vm)

## &nbsp;  - \[Kali Linux VM](#kali-linux-vm)

## &nbsp;  - \[Ubuntu VM](#ubuntu-vm)

## 4\. \[IP Address Table](#ip-address-table)

## 5\. \[Screenshots](#screenshots)

## 6\. \[Commands Used](#commands-used)

## 7\. \[Notes](#notes)

## 

## ---

## 

## \## VM Installation

## \- \*\*Virtualization Software:\*\* VMware Workstation / VirtualBox

## \- \*\*VMs Installed:\*\* Windows 10, Kali Linux, Ubuntu 22.04

## \- \*\*VM Settings:\*\*

## &nbsp; - Windows 10: 4 GB RAM, 2 CPUs, 60 GB storage

## &nbsp; - Kali Linux: 2 GB RAM, 2 CPUs, 20 GB storage

## &nbsp; - Ubuntu 22.04: 2 GB RAM, 2 CPUs, 20 GB storage

## \- \*\*Network Adapter:\*\* Initially NAT → changed to Bridged for static IPs

## \- \*\*Notes:\*\* Internet connectivity tested via NAT before switching to static IP

## 

## ---

## 

## \## Ping Tests

## \### Windows

## ```cmd

## ping 8.8.8.8

## ping google.com

## Linux (Kali / Ubuntu)

## bash

## Copier le code

## ping -c 4 8.8.8.8

## ping -c 4 google.com

## ✅ Ping successful confirms network connectivity.

## 

## Static IP Configuration

## Windows VM

## Open Control Panel → Network and Internet → Network Connections.

## 

## Right-click your adapter → Properties → Internet Protocol Version 4 (TCP/IPv4) → Properties.

## 

## Select Use the following IP address:

## 

## IP Address: 192.168.1.100

## 

## Subnet Mask: 255.255.255.0

## 

## Default Gateway: 192.168.1.1

## 

## DNS Servers: 8.8.8.8, 8.8.4.4

## 

## Save settings and test:

## 

## cmd

## Copier le code

## ipconfig

## ping 192.168.1.1

## ping 8.8.8.8

## Kali Linux VM

## Edit the network interfaces file:

## 

## bash

## Copier le code

## sudo nano /etc/network/interfaces

## Add:

## 

## text

## Copier le code

## auto eth0

## iface eth0 inet static

## address 192.168.1.101

## netmask 255.255.255.0

## gateway 192.168.1.1

## dns-nameservers 8.8.8.8 8.8.4.4

## Restart networking:

## 

## bash

## Copier le code

## sudo systemctl restart networking

## Test connectivity:

## 

## bash

## Copier le code

## ifconfig eth0

## ping 192.168.1.1

## ping 8.8.8.8

## Ubuntu VM

## Open Netplan configuration:

## 

## bash

## Copier le code

## sudo nano /etc/netplan/00-installer-config.yaml

## Update with:

## 

## yaml

## Copier le code

## network:

## &nbsp; version: 2

## &nbsp; ethernets:

## &nbsp;   ens33:

## &nbsp;     dhcp4: no

## &nbsp;     addresses: \[192.168.1.102/24]

## &nbsp;     gateway4: 192.168.1.1

## &nbsp;     nameservers:

## &nbsp;         addresses: \[8.8.8.8,8.8.4.4]

## Apply config:

## 

## bash

## Copier le code

## sudo netplan apply

## Test:

## 

## bash

## Copier le code

## ip a

## ping 192.168.1.1

## ping google.com

## IP Address Table

## VM	IP Address	Subnet Mask	Gateway	DNS Servers

## Windows	192.168.1.100	255.255.255.0	192.168.1.1	8.8.8.8, 8.8.4.4

## Kali	192.168.1.101	255.255.255.0	192.168.1.1	8.8.8.8, 8.8.4.4

## Ubuntu	192.168.1.102	255.255.255.0	192.168.1.1	8.8.8.8, 8.8.4.4

## 

## Screenshots

## Add screenshots in a /screenshots folder and link here:

## 

## Windows IP config: screenshots/windows-ip.png

## 

## Kali config: screenshots/kali-ip.png

## 

## Ubuntu Netplan: screenshots/ubuntu-ip.png

## 

## Ping tests: screenshots/ping-test.png

## 

## Commands Used



## Windows - cmd

## ipconfig

## ping <IP>



## Linux - bash

## ifconfig

## ip a

## ping -c 4 <IP>

## sudo nano /etc/network/interfaces

## sudo nano /etc/netplan/00-installer-config.yaml

## sudo netplan apply

## sudo systemctl restart networking





## Notes

## Backup network config files before making changes.

## 

## Use a consistent IP scheme to avoid conflicts.

## 

## Document screenshots for future reference.

## 

## Observe differences between Linux distributions when configuring static IPs.

## 



