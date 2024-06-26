# 1 - Set up Proxmox from scratch

## Contents
  - [Make a bootable USB with OS images and tools using Ventoy](#make-a-bootable-usb-with-os-images-and-tools-using-ventoy)
  - [Proxmox post-install setup](#proxmox-post-install-setup)
  - [Set up the Proxmox terminal](#set-up-the-proxmox-terminal)
  - [Configure Proxmox alerts](#configure-proxmox-alerts)
----
# Make a bootable USB with OS images and tools using Ventoy
1. Latest Ventoy installers at https://sourceforge.net/projects/ventoy/files/
  
   It is Linux or Windows only. For Mac, use Parallels Windows or Linux.
   
4. ISO images at:
   | ISO | URL |
   |-----|-----|
   | System Rescue ISO | https://www.system-rescue.org/Download/ |
   | Proxmox ISO | https://www.proxmox.com/en/downloads |
   | Ubuntu Server Live Install ISO | https://releases.ubuntu.com/24.04/ubuntu-24.04-live-server-amd64.iso|
   | Ubuntu Server Cloud-Init Install ISO | https://cloud-images.ubuntu.com/noble/current/ |
   | Ubuntu DESKTOP ISO | https://ubuntu.com/download/desktop/ |

# Proxmox post-install setup
## Check that SSH is running
```shell-script
systemctl status ssh.service
```
## Run TTeck Proxmox VE Helper-Scripts at https://helper-scripts.com/scripts?id=Proxmox+VE+Post+Install
> Run Tteck scripts from Proxmox GUI shell, not SSH!
```shell-script
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/post-pve-install.sh)"
```
## Set up IKoolcore-specific Proxmox summary 
Follow steps in iKoolcore R2 wiki at https://wiki.ikoolcore.com/#/R2/en/FAQs/VM
- Add iKoolcore R2 hardware stats to Proxmox summary page by running shell script at https://github.com/KoolCore/Proxmox_VE_Status
> NOTE: You may need to run `bash ./Proxmox_VE_Status_zh.sh` first, and then run `bash ./Proxmox_VE_Status_en.sh` to display sensor data in the Proxmox pve summary page.
```sh
cd Proxmox_VE_Status
```
```sh
bash ./Proxmox_VE_Status_zh.sh
```
```sh
bash ./Proxmox_VE_Status_en.sh
```

## Run TTeck Proxmox VE Processor Microcode script at https://helper-scripts.com/scripts?id=Proxmox+VE+Processor+Microcode
> Run Tteck scripts from Proxmox *GUI* shell, not SSH!
```shell-script
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/misc/microcode.sh)"
```
Reboot
```sh
reboot
```

# Set up the Proxmox terminal
## Install neofetch
```shell
sudo apt install neofetch
```
## Install Oh My Bash
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
## Add plugins and completions to `.bashrc`
Edit `.bashrc` by copying and comparing to GitHub Proxmox [`.bashrc`](/Proxmox%20files/.bashrc)
```shell
nano .bashrc
```
Reload `.bashrc`
```shell
source ~/.bashrc
```

## Install iTerm shell integration:
In iTerm 2 GUI, click on `iTerm2 → Iterm Shell Integration`

# Configure Proxmox alerts
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
## Fix the "from" name in email

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

# Next Steps

~~[1 - Set up Proxmox from scratch](1%20-%20Proxmox%20Setup.md)~~

[2 - Ubuntu VM installation within Proxmox](2%20-%20Ubuntu%20VM%20Installation%20within%20Proxmox.md)

[3 - iGPU Hardware Passthrough Setup](3%20-%20iGPU%20Hardware%20Passthrough%20Setup.md)

[4 - USB Drive Hardware Passthrough Setup](4%20-%20USB%20Drive%20Hardware%20Passthrough%20Setup.md)

[5 - Ubuntu OS setup](5%20-%20Ubuntu%20OS%20Setup.md)

