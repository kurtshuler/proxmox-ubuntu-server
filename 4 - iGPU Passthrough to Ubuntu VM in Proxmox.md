4 - iGPU Passthrough to Ubuntu VM in Proxmox
============================================
> **NOTE:** Do these steps AFTER you have created the Ubuntu VM!
# Within the Proxmox Host
## Add IOMMU Support
> **NOTE:** These steps are for EFI boot systems.

1. Open `/etc/kernel/cmdline`
```sh
nano /etc/kernel/cmdline
```
2. Add this to first line:
```EditorConfig
intel_iommu=on
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
