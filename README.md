# Proxmox
## Proxmox Installation
### Making a bootable USB with OS images using Ventoy
1. Latest Ventoy installers at https://sourceforge.net/projects/ventoy/files/ Windows only - use Parallels Windows or Linux
2. Proxmox ISO at https://www.proxmox.com/en/downloads
3. OS ISO images at:
   - Ubuntu SERVER Cloud-Init ISO at https://cloud-images.ubuntu.com/noble/current/
   - Ubuntu DESKTOP ISO at https://ubuntu.com/download/desktop/

### After Install basic Proxmox actions

1. Run TTeck Proxmox VE Helper-Scripts at https://helper-scripts.com/scripts?id=Proxmox+VE+Post+Install

   ```bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/post-pve-install.sh)"```
3. Review iKoolcore R2 wiki at https://wiki.ikoolcore.com/#/R2/en/FAQs/VM
4. Add hardware passthrough https://github.com/KoolCore/Proxmox_VE_Status
5. Set up SSH
6. Install neofetch
apt-get install neofetch
7. Copy-and-paste pve .bashrc
8. Reload .bashrc
source ~/.bashrc
9. Install iTem shell integration: iTerm2 → Iterm Shell Integration 

Verify Proxmox iGPU passthrough

