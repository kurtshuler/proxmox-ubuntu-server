# Set up Proxmox and Ubuntu 24.04 VM from scratch
This repository describes how to build a Proxmox server from scratch and create an Ubuntu VM with virtual SCSI drive and a passed-through USB drive. This system is the basis for a docker host to run the udms, Ultimate Docker Media Server, as described by Anand on his https://www.smarthomebeginner.com website.

### [1 - Proxmox Setup](1%20-%20Proxmox%20Setup.md)
  - Make a bootable USB with OS images and tools using Ventoy
  - Proxmox post-install setup
  - Set up the Proxmox terminal
  - Configure Proxmox alerts
### [2 - Ubuntu VM installation within Proxmox](2%20-%20Ubuntu%20VM%20Installation%20within%20Proxmox.md)
  - Run Tteck Ubuntu 24.04 script
  - Setup Cloud-Init on Proxmox
  - Start the VM
### [3 - iGPU Hardware Passthrough Setup](3%20-%20iGPU%20Hardware%20Passthrough%20Setup.md)
  - Within the Proxmox Host
  - In Proxmox GUI, add GPU to VM PCI devices
### [4 - Create Proxmox Virtual Disk for Ubuntu VM](4%20-%20Create%20Proxmox%20Virtual%20Disk%20for%20Ubuntu%20VM.md)
  - Within the Proxmox GUI VM setup page
  - Within the VM shell
### [5 - USB Drive Passthrough to Ubuntu VM in Proxmox](5%20-%20USB%20Drive%20Hardware%20Passthrough%20Setup.md)
  - Within Proxmox Node: Pass through from Proxmox node to VM
  - Within Proxmox GUI VM setup page: Verify your drive is passed through and turn off backup
  - Within the running Ubuntu VM: Mount the passed-through drive and add it to fstab

### [6 - Ubuntu OS setup](6%20-%20Ubuntu%20OS%20Setup.md)
  - Initial Setup
  - Change SSH port to 2053
  - Set up the Ubuntu terminal - OMB, aliases and iTerm2
  - Make server tweaks
  - Enable UFW firewall
