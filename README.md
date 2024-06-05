# Proxmox and Ubuntu VM Installation
*Contents*
- [Proxmox and Ubuntu VM Installation](#proxmox-and-ubuntu-vm-installation)
- [Proxmox Installation](#proxmox-installation)
  - [Making a bootable USB with OS images using Ventoy](#making-a-bootable-usb-with-os-images-using-ventoy)
  - [Proxmox After Install Setup](#proxmox-after-install-setup)
  - [Proxmox Terminal Setup](#proxmox-terminal-setup)

# Proxmox Installation
## Making a bootable USB with OS images using Ventoy
1) Latest Ventoy installers at https://sourceforge.net/projects/ventoy/files/ Windows only - use Parallels Windows or Linux
2) ISO images at:
   | ISO | URL |
   |-----|-----|
   | System Rescue ISO | https://www.system-rescue.org/Download/ |
   | Proxmox ISO | https://www.proxmox.com/en/downloads |
   | Ubuntu SERVER Cloud-Init ISO | https://cloud-images.ubuntu.com/noble/current/ |
   | Ubuntu DESKTOP ISO | https://ubuntu.com/download/desktop/ |

## Proxmox After Install Setup
1) Check that SSH is running
   ```
   systemctl status ssh.service
   ```
2) Run TTeck Proxmox VE Helper-Scripts at https://helper-scripts.com/scripts?id=Proxmox+VE+Post+Install
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
   ```
   bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/microcode.sh)"
   ```
## Proxmox Terminal Setup
5) Install neofetch
   ```
   apt-get install neofetch
   ```
6) Set up Proxmox terminal
    - Copy-and-paste [pve .bashrc] (files/Proxmox files/.bashrc "Proxmox .bashrc")
    - Reload .bashrc
      ```
      source ~/.bashrc
      ```
7) Install iTerm shell integration: iTerm2 → Iterm Shell Integration

