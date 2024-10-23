# 6 - Ubuntu OS setup

## Contents
  - [Initial Setup](#initial-setup)
  - [Change SSH port to 2053](#change-ssh-port-to-2053)
  - [Set up the Ubuntu terminal](#set-up-the-ubuntu-terminal)
  - [Make server tweaks](#make-server-tweaks)
  - [Enable UFW firewall](#enable-ufw-firewall)
----    


## Initial Setup
> NOTE: These steps are based on Anand's OS setup guide at [Ultimate Docker Server: Getting Started with OS Preparation Part 1](https://www.smarthomebeginner.com/ultimate-docker-server-1-os-preparation/). 
>The steps below are adapted from this guide to match Ubuntu 24.04 SSH requirements.
### Create new user and give sudo priveleges
```shell-script
adduser kurt
adduser kurt sudo
```
### Update Ubuntu OS
```shell
sudo apt update
sudo apt upgrade
```
### Install basic/required packages
```shellcheck
sudo apt install sudo linux-generic ca-certificates curl gnupg lsb-release ntp htop zip unzip gnupg apt-transport-https ca-certificates net-tools ncdu apache2-utils git neofetch vsftpd mc
```
> NOTE: `linux-generic` is required to enable GPU passthrough from Proxmox to the Ubuntu VM. It is not included in the Ubuntu Cloud-Init distribution used by the Tteck Ubuntu installation script.
## Change SSH port to 2053
> NOTE 1: These instructions are different than what is on Anand's site due to changes in Ubuntu 24.04 SSH.
> 
> NOTE 2: The blank line `ListenStream=` is required to ensure that port 22 is no longer used. Without this line, the SSH server would then be accessible via port 22 (default) *and* 2053.

To change the port of the SSH server, the systemd configuration for ssh.socket must be changed or supplemented. The configuration adjustment is made by editing the socket using `systemctl`.

Edit `ssh.socket` file:
```shell-script
systemctl edit ssh.socket
```
Add the following lines at the top under the two initial commented lines:

```EditorConfig
[Socket]
ListenStream=
ListenStream=2053
```
Restart SSH:
```shell
systemctl daemon-reload
sudo systemctl restart ssh  
```
Reboot the VM:
```shell
reboot 
```
Ensure that port 22 is *closed* and port 2053 is *open*:
```sh
netstat -ant | grep 2053
```
```shell-script
sudo lsof -nP -iTCP -sTCP:LISTEN
```
```shell-script
sudo netstat -tunpl
```
SSH `root` check: Log into Ubuntu server using new SSH port 2053
```shell-script
ssh root@192.168.1.100 -p 2053
```
SSH `user` check: Log into Ubuntu server using new SSH port 2053
```shell
ssh kurt@192.168.1.100 -p 2053
```

## Set up the Ubuntu terminal
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
### Add bash aliases, OMS plugins and OMB completions to `.bashrc`
Edit `.bashrc` by copying and comparing to GitHub Ubuntu [`.bashrc`](/Ubuntu%20files/.bashrc)
Be sure to copy over [Anand's bash aliases](https://github.com/htpcBeginner/docker-traefik/blob/master/shared/config/bash_aliases)
```shell
nano .bashrc
```
Reload `.bashrc`
```shell
source ~/.bashrc
```
### Install iTerm shell integration
In iTerm 2 GUI, click on `iTerm2 → Iterm Shell Integration`

##  Make server tweaks
> NOTE: This is based on Anand's OS setup guide at [Ultimate Docker Server: Getting Started with OS Preparation Part 1](https://www.smarthomebeginner.com/ultimate-docker-server-1-os-preparation/).

System configuration tweaks to enhance the performance and handling of large list of files (e.g. Plex/Jellyfin metadata). 

Edit `/etc/sysctl.conf` using the following command:

```shell
sudo nano /etc/sysctl.conf
```

Add the following 3 lines at the end of the file:
```EditorConfig
vm.swappiness=10
vm.vfs_cache_pressure=50
fs.inotify.max_user_watches=262144
```
## Enable UFW firewall
Apply UFW settings:
```shell
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow from 192.168.0.0/24
sudo ufw allow 80
sudo ufw allow 443
```
Activate UFW using the command:

```shell
sudo ufw enable
```
Check  UFW status
```shell
sudo ufw status
```

## Mount Synology NAS shared NFS drive using `mount` and `fstab`

### Create mount point
1. Make the mount point:
   ```sh
   mkdir /mnt/nas
   ```

3. Mount your partition to the mount point:
   ```sh
   mount nas.kurtshuler.net:/volume1/data /mnt/nas
   ```

5. Verify the mount is correct:
   ```sh
   df -HT
   ```
## Update `/etc/fstab` file so the Synology shared drive will always be mounted at boot time

1. Open Terminal and install `nfs-common`: 
   ```sh
   sudo apt install nfs-common
   ```
3. Open `fstab`:
   ```sh
   nano /etc/fstab
   ```

4. Append this line:
   ```EditorConfig
   nas.kurtshuler.net:/volume1/data /mnt/nas nfs defaults        0       0
   ```

5. Reboot and check
   ```sh
   reboot
   ```
   ```sh
   df -HT
   ```

# Next Steps

~~[1 - Set up Proxmox from scratch](1%20-%20Proxmox%20Setup.md)~~

~~[2 - Ubuntu VM installation within Proxmox](2%20-%20Ubuntu%20VM%20Installation%20within%20Proxmox.md)~~

~~[3 - iGPU Hardware Passthrough Setup](3%20-%20iGPU%20Hardware%20Passthrough%20Setup.md)~~

~~[4 - USB Drive Hardware Passthrough Setup](4%20-%20USB%20Drive%20Hardware%20Passthrough%20Setup.md)~~

~~[5 - Ubuntu OS setup](5%20-%20Ubuntu%20OS%20Setup.md)~~

Download Anand's Auto-Traefik from https://github.com/htpcBeginner/auto-traefik
