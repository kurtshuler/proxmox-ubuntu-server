# Ubuntu VM installation within Proxmox 

## Install Ubuntu VM within Proxmox PVE GUI shell

### Run Tteck Ubuntu 24.04 script at https://helper-scripts.com/scripts?id=Ubuntu+24.04
> NOTE: Run TTeck scripts from Proxmox GUI shell, not SSH!
```shell-script
bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/vm/ubuntu2404-vm.sh)"
```
### Setup Cloud-Init on Proxmox
> WARNING: Do NOT start the VM until after you do the following Cloud-Init setup step!


**Follow the detailed instructions at** https://github.com/tteck/Proxmox/discussions/2072 

### Start the VM
----
