2 - Ubuntu VM installation within Proxmox
==========================================
There are (2) methods to install Ubuntu Server OS and I prefer the Cloud-Init Server install over the manual Server Live ISO install.

# Cloud-Init Server install using tteck's Proxmox VE Helper script

>**NOTE** For iGPU passthrough, the required files in `linux-generic` ***are NOT part of the Ubuntu Cloud-Init distribution*** used by the tteck Ubuntu installation script. That means we have to ***remember to load them*** using `sudo apt install sudo linux-generic` after starting the VM for the first time.

## 
>Run tteck's Ubuntu 24.04 script at https://helper-scripts.com/scripts?id=Ubuntu+24.04
>
> NOTE: Run tteck scripts from the **Proxmox GUI shell**, not SSH!
```shell-script
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/vm/ubuntu2404-vm.sh)"
```
## Setup Cloud-Init data and Resize Disks in Proxmox

```diff
- ⬇ WARNING: Do NOT start the VM until after you do the following Cloud-Init setup steps linked below! ⬇
```
> ➡️ Follow tteck's detailed **Cloud-Init setup** instructions at [https://github.com/tteck/Proxmox/discussions/2072](https://github.com/tteck/Proxmox/discussions/2072)

> ➡️ After the steps above, **STOP at tteck's "Install Docker" step**. We will do that later!


## In VM terminal, install files required for iGPU hardware passthrough
```diff
- ⬇ WARNING: Be sure to do this step or iGPU HW passthrough from Proxmox to your new VM will not work! ⬇
```
```sh
sudo apt install sudo linux-generic
```
# Manual Install Method: Server Live ISO install

## Upload the Live Install ISO to Proxmox
> 1. In the Promox GUI, go to `pve --> local (pve) --> ISO Images` and click `Download from URL`
> 2. Enter this URL in the `URL:` field
      `https://releases.ubuntu.com/24.04/ubuntu-24.04.1-live-server-amd64.iso`
> 3. Click the `Query URL` button
> 4. Click the `Download` button
>
> The task viewer will open with the download status. Drink some coffee while it downloads!

## Set up the VM
### In the Promox GUI, click on `pve` and then click on the `Create VM` button
>
>   The `Create: Virtual Machine` popup will appear
### Enter `General` settings
>   
```yaml
   `VM ID: 100`
   `Name: udms`
```
> <img src="https://github.com/kurtshuler/proxmox-ubuntu-server/blob/main/images/VM-settings-general.png" width="500"/> 

### Enter `OS` settings

> Choose your ISO image.
> 
> <img src="https://github.com/kurtshuler/proxmox-ubuntu-server/blob/main/images/VM-settings-OS.png" width="500"/> 

### Enter `System` settings

```yaml
   `Machine: q35`
   `BIOS: OVMF (UEFI)`
   `Add EFI Disk: YES`
   `EFI Storage: local-lvm`
   `SCSI Controller: VirtIO SCSI single`
   `Qemu Agent: YES`

```
> <img src="https://github.com/kurtshuler/proxmox-ubuntu-server/blob/main/images/VM-settings-system.png" width="500"/>
> 
### Enter `Disks` settings

```yaml
   `Storage: local-lvm`
   `Disk size (GiB): 128`
   `Discard: YES`
   `SSD emulation: YES`
   `Backup: YES`
```
> <img src="https://github.com/kurtshuler/proxmox-ubuntu-server/blob/main/images/VM-settings-disks.png" width="500"/>

### Enter `CPU` settings

```yaml
   `Cores: 8`
   `Type: host`
```
> <img src="https://github.com/kurtshuler/proxmox-ubuntu-server/blob/main/images/VM-settings-CPU.png" width="500"/>

### Enter `Memory` settings

```yaml
   `Memory (MiB): 12288`
   `Ballooning Device: NO/OFF`
```
> <img src="https://github.com/kurtshuler/proxmox-ubuntu-server/blob/main/images/VM-settings-memory.png" width="500"/>

### Enter `Network` settings

>Use default settings.
>
> <img src="https://github.com/kurtshuler/proxmox-ubuntu-server/blob/main/images/VM-settings-network.png" width="500"/>

### Check and Confirm
> <img src="https://github.com/kurtshuler/proxmox-ubuntu-server/blob/main/images/VM-settings-confirm.png" width="500"/>

## Next Steps

~~[1 - Set up Proxmox from scratch](1%20-%20Proxmox%20Setup.md)~~

~~[2 - Ubuntu VM installation within Proxmox](2%20-%20Ubuntu%20VM%20Installation%20within%20Proxmox.md)~~

[3 - iGPU Hardware Passthrough Setup](3%20-%20iGPU%20Hardware%20Passthrough%20Setup.md)

[4 - USB Drive Hardware Passthrough Setup](4%20-%20USB%20Drive%20Hardware%20Passthrough%20Setup.md)

[5 - Ubuntu OS setup](5%20-%20Ubuntu%20OS%20Setup.md)


