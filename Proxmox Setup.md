# Set up Proxmox from scratch

## Make a bootable USB with OS images and tools using Ventoy
1. Latest Ventoy installers at https://sourceforge.net/projects/ventoy/files/ Windows only - use Parallels Windows or Linux
2. ISO images at:
   | ISO | URL |
   |-----|-----|
   | System Rescue ISO | https://www.system-rescue.org/Download/ |
   | Proxmox ISO | https://www.proxmox.com/en/downloads |
   | Ubuntu SERVER Cloud-Init ISO | https://cloud-images.ubuntu.com/noble/current/ |
   | Ubuntu DESKTOP ISO | https://ubuntu.com/download/desktop/ |

## Proxmox post-install setup
### Check that SSH is running
```shell-script
systemctl status ssh.service
```
### Run TTeck Proxmox VE Helper-Scripts at https://helper-scripts.com/scripts?id=Proxmox+VE+Post+Install
> Run Tteck scripts from Proxmox GUI shell, not SSH!
```shell-script
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/post-pve-install.sh)"
```
### Set up Hardware Passthrough (iGPU):
- Review iKoolcore R2 wiki at https://wiki.ikoolcore.com/#/R2/en/FAQs/VM
- Add hardware passthrough https://github.com/KoolCore/Proxmox_VE_Status
- Read https://www.derekseaman.com/2023/04/proxmox-plex-lxc-with-alder-lake-transcoding.html
### Verify that hardware passthrough is working
Source: https://pve.proxmox.com/wiki/PCI_Passthrough

Verify IOMMU is enabled:
```shell-script
dmesg | grep -e DMAR -e IOMMU
```

Verify IOMMU interrupt remapping is enabled:
```shell-script
dmesg | grep 'remapping'
```

Verify that Proxmox recognizes the GPU:
```shell-script
lspci -v | grep -e VGA
```

Get more details about that VGA adapter:
```shell-script
lspci -v -s 00:02.0
```

### Run TTeck Proxmox VE Processor Microcode script at https://helper-scripts.com/scripts?id=Proxmox+VE+Processor+Microcode
> Run Tteck scripts from Proxmox *GUI* shell, not SSH!
```shell-script
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/microcode.sh)"
```

## Set up the Proxom terminal
### Install Oh My Bash
```shell
bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)"
```
### Reload `.bashrc`
```shell
source ~/.bashrc
```
### Edit `.bashrc` by copying and comparing to GitHub Proxmox [`.bashrc`](/Proxmox%20files/.bashrc)
### Reload `.bashrc`, again
```shell
nano .bashrc
```
> If error loading OMB, set proper OMB location in `.bashrc`
> ```shell
> export OSH='/root/.oh-my-bash'
> ```
### Install iTerm shell integration:
In iTerm 2 GUI, click on `iTerm2 → Iterm Shell Integration`

-----

