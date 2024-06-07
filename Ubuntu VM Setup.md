# Ubuntu VM installation within Proxmox 

## Install Ubuntu VM within Proxmox PVE GUI shell

Run Tteck Ubuntu 24.04 script at https://helper-scripts.com/scripts?id=Ubuntu+24.04
> NOTE: Run TTeck scripts from Proxmox GUI shell, not SSH!
```shell-script
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/vm/ubuntu2404-vm.sh)"
```
> WARNING: Do NOT start the VM until after you do the following Cloud-Init setup step!
Setup Cloud-Init on Proxmox

- Info at https://github.com/tteck/Proxmox/discussions/2072 

Start the VM
## Ubuntu OS setup
    
> NOTE: This is based on Anand's OS setup guide at [Ultimate Docker Server: Getting Started with OS Preparation Part 1](https://www.smarthomebeginner.com/ultimate-docker-server-1-os-preparation/). 
>The steps below are adapted from this guide to match Ubuntu 24.04 SSH requirements.
### Create new user and give sudo priveleges
```shell-script
adduser kurt
adduser kurt sudo
```
### Update Ubuntu OS
```shell-script
sudo apt update
sudo apt upgrade
```
### Install basic/required packages
```shell-script
sudo apt install ca-certificates curl gnupg lsb-release ntp htop zip unzip gnupg apt-transport-https ca-certificates net-tools ncdu apache2-utils git neofetch vsftpd
```
### Change SSH port to 2053

To change the port of the SSH server, the systemd configuration for ssh.socket must be changed or supplemented. The configuration adjustment is made by editing a *.conf file in the directory `/etc/systemd/system/ssh.socket.d/`.

Edit `override.conf` file to extend the default config:
```shell-script
nano /etc/systemd/system/ssh.socket.d/override.conf
```
Edit the following lines:
> NOTE 1: These instructions are different than what is on Anand's site due to changes in Ubuntu 24.04 SSH.
> 
> NOTE 2: The blank line `ListenStream=` is required to ensure that port 22 is no longer used. Without this line, the SSH server would then be accessible via port 22 (default) *and* 2053.
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
### Add aliases to `.bashrc`
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
   - 
----------
