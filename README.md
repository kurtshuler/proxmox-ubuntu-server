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
   ```
   systemctl status ssh.service
   ```
2) Run TTeck Proxmox VE Helper-Scripts at https://helper-scripts.com/scripts?id=Proxmox+VE+Post+Install
   > Run Tteck scripts from Proxmox GUI shell, not SSH!
   ```
   bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/post-pve-install.sh)"
   ```
3) Hardware Passthrough (iGPU):
   - Review iKoolcore R2 wiki at https://wiki.ikoolcore.com/#/R2/en/FAQs/VM
   - Add hardware passthrough https://github.com/KoolCore/Proxmox_VE_Status
   - Read https://www.derekseaman.com/2023/04/proxmox-plex-lxc-with-alder-lake-transcoding.html
   - Verify that hardware passthrough is working: https://pve.proxmox.com/wiki/PCI_Passthrough
      - Verify IOMMU is enabled:
        ```
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
4) Run TTeck Proxmox VE Processor Microcode script at https://helper-scripts.com/scripts?id=Proxmox+VE+Processor+Microcode
   > Run Tteck scripts from Proxmox GUI shell, not SSH!
   ```
   bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/microcode.sh)"
   ```
### Proxmox terminal setup
1) Install neofetch
   ```
   apt-get install neofetch
   ```
2) Set up the Proxmox terminal
    - Copy-and-paste [Proxmox .bashrc](https://github.com/kurtshuler/proxmox-ubuntu-server/blob/71f390c3b2396e606b1f151ae2aeec1cd3021a39/Proxmox%20files/.bashrc)
    - Reload .bashrc
      ```
      source ~/.bashrc
      ```
7) Install iTerm shell integration: *iTerm2 → Iterm Shell Integration*

## Ubuntu installation within Proxmox 
### Install Ubuntu using Tteck sctipt
1) Run Tteck Ubuntu 24.04 script at https://helper-scripts.com/scripts?id=Ubuntu+24.04
    > Run TTeck scripts from Proxmox GUI shell, not SSH!
    ```
    bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/vm/ubuntu2404-vm.sh)"

    ```
2) Setup Cloud-Int on Proxmox
Info at https://github.com/tteck/Proxmox/discussions/2072 
### Follow Anand's OS setup guide
Go to [Ultimate Docker Server: Getting Started with OS Preparation Part 1](https://www.smarthomebeginner.com/ultimate-docker-server-1-os-preparation/). 
>The steps below are adapted from this guide to match Ubuntu 24.04 requirements.
1) Create new user and give sudo priveleges
   ```
   adduser kurtshuler
   adduser kurtshuler sudo
   ```
2) Update Ubuntu OS
   ```
   sudo apt update
   sudo apt upgrade
   ```
3) Install basic/required packages
   ```
   sudo apt install ca-certificates curl gnupg lsb-release ntp htop zip unzip gnupg apt-transport-https ca-certificates net-tools ncdu apache2-utils git neofetch
   ```

4) Change SSH port to 2053
To change the port of the SSH server, the systemd configuration for ssh.socket must be changed or supplemented. The configuration adjustment is made by creating a *.conf file in the directory /etc/systemd/system/ssh.socket.d/.
   - Create conf file to extend the default config:
     ```
     nano /etc/systemd/system/ssh.socket.d/override.conf
     ```
   - Add the following lines:
     ```
     [Socket]
     ListenStream=
     ListenStream=2053
     ```
     > **Note:** The blank line *ListenStream=* is required to ensure that port 22 is no longer used. Without this line, the SSH server would then be accessible via port 22 (default) *and* 2053.
   - Restart the socket:
     ```
     systemctl restart ssh.socket 
     ```

