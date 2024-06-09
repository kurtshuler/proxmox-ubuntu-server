
# 3 - USB Drive Passthrough to Proxmox VM
## Contents
  - [Within Proxmox Node :Psss through from Proxmox node to VM](#within-proxmox-node-psss-through-from-proxmox-node-to-vm)
  - [Within the VM: Set up the mount point and ount the USB drive partition](#within-the-vm-set-up-the-mount-point-and-ount-the-usb-drive-partition)
  - [Within the VM:  Set up `fstab` so passed-through drive will mount on boot](#within-the-vm--set-up-fstab-so-passed-through-drive-will-mount-on-boot)


## Within Proxmox Node: Pass through from Proxmox node to VM

> ***NOTE:*** Watch this video for a good overview of the process https://www.youtube.com/watch?v=U-UTMuhmC1U
1. Plug in the USB drive (duh!)
2. Go to Proxmox GUI node (usually `pve`) and disk to note which drive you want to pass through: `pve --> Disks`
3. Note the VM number you want to pass the disk through to: `[VM-ID]`
4. Enter in `pve` shell to get list of connected drives by ID. Note the ID for your USB drive: `[DISK-ID]`
   ```shell
   ls -n /dev/disk/by-id/
   ```
5. To pass through drive to you VM:
   ```shell
   /sbin/qm set [VM-ID] -virtio2 /dev/disk/by-id/[DISK-ID]
   ```
## Within the VM: Set up the mount point and mount the USB drive partition
1. Switch to VM console
2. Find out your passed-through drive partition name (something like `/dev/sda1` or `/dev/vda1`)
3.  Create the mount point
   ```shell
      sudo mkdir /mnt/crucial
   ```
4. Mount the passed-through drive to the VM mount point
   ```sh
   sudo mount /dev/vda1 /mnt/crucial
   ```
5. Verify mount using `df` or `mount` methods
   ```sh
   df -h
   ```
   or
      ```sh
   mount
   ```
6. List files on the passed-through USB drive
   ```sh
   cd /mnt/crucial
   ls -al
   ```
## Within the VM:  Set up `fstab` so passed-through drive will mount on boot
1. Edit the `fstab` file
   ```sh
   sudo nano /etc/fstab
   ```
2. Add the following line
   ```EditorConfig
   /dev/vda1   /mnt/crucial   ext4    defaults     0        2
   ```
3. Test by mounting all file systems in `fstab`
   ```sh
   sudo mount -av
   ```
   ----