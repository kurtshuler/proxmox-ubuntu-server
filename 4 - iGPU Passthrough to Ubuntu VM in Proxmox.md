4 - iGPU Passthrough to Ubuntu VM in Proxmox
============================================
> **NOTE:** Do these steps AFTER you have created the Ubuntu VM!
# Within the Proxmox Host
## Add IOMMU Support
> **NOTE:** These steps are for EFI boot systems.

Edit `/etc/kernel/cmdline`
```sh
nano /etc/kernel/cmdline
```
Add this to first line:
```EditorConfig
intel_iommu=on
```
Save file and close

Run:
```sh
proxmox-boot-tool refresh
```
