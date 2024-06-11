4 - iGPU Passthrough to Ubuntu VM in Proxmox
============================================
> **NOTE:** Do these steps AFTER you have created the Ubuntu VM!
>
> **SOURCES:**
> 1. iKoolcore R2 script code at https://github.com/KoolCore/Proxmox_VE_Status/blob/main/passthrough.sh
> 2. Craft Computing video at https://www.youtube.com/watch?v=_hOBAGKLQkI 

Executing the iKoolcore R2 hardware passthrough script at https://github.com/KoolCore/Proxmox_VE_Status did not work for me. It ran but didn't make the file changes. Instead, I created these steps manually from the script source code at https://github.com/KoolCore/Proxmox_VE_Status/blob/main/passthrough.sh. 

Also, the Craft Computing video at https://www.youtube.com/watch?v=_hOBAGKLQkI follows these steps almost exactly, wiht the only difference being the changes to `/etc/kernel/cmdline`.

# Within the Proxmox Host

## Make IOMMU changes at boot
>**NOTE:** There are two possible boot systems, Systemd (EFI) or Grub.
>
>**NOTE:** The **'Boot Mode'** in the Proxmox GUI summary page for a node (like `pve`) indicates whether it is EFI (systemd) or Grub booted.

Since we don't yet know what bootloader all of our nodes will use, **do both the Grub and EFI steps below!**

### For Grub boot, edit `/etc/default/grub`
1. Open `/etc/default/grub`
``` sh
nano /etc/default/grub
```
2. Change this line to:
```EditorConfig
GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on iommu=pt"
```
3. Save file and close

4. Run:
```sh
update-grub
```
### For EFI boot, edit `/etc/kernel/cmdline`
> **NOTE:** These steps are for EFI boot systems.

1. Open `/etc/kernel/cmdline`
```sh
nano /etc/kernel/cmdline
```
2. Add this to first line:

>**NOTE** All commands in `/etc/kernel/cmdline` must be in a **single line** on the **first line!**
```EditorConfig
intel_iommu=on iommu=pt
```
3. Save file and close

4. Run:
```sh
proxmox-boot-tool refresh
```
## Load VFIO modules at boot
1. Open `/etc/modules`
```sh
nano /etc/modules
```

2. Add these lines:
```
vfio
vfio_iommu_type1
vfio_pci
vfio_virqfd
```

3. Save file and close

## Configure VFIO for PCIe Passthrough-

1. Find your GPU PCI identifier
   It will be something like `00:02`
```sh
lspci
```

2. Find your GPU's PCI HEX values

   Enter the PCI identifier (`00:02`) from above into the `lspci` command: 
```
lspci -n -s 00:02 -v
```
You will see an associated HEX value like `8086:46d0`

3. Edit `/etc/modprobe.d/vfio.conf`

   Copy the HEX values from your GPU into this command and hit enter:
```sh
echo "options vfio-pci ids=8086:46d0 disable_vga=1"> /etc/modprobe.d/vfio.conf
```

4. Apply all changes
```sh
update-initramfs -u -k all
```

## Blacklist Proxmox host device drivers

This ensures nothing else on Proxmox can use the GPU that you want to pass through to a VM.

1. Edit `/etc/modprobe.d/iommu_unsafe_interrupts.conf`
```sh
echo "options vfio_iommu_type1 allow_unsafe_interrupts=1" > /etc/modprobe.d/iommu_unsafe_interrupts.conf
```

2. Edit `/etc/modprobe.d/blacklist.conf`
```sh
echo "blacklist nouveau" >> /etc/modprobe.d/blacklist.conf
echo "blacklist nvidia" >> /etc/modprobe.d/blacklist.conf
```

3. Apply all changes
```sh
update-initramfs -u -k all
```

4, Reboot to apply all changes
```sh
reboot
```

5. Verify all changes

After reboot, type
```sh
lspci -n -s 00:02 -v
```

   In the output, you should see the `vfio-pci` kernel driver being used: 
   ```yaml
   Kernel driver in use: vfio-pci
   ```
# In Proxmox GUI to add GPU to a VM

1. In GUI, click
```
pve —> [VM#] —> Hardware —> Add —> PCI Device
```

2. In popup, select
```yaml
Raw Device: YES
Device: Select your GPU
```

3. Then click the following:
```yaml
All Functions: YES
ROM-Bar: YES
Primary GPU: NO
PCI-Express: YES (requires 'machine: q35' in VM config file)
```

