# Proxmox
## Proxmox Installation
### Making a bootable USB with OS images using Ventoy
1. Latest Ventoy installers at https://sourceforge.net/projects/ventoy/files/ Windows only - use Parallels Windows or Linux
2. Proxmox ISO at https://www.proxmox.com/en/downloads
3. ISO images at:
   | ISO | URL |
   |-----|-----|
   | System Rescue ISO | https://www.system-rescue.org/Download/ |
   | Ubuntu SERVER Cloud-Init ISO | https://cloud-images.ubuntu.com/noble/current/ |
   | Ubuntu DESKTOP ISO: https://ubuntu.com/download/desktop/ |

### After Install basic Proxmox actions
1. Check SSH is running
   ```
   systemctl status ssh.service
   ```
2. Run TTeck Proxmox VE Helper-Scripts at https://helper-scripts.com/scripts?id=Proxmox+VE+Post+Install
   ```
   bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/post-pve-install.sh)"
   ```
3. Hardware Passthrough (iGPU):
   - Review iKoolcore R2 wiki at https://wiki.ikoolcore.com/#/R2/en/FAQs/VM
   - Add hardware passthrough https://github.com/KoolCore/Proxmox_VE_Status
   - Verify that hardware passthrough is working: https://pve.proxmox.com/wiki/PCI_Passthrough
      - Verify IOMMU is enabled:
        ```
        dmesg | grep -e DMAR -e IOMMU
        ```
      - Verify IOMMU interrupt remapping is enabled:
        ```
        dmesg | grep 'remapping'
        ```   
4. Run TTeck Proxmox VE Processor Microcode script at https://helper-scripts.com/scripts?id=Proxmox+VE+Processor+Microcode
   ```
   bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/microcode.sh)"
   ```
5. Install neofetch
   ```
   apt-get install neofetch
   ```
11. Set up pve terminal
    - Copy-and-paste pve .bashrc
    - Reload .bashrc
      ```
      source ~/.bashrc
      ```
14. Install iTerm shell integration: iTerm2 → Iterm Shell Integration

