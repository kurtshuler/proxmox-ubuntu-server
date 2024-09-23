# 2 - Ubuntu VM installation within Proxmox
There are (2) methods to install Ubuntu Server OS and I prefer the Cloud-Init Server install over the manual Server Live ISO install.

## Cloud-Init Server install using tteck's Proxmox VE Helper script

>**NOTE** For iGPU passthrough, the required files in `linux-generic` ***are NOT part of the Ubuntu Cloud-Init distribution*** used by the tteck Ubuntu installation script. That means we have to ***remember to load them*** using `sudo apt install sudo linux-generic` after starting the VM for the first time.

### Run tteck's Ubuntu 24.04 installtion script
>Run tteck's Ubuntu 24.04 script at https://helper-scripts.com/scripts?id=Ubuntu+24.04
>
> **NOTE:** Run tteck scripts from the **Proxmox GUI shell**, not SSH!
```shell-script
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/vm/ubuntu2404-vm.sh)"
```
### Setup Cloud-Init data and Resize Disks in Proxmox

```diff
- ⬇ WARNING: Do NOT start the VM until after you do the following Cloud-Init setup steps linked below! ⬇
```
> ➡️ Follow tteck's detailed **[Cloud-Init setup](https://github.com/tteck/Proxmox/discussions/2072)** instructions at [https://github.com/tteck/Proxmox/discussions/2072](https://github.com/tteck/Proxmox/discussions/2072)

> ➡️ After the steps above, **STOP at tteck's "Install Docker" step**. We will do that later!
>
> tteck's Ubuntu Cloud-Init steps are copied here below for your convenience.
#### Click on `Cloud-Init` in the VM's vertical middle menu bar
#### Fill in the `Cloud-Init` settings

### In VM terminal, install files required for iGPU hardware passthrough
```diff
- ⬇ WARNING: Be sure to do this step or iGPU HW passthrough from Proxmox to your new VM will not work! ⬇
```
```sh
sudo apt install sudo linux-generic
```
## Manual Install Method: Server Live ISO install

### Upload the Live Install ISO to Proxmox
> 1. In the Promox GUI, go to `pve --> local (pve) --> ISO Images` and click `Download from URL`
> 2. Enter this URL in the `URL:` field
      `https://releases.ubuntu.com/24.04/ubuntu-24.04.1-live-server-amd64.iso`
> 3. Click the `Query URL` button
> 4. Click the `Download` button
>
> The task viewer will open with the download status. Drink some coffee while it downloads!

### Set up the VM
#### In the Promox GUI, click on `pve` and then click on the `Create VM` button
>
>   The `Create: Virtual Machine` popup will appear
#### Enter `General` settings
>   
```yaml
   `VM ID: 100`
   `Name: udms`
```
> <img src="https://github.com/kurtshuler/proxmox-ubuntu-server/blob/main/images/VM-settings-general.png" width="500"/> 

#### Enter `OS` settings

> Choose your ISO image.
> 
> <img src="https://github.com/kurtshuler/proxmox-ubuntu-server/blob/main/images/VM-settings-OS.png" width="500"/> 

#### Enter `System` settings

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
#### Enter `Disks` settings

```yaml
   `Storage: local-lvm`
   `Disk size (GiB): 128`
   `Discard: YES`
   `SSD emulation: YES`
   `Backup: YES`
```
> <img src="https://github.com/kurtshuler/proxmox-ubuntu-server/blob/main/images/VM-settings-disks.png" width="500"/>

#### Enter `CPU` settings

```yaml
   `Cores: 8`
   `Type: host`
```
> <img src="https://github.com/kurtshuler/proxmox-ubuntu-server/blob/main/images/VM-settings-CPU.png" width="500"/>

#### Enter `Memory` settings

```yaml
   `Memory (MiB): 12288`
   `Ballooning Device: NO/OFF`
```
> <img src="https://github.com/kurtshuler/proxmox-ubuntu-server/blob/main/images/VM-settings-memory.png" width="500"/>

#### Enter `Network` settings

>Use default settings.
>
> <img src="https://github.com/kurtshuler/proxmox-ubuntu-server/blob/main/images/VM-settings-network.png" width="500"/>

#### Check and Confirm
> <img src="https://github.com/kurtshuler/proxmox-ubuntu-server/blob/main/images/VM-settings-confirm.png" width="500"/>


## iGPU Passthrough: Add GPU to VM PCI devices
> **NOTE:** These steps can only be done **after** you have created a VM and only affect that VM. They are included here for your convenience.
>
> **NOTE:** If you have issues after the steps below, ensure you installed the `linux-generic` which are required to be installed to enable iGPU passthrough after using tteck's Ubuntu installation script. **Remember to load them** using `sudo apt install sudo linux-generic` after starting the VM for the first time!
>


### In Proxmox GUI, click `PCI Device`
```
pve —> [VM#] —> Hardware —> Add —> PCI Device
```
> <img src="https://github.com/kurtshuler/proxmox-ubuntu-server/blob/main/images/iGPU-passthrough-add-pci-device-button.png" width="500"/>
   
### In the popup, select
```yaml
Raw Device: YES
Device: Select your GPU
```
Then click the following:
```yaml
All Functions: YES
ROM-Bar: YES
Primary GPU: YES
PCI-Express: YES (requires 'machine: q35' in VM config file)
```
> <img src="https://github.com/kurtshuler/proxmox-ubuntu-server/blob/main/images/iGPU-passthrough-add-pci-device-button-screen.png" width="500"/>
   
### Check the results
> <img src="https://github.com/kurtshuler/proxmox-ubuntu-server/blob/main/images/iGPU-passthrough-add-pci-device-check.png" width="500"/>

## Turn off `Display`
Click on "Display", then "Edit", and set "Graphic Card" to "none", and press OK.
>**NOTE:** This will mean that the "console" function on the left will no longer work, and the only way to get into your VM will be via SSH. I have tried dozens of options to get the console to keep working after adding the GPU, and nothing has worked, but SSH to the server still works just fine. Open to suggestions on how to get this to work long term)

## Reboot VM and verify
```sh
lspci -k | grep VGA
lspci -n -s 01:00 -v
```
## Reboot and verify iGPU hardware passthough is working in the Ubuntu VM
Check to see if your VGA adapter is available
```sh
lspci -nnv | grep VGA
```
Check to confirm `Kernel driver in use: i915`:
```sh
lspci -n -s 01:00 -v
```
Check to see that you have `renderD128` in `/dev/dri`
```sh
ls -l /dev/dri/by-path/
```




## Next Steps

~~[1 - Set up Proxmox from scratch](1%20-%20Proxmox%20Setup.md)~~

~~[2 - Ubuntu VM installation within Proxmox](2%20-%20Ubuntu%20VM%20Installation%20within%20Proxmox.md)~~

[3 - iGPU Hardware Passthrough Setup](3%20-%20iGPU%20Hardware%20Passthrough%20Setup.md)

[4 - USB Drive Hardware Passthrough Setup](4%20-%20USB%20Drive%20Hardware%20Passthrough%20Setup.md)

[5 - Ubuntu OS setup](5%20-%20Ubuntu%20OS%20Setup.md)


