# Set up Proxmox from scratch
----
## Contents
  - [Make a bootable USB with OS images and tools using Ventoy](#make-a-bootable-usb-with-os-images-and-tools-using-ventoy)
  - [Proxmox post-install setup](#proxmox-post-install-setup)
  - [Set up the Proxmox terminal](#set-up-the-proxmox-terminal)
  - [Configure Proxmox alerts](#configure-proxmox-alerts)
----
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
### Set up IKoolcore specific Proxmox summary Hardware Passthrough (iGPU):
Follow steps in iKoolcore R2 wiki at https://wiki.ikoolcore.com/#/R2/en/FAQs/VM
- Add iKoolcore R2 hardware stats to Proxmox summary page by running shell script at https://github.com/KoolCore/Proxmox_VE_Status
> You may need to run `bash ./Proxmox_VE_Status_zh.sh` first, and then run `bash ./Proxmox_VE_Status_en.sh` to display sensor data in the Proxmox pve summary page.
- Add hardware passthrough by running script at https://github.com/KoolCore/Proxmox_VE_Status
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
Reboot.

## Set up the Proxmox terminal
### Install neofetch
```shell
sudo apt install neofetch
```
### Install Oh My Bash
```shell
bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)"
```
Reload `.bashrc`
```shell
source ~/.bashrc
```
> If error loading OMB, set proper OMB file location in `.bashrc`
> ```shell
> export OSH='/root/.oh-my-bash'
> ```
### Add plugins and completions to `.bashrc`
Edit `.bashrc` by copying and comparing to GitHub Proxmox [`.bashrc`](/Proxmox%20files/.bashrc)
```shell
nano .bashrc
```
Reload `.bashrc`
```shell
source ~/.bashrc
```

### Install iTerm shell integration:
In iTerm 2 GUI, click on `iTerm2 → Iterm Shell Integration`

## Configure Proxmox alerts
This guide is adapted from Techno Tim's [Set up alerts in Proxmox before it's too late!](https://technotim.live/posts/proxmox-alerts/) article.

Install dependencies
```shell
apt update
apt install -y libsasl2-modules mailutils
```
Configure app passwords on your Google account
https://myaccount.google.com/apppasswords

Configure postfix
```shell
echo "smtp.gmail.com your-email@gmail.com:YourAppPassword" > /etc/postfix/sasl_passwd
```
Update permissions
```shell
chmod 600 /etc/postfix/sasl_passwd
```
Hash the file

```shell
postmap hash:/etc/postfix/sasl_passwd
```
Check to to be sure the db file was created

```shell
cat /etc/postfix/sasl_passwd.db
```
Edit postfix config

```shell
nano /etc/postfix/main.cf
```
Comment out line 26
```shell
### relayhost =
```
Add this text at end of file
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
Reload postfix
```shell
postfix reload
```
Send a test email
```shell
echo "This is a test message sent from postfix on my Proxmox Server" | mail -s "Test Email from Proxmox" shulerpve1@gmail.com
```
### Fix the "from" name in email

Install dependency
```shell
apt update
apt install postfix-pcre
```
Edit config
```shell
nano /etc/postfix/smtp_header_checks
```
Add the following text
```shell
/^From:.*/ REPLACE From: pve1-alert <pve1-alert@something.com>
```
Hash the file
```shell
postmap hash:/etc/postfix/smtp_header_checks
```
Check the contents of the file
```shell
cat /etc/postfix/smtp_header_checks.db
```
Add the module to our postfix config
```shell
nano /etc/postfix/main.cf
```
Add to the end of the file
```shell
smtp_header_checks = pcre:/etc/postfix/smtp_header_checks
```
Reload postfix service
```shell
postfix reload
```
Send another  test email
```shell
echo "This is a second test message sent from postfix on my Proxmox Server" | mail -s "Second Test Email from Proxmox" shulerpve1@gmail.com
```
---------------
