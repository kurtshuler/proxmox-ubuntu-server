# 1. Proxmox and Ubuntu VM Installation 
- [1. Proxmox and Ubuntu VM Installation](#1-proxmox-and-ubuntu-vm-installation)
  - [1.1. Proxmox Installation](#11-proxmox-installation)
    - [1.1.1. Making a bootable USB with OS images using Ventoy](#111-making-a-bootable-usb-with-os-images-using-ventoy)
      - [1.1.1.1. Latest Ventoy installers at https://sourceforge.net/projects/ventoy/files/ Windows only - use Parallels Windows or Linux](#1111-latest-ventoy-installers-at-httpssourceforgenetprojectsventoyfiles-windows-only---use-parallels-windows-or-linux)
      - [1.1.1.2. ISO images at:](#1112-iso-images-at)
    - [1.1.2. After Install basic Proxmox actions](#112-after-install-basic-proxmox-actions)
      - [1.1.2.1. Check SSH is running](#1121-check-ssh-is-running)
    - [1.1.3. Run TTeck Proxmox VE Helper-Scripts at https://helper-scripts.com/scripts?id=Proxmox+VE+Post+Install](#113-run-tteck-proxmox-ve-helper-scripts-at-httpshelper-scriptscomscriptsidproxmoxvepostinstall)
    - [1.1.4. Hardware Passthrough (iGPU):](#114-hardware-passthrough-igpu)
    - [1.1.5. Run TTeck Proxmox VE Processor Microcode script at https://helper-scripts.com/scripts?id=Proxmox+VE+Processor+Microcode](#115-run-tteck-proxmox-ve-processor-microcode-script-at-httpshelper-scriptscomscriptsidproxmoxveprocessormicrocode)
    - [1.1.6. Install neofetch](#116-install-neofetch)
    - [1.1.7. Set up pve terminal](#117-set-up-pve-terminal)
    - [1.1.8. Install iTerm shell integration: iTerm2 → Iterm Shell Integration](#118-install-iterm-shell-integration-iterm2--iterm-shell-integration)

## 1.1. Proxmox Installation
### 1.1.1. Making a bootable USB with OS images using Ventoy
#### 1.1.1.1. Latest Ventoy installers at https://sourceforge.net/projects/ventoy/files/ Windows only - use Parallels Windows or Linux
#### 1.1.1.2. ISO images at:
   | ISO | URL |
   |-----|-----|
   | System Rescue ISO | https://www.system-rescue.org/Download/ |
   | Proxmox ISO | https://www.proxmox.com/en/downloads |
   | Ubuntu SERVER Cloud-Init ISO | https://cloud-images.ubuntu.com/noble/current/ |
   | Ubuntu DESKTOP ISO | https://ubuntu.com/download/desktop/ |

### 1.1.2. After Install basic Proxmox actions
#### 1.1.2.1. Check SSH is running
   ```
   systemctl status ssh.service
   ```
### 1.1.3. Run TTeck Proxmox VE Helper-Scripts at https://helper-scripts.com/scripts?id=Proxmox+VE+Post+Install
   ```
   bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/post-pve-install.sh)"
   ```
### 1.1.4. Hardware Passthrough (iGPU):
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
### 1.1.5. Run TTeck Proxmox VE Processor Microcode script at https://helper-scripts.com/scripts?id=Proxmox+VE+Processor+Microcode
   ```
   bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/microcode.sh)"
   ```
### 1.1.6. Install neofetch
   ```
   apt-get install neofetch
   ```
### 1.1.7. Set up pve terminal
    - Copy-and-paste pve .bashrc
    - Reload .bashrc
      ```
      source ~/.bashrc
      ```
### 1.1.8. Install iTerm shell integration: iTerm2 → Iterm Shell Integration

