# 1 - Proxmox Setup
<!-- TOC tocDepth:2..2 chapterDepth:2..6 -->

- [1. Make a bootable USB with OS images and tools using Ventoy](#1-make-a-bootable-usb-with-os-images-and-tools-using-ventoy)
- [2. Proxmox post-install setup](#2-proxmox-post-install-setup)
- [3. Set up the Proxmox terminal](#3-set-up-the-proxmox-terminal)
- [4. Configure Proxmox alerts](#4-configure-proxmox-alerts)
- [5. Set up iGPU passthrough in Proxmox Host (VM steps done in Ubuntu OS checklist)](#5-set-up-igpu-passthrough-in-proxmox-host-vm-steps-done-in-ubuntu-os-checklist)

<!-- /TOC -->
-----------------
## 1. Make a bootable USB drive with OS images and tools using Ventoy
> Latest Ventoy installers are at https://sourceforge.net/projects/ventoy/files/
> 
> ➡️ Ventoy USB can be **created** in Linux or Windows only. For Mac, use Parallels Windows VM or Linux VM.
>
> **After** you have created the Ventoy USB, you can **copy files to it** using your Mac or PC.
   
Important ISO images:
   | ISO | URL |
   |-----|-----|
   | System Rescue ISO | https://www.system-rescue.org/Download/ |
   | Proxmox PVE and PBS ISOs | https://www.proxmox.com/en/downloads |
   | Ubuntu Server Live Install ISO | https://releases.ubuntu.com/24.04/ubuntu-24.04-live-server-amd64.iso|
   | Ubuntu Server Cloud-Init Install ISO | https://cloud-images.ubuntu.com/noble/current/ |
   | Ubuntu DESKTOP ISO | https://ubuntu.com/download/desktop/ |

## 2. Proxmox post-install setup
### 2.1. Check that SSH is running
```shell-script
systemctl status ssh.service
```
### 2.2. Run tteck's Proxmox VE Post Install Script
> tteck's Helper-Scripts are at https://tteck.github.io/Proxmox/
```diff
- WARNING: Run tteck scripts from the **Proxmox GUI shell**, not SSH!
```
```shell-script
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/post-pve-install.sh)"
```
### 2.3. Set up IKoolcore-specific Proxmox summary
> Follow the steps in the iKoolcore R2 wiki at https://github.com/KoolCore/Proxmox_VE_Status
>
> Add iKoolcore R2 hardware stats to the Proxmox summary page by running this shell script that I modified https://github.com/kurtshuler/proxmox-ubuntu-server/blob/main/Proxmox%20files/Proxmox_VE_Status_zh.sh
```sh
cd Proxmox_VE_Status
```
```sh
bash ./Proxmox_VE_Status_zh.sh
```

### 2.4. Run tteck's Proxmox VE Processor Microcode Script
> tteck's Helper-Scripts are at https://tteck.github.io/Proxmox/
```diff
- WARNING: Run tteck scripts from the **Proxmox GUI shell**, not SSH!
```
```shell-script
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/microcode.sh)"
```
> Reboot
```sh
reboot
```

## 3. Set up the Proxmox terminal
### 3.1. Install neofetch
```shell
sudo apt install neofetch
```
### 3.2. Install Oh My Bash
```shell
bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)"
```
> Reload `.bashrc`
```shell
source ~/.bashrc
```
> If error loading OMB, set proper OMB file location in `.bashrc`
> ```shell
> export OSH='/root/.oh-my-bash'
> ```
### 3.3. Add plugins and completions to `.bashrc`
> Edit `.bashrc` by copying and comparing to GitHub Proxmox [`.bashrc`](/Proxmox%20files/.bashrc)
```shell
nano .bashrc
```
> Reload `.bashrc`
```shell
source ~/.bashrc
```

### 3.4. Install iTerm shell integration:
> In iTerm 2 GUI, click on `iTerm2 → Iterm Shell Integration`

## 4. Configure Proxmox alerts
This guide is adapted from Techno Tim's [Set up alerts in Proxmox before it's too late!](https://technotim.live/posts/proxmox-alerts/) article.

### 4.1. Install dependencies
```shell
apt update
apt install -y libsasl2-modules mailutils
```
### 4.2. Configure app passwords on your Google account
> https://myaccount.google.com/apppasswords

### 4.3. Configure postfix
```shell
echo "smtp.gmail.com your-email@gmail.com:YourAppPassword" > /etc/postfix/sasl_passwd
```
#### 4.3.1. Update permissions
```shell
chmod 600 /etc/postfix/sasl_passwd
```
#### 4.3.2. Hash the file

```shell
postmap hash:/etc/postfix/sasl_passwd
```
> Check to to be sure the db file was created

```shell
cat /etc/postfix/sasl_passwd.db
```
#### 4.3.3. Edit postfix config

```shell
nano /etc/postfix/main.cf
```
> Comment out line 26
```shell
### relayhost =
```
> Add this text at end of file
```EditorConfig
# google mail configuration

relayhost = smtp.gmail.com:587
smtp_use_tls = yes
smtp_sasl_auth_enable = yes
smtp_sasl_security_options =
smtp_sasl_password_maps = hash:/etc/postfix/sasl_passwd
smtp_tls_CAfile = /etc/ssl/certs/Entrust_Root_Certification_Authority.pem
smtp_tls_session_cache_database = btree:/var/lib/postfix/smtp_tls_session_cache
smtp_tls_session_cache_timeout = 3600s
```
> Save file 

#### 4.3.4. Reload postfix
```shell
postfix reload
```
#### 4.3.5. Send a test email
```shell
echo "This is a test message sent from postfix on my Proxmox Server" | mail -s "Test Email from Proxmox" shulerpve1@gmail.com
```
### 4.4. Fix the "from" name in email

#### 4.4.1. Install dependency
```shell
apt update
apt install postfix-pcre
```
#### 4.4.2. Edit config
```shell
nano /etc/postfix/smtp_header_checks
```
> Add the following text
```shell
/^From:.*/ REPLACE From: pve1-alert <pve1-alert@something.com>
```
#### 4.4.3. Hash the file
```shell
postmap hash:/etc/postfix/smtp_header_checks
```
> Check the contents of the file
```shell
cat /etc/postfix/smtp_header_checks.db
```
#### 4.4.4. Add the module to our postfix config
```shell
nano /etc/postfix/main.cf
```
> Add to the end of the file
```shell
smtp_header_checks = pcre:/etc/postfix/smtp_header_checks
```
#### 4.4.5. Reload postfix service
```shell
postfix reload
```
#### 4.4.6. Send another  test email
```shell
echo "This is a second test message sent from postfix on my Proxmox Server" | mail -s "Second Test Email from Proxmox" shulerpve1@gmail.com
```
## 5. Set up iGPU passthrough in Proxmox Host
>
> **NOTE:** Additional steps are required in **each VM** to 
### 5.1. Make IOMMU changes at boot
>**NOTE:** There are two possible boot systems, Systemd (EFI) or Grub.
>
> According to the [Proxmox manual](https://pve.proxmox.com/pve-docs/pve-admin-guide.html#sysboot): "For EFI Systems installed with ZFS as the root filesystem `systemd-boot` is used, unless Secure Boot is enabled. All other deployments use the standard GRUB bootloader (this usually also applies to systems which are installed on top of Debian)."
>
> ➡️ BOTTOM LINE: If you did not install Proxmox on ZFS, it's normal that GRUB is used for booting in UEFI mode and you will use the first method below.

#### 5.1.1. For Grub boot, edit `/etc/default/grub`
> Open `/etc/default/grub`
``` sh
nano /etc/default/grub
```
> Change this line to:
```EditorConfig
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on iommu=pt"
```
> Save file and close
>
> Run:
```sh
update-grub
```
#### 5.1.2. For Systemd (EFI) boot, edit `/etc/kernel/cmdline`
> **NOTE:** These steps are for EFI boot systems.
>
> Open `/etc/kernel/cmdline`
```sh
nano /etc/kernel/cmdline
```
> Add this to first line:
>
>**NOTE** All commands in `/etc/kernel/cmdline` must be in a **single line** on the **first line!**
```EditorConfig
intel_iommu=on iommu=pt
```
> Save file and close
>
> Run:
```sh
proxmox-boot-tool refresh
```
### 5.2. Load VFIO modules at boot
> Open `/etc/modules`
```sh
nano /etc/modules
```

> Add these lines:
```
vfio
vfio_iommu_type1
vfio_pci
```

> Save file and close

### 5.3. Configure VFIO for PCIe Passthrough

#### 5.3.1. Find your GPU PCI identifier
   
> It will be something like `00:02`
```sh
lspci
```

#### 5.3.2. Find your GPU's PCI HEX values

> Enter the PCI identifier (`00:02`) from above into the `lspci` command: 
```
lspci -n -s 00:02 -v
```
> You will see an associated HEX value like `8086:46d0`

#### 5.3.3. Edit `/etc/modprobe.d/vfio.conf`

> Copy the HEX values from your GPU into this command and hit enter:
```sh
echo "options vfio-pci ids=8086:46d0 disable_vga=1"> /etc/modprobe.d/vfio.conf
```

#### 5.3.4. Apply all changes
```sh
update-initramfs -u -k all
```

### 5.4. Blacklist Proxmox host device drivers

> This ensures nothing else on Proxmox can use the GPU that you want to pass through to a VM.

#### 5.4.1. Edit `/etc/modprobe.d/iommu_unsafe_interrupts.conf`
```sh
echo "options vfio_iommu_type1 allow_unsafe_interrupts=1" > /etc/modprobe.d/iommu_unsafe_interrupts.conf
```

#### 5.4.2. Edit `/etc/modprobe.d/blacklist.conf`
```sh
echo "blacklist i915" >> /etc/modprobe.d/blacklist.conf
echo "blacklist nouveau" >> /etc/modprobe.d/blacklist.conf
echo "blacklist nvidia" >> /etc/modprobe.d/blacklist.conf
```

#### 5.4.3. Apply all changes
```sh
update-initramfs -u -k all
```

#### 5.4.4. Reboot to apply all changes
```sh
reboot
```

### 5.5. Verify all changes

#### 5.5.1. Verify `vfio-pci` kernel driver being used:
```sh
lspci -n -s 00:02 -v
```

> In the output, you should see: 
   ```yaml
   Kernel driver in use: vfio-pci
   ```
#### 5.5.2. Verify IOMMU is enabled:
```shell-script
dmesg | grep -e DMAR -e IOMMU
```
> In the output, you should see: 
   ```yaml
   DMAR: IOMMU enabled
   ```
#### 5.5.3. Verify IOMMU interrupt remapping is enabled:
```shell-script
dmesg | grep 'remapping'
```
> In the output, you should see something like: 
   ```yaml
   DMAR-IR: Enabled IRQ remapping in x2apic mode
   ```
#### 5.5.4. Verify that Proxmox recognizes the GPU:
```shell-script
lspci -v | grep -e VGA
```
> In the output, you should see something like: 
   ```yaml
   00:02.0 VGA compatible controller: Intel Corporation Alder Lake-N [UHD Graphics] (prog-if 00 [VGA controller])
```
# Next Steps

~~[1 - Set up Proxmox from scratch](1%20-%20Proxmox%20Setup.md)~~

[2 - Ubuntu VM installation within Proxmox](2%20-%20Ubuntu%20VM%20Installation%20within%20Proxmox.md)

[3 - iGPU Hardware Passthrough Setup](3%20-%20iGPU%20Hardware%20Passthrough%20Setup.md)

[4 - USB Drive Hardware Passthrough Setup](4%20-%20USB%20Drive%20Hardware%20Passthrough%20Setup.md)

[5 - Ubuntu OS setup](5%20-%20Ubuntu%20OS%20Setup.md)



[def]: #configure-proxmox-alerts
