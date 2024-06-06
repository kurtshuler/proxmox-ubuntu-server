# Set up Proxmox and Ubuntu 24.04 VM from scratch
## Make a bootable USB with OS images and tools using Ventoy
1) Latest Ventoy installers at https://sourceforge.net/projects/ventoy/files/ Windows only - use Parallels Windows or Linux
2) ISO images at:
   | ISO | URL |
   |-----|-----|
   | System Rescue ISO | https://www.system-rescue.org/Download/ |
   | Proxmox ISO | https://www.proxmox.com/en/downloads |
   | Ubuntu SERVER Cloud-Init ISO | https://cloud-images.ubuntu.com/noble/current/ |
   | Ubuntu DESKTOP ISO | https://ubuntu.com/download/desktop/ |
## Proxmox installation
### Proxmox post-install setup
1) Check that SSH is running
```shell-script
systemctl status ssh.service
```
2) Run TTeck Proxmox VE Helper-Scripts at https://helper-scripts.com/scripts?id=Proxmox+VE+Post+Install
> Run Tteck scripts from Proxmox GUI shell, not SSH!
```shell-script
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/post-pve-install.sh)"
```
3) Set up Hardware Passthrough (iGPU):
- Review iKoolcore R2 wiki at https://wiki.ikoolcore.com/#/R2/en/FAQs/VM
- Add hardware passthrough https://github.com/KoolCore/Proxmox_VE_Status
- Read https://www.derekseaman.com/2023/04/proxmox-plex-lxc-with-alder-lake-transcoding.html
4) Verify that hardware passthrough is working
Source: https://pve.proxmox.com/wiki/PCI_Passthrough
- Verify IOMMU is enabled:
```shell-script
dmesg | grep -e DMAR -e IOMMU
```
- Verify IOMMU interrupt remapping is enabled:

```
dmesg | grep 'remapping'
```
- Verify that Proxmox recognizes the GPU:
```
lspci -v | grep -e VGA
```
- Get more details about that VGA adapter:
```
lspci -v -s 00:02.0
```
5) Run TTeck Proxmox VE Processor Microcode script at https://helper-scripts.com/scripts?id=Proxmox+VE+Processor+Microcode
> Run Tteck scripts from Proxmox GUI shell, not SSH!
```
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/microcode.sh)"
```
### Proxmox terminal setup
2) Install neofetch
```
apt-get install neofetch
```
3) Set up the Proxmox terminal
- Copy-and-paste [Proxmox .bashrc](/Proxmox%20files/.bashrc)
- Reload .bashrc
```
source ~/.bashrc
```
4) Install iTerm shell integration: *iTerm2 → Iterm Shell Integration*
## Ubuntu installation within Proxmox 
### Install Ubuntu using Tteck script
1) Run Tteck Ubuntu 24.04 script at https://helper-scripts.com/scripts?id=Ubuntu+24.04
> Run TTeck scripts from Proxmox GUI shell, not SSH!
```
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/vm/ubuntu2404-vm.sh)"
```
5) Setup Cloud-Int on Proxmox

Info at https://github.com/tteck/Proxmox/discussions/2072 

### Ubuntu OS setup
    
This is based on Anand's OS setup guide at [Ultimate Docker Server: Getting Started with OS Preparation Part 1](https://www.smarthomebeginner.com/ultimate-docker-server-1-os-preparation/). 
>The steps below are adapted from this guide to match Ubuntu 24.04 requirements.
1) Create new user and give sudo priveleges
```
adduser kurt
adduser kurt sudo
```
2) Update Ubuntu OS
```
sudo apt update
sudo apt upgrade
```
3) Install basic/required packages
```
sudo apt install ca-certificates curl gnupg lsb-release ntp htop zip unzip gnupg apt-transport-https ca-certificates net-tools ncdu apache2-utils git neofetch vsftpd
```

4) Change SSH port to 2053

    To change the port of the SSH server, the systemd configuration for ssh.socket must be changed or supplemented. The configuration adjustment is made by creating a *.conf file in the directory /etc/systemd/system/ssh.socket.d/.
- Edit conf file to extend the default config:
```
nano /etc/systemd/system/ssh.socket.d/override.conf
```
- Edit the following lines:
> **Note 1:** These instructions are different than what is on Anand's site due to changes in Ubuntu 24.04 SSH.
> **Note 2:** The blank line *ListenStream=* is required to ensure that port 22 is no longer used. Without this line, the SSH server would then be accessible via port 22 (default) *and* 2053.
```
[Socket]
ListenStream=
ListenStream=2053
```
- Restart SSH:
```
systemctl daemon-reload
sudo systemctl restart ssh  
```
- Ensure that port 22 is *closed* and port 2053 is *open*:
```
netstat -ant |grep 2053
```
```
sudo lsof -nP -iTCP -sTCP:LISTEN
```
```
sudo netstat -tunpl
```
- SSH *root* check: Log into Ubuntu server using new SSH port 2053
```
ssh root@192.168.1.100 -p 2053
```
- SSH *user* check: Log into Ubuntu server using new SSH port 2053
```
ssh kurt@192.168.1.100 -p 2053
```
5) Set up the Ubuntu terminal
- Copy-and-paste [Ubuntu .bashrc](/Ubuntu%20files/.bashrc)
- Reload .bashrc
```
source ~/.bashrc
```
4) Install iTerm shell integration: *iTerm2 → Iterm Shell Integration*
5) Make server tweaks
   - 
