# Set up Proxmox and Ubuntu 24.04 VM from scratch
This repository describes how to build a Proxmox server from scratch and create an Ubuntu VM with a passed-through USB drive. This system is the basis for a docker host to run the udms, Ultimate Docker Media Server, as described by Anand on his https://www.smarthomebeginner.com website.

Here is a summary of the contents with direct links to the markdown docs in this repository:

## [1 - Proxmox Setup](1%20-%20Proxmox%20Setup.md)
  - [Make a bootable USB with OS images and tools using Ventoy](1%20-%20Proxmox%20Setup.md/#make-a-bootable-usb-with-os-images-and-tools-using-ventoy)
  - [Proxmox post-install setup](1%20-%20Proxmox%20Setup.md/#proxmox-post-install-setup)
  - [Set up the Proxmox terminal](1%20-%20Proxmox%20Setup.md/#set-up-the-proxmox-terminal)
  - [Configure Proxmox alerts](https://github.com/kurtshuler/proxmox-ubuntu-server/blob/main/1%20-%20Proxmox%20Setup.md#configure-proxmox-alerts)
## [2 - Ubuntu VM Setup](2%20-%20Ubuntu%20VM%20Setup.md)
  - [Run Tteck Ubuntu 24.04 script at https://helper-scripts.com/scripts?id=Ubuntu+24.04](2%20-%20Ubuntu%20VM%20Setup.md#run-tteck-ubuntu-2404-script-at-httpshelper-scriptscomscriptsidubuntu2404)
## [3 - USB Drive Passthrough to Proxmox VM](3%20-%20USB%20%20Drive%20Passthrough%20to%20Proxmox%20VM.md)
  - [Within Proxmox Node: Pass through from Proxmox node to VM](3%20-%20USB%20%20Drive%20Passthrough%20to%20Proxmox%20VM.md#within-proxmox-node-psss-through-from-proxmox-node-to-vm)
  - [Within the VM: Set up the mount point and mount the USB drive partition](3%20-%20USB%20%20Drive%20Passthrough%20to%20Proxmox%20VM.md#within-the-vm-set-up-the-mount-point-and-ount-the-usb-drive-partition)
  - [Within the VM: Set up fstab so passed-through drive will mount on boot](3%20-%20USB%20%20Drive%20Passthrough%20to%20Proxmox%20VM.md#within-the-vm--set-up-fstab-so-passed-through-drive-will-mount-on-boot)

## [4 - Ubuntu OS setup](4%20-%20Ubuntu%20OS%20Setup.md)
  - [Initial Setup](4%20-%20Ubuntu%20OS%20Setup.md#initial-setup)
  - [Change SSH port to 2053](4%20-%20Ubuntu%20OS%20Setup.md#change-ssh-port-to-2053)
  - [Set up the Ubuntu terminal](4%20-%20Ubuntu%20OS%20Setup.md#set-up-the-ubuntu-terminal)
  - [Make server tweaks](4%20-%20Ubuntu%20OS%20Setup.md#make-server-tweaks)
  - [Enable UFW firewall](4%20-%20Ubuntu%20OS%20Setup.md#enable-ufw-firewall)
