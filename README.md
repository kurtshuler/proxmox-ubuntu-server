# Proxmox
## Proxmox Installation
### Making a bootable USB with OS images using Ventoy



- [ ] Latest Ventoy installers at https://sourceforge.net/projects/ventoy/files/ Windows only - use Parallels Windows or Linux
- [ ] Proxmox ISO at https://www.proxmox.com/en/downloads
- [ ] Ubuntu DESKTOP ISO at https://ubuntu.com/download/desktop
- [ ] Ubuntu SERVER Cloud-Init ISO at https://cloud-images.ubuntu.com/noble/current/

After Install basic Proxmox actions

1. Run TTeck Proxmox VE Helper-Scripts at https://helper-scripts.com/scripts?id=Proxmox+VE+Post+Install
2. Review iKoolcore R2 wiki at https://wiki.ikoolcore.com/#/R2/en/FAQs/VM
3. Add hardware passthrough https://github.com/KoolCore/Proxmox_VE_Status
4. Set up SSH
5. Install neofetch
apt-get install neofetch
6. Copy-and-paste pve .bashrc
7. Reload .bashrc
source ~/.bashrc
8. Install iTem shell integration: iTerm2 → Iterm Shell Integration 

Verify Proxmox iGPU passthrough

