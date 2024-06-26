4 - Create Proxmox Virtual Disk for Ubuntu VM
===================================================

# Contents
- [Within the Proxmox GUI VM setup page](#within-the-proxmox-gui-vm-setup-page)
- [Within the VM shell](#within-the-vm-shell)
  - [Partition the new disk using the `fdisk` command](#partition-the-new-disk-using-the-fdisk-command)
  - [Format the new disk using the `mkfs` command](#format-the-new-disk-using-the-mkfs-command)
  - [Mount the new disk using the `mount` command](#mount-the-new-disk-using-the-mount-command)
  - [Update `/etc/fstab` file so the drive will always be mounted at boot time](#update-etcfstab-file-so-the-drive-will-always-be-mounted-at-boot-time)
  - [Set up `fstab` so the passed-through drive will mount on boot](#set-up-fstab-so-the-passed-through-drive-will-mount-on-boot)
- [Next Steps](#next-steps)
----
> **NOTE:** Do these steps AFTER you have created the Ubuntu VM!
# Within the Proxmox GUI VM setup page

1. In Proxmox GUI, click on `pve` --> `[VM-ID]` --> `Hardware` --> `Add` --> `Hard Disk`

   ![images](images/proxmox%20get%20to%20hard%20disk%20setup.png)

2. Fill in the settings as below. Be sure to uncheck `Backup`!

    ![images](images/proxmox%20hard%20disk%20settings.png)

3. Verify the disk was created in your VM's hardware list:

    ![images](images/proxmox%20verify%20hard%20disk.png)

# Within the VM shell

## Partition the new disk using the `fdisk` command

1. List your drives:
   ```sh
   lsblk
   ```

3. Create a partition for your drive:
   ```sh
   fdisk /dev/sdb
   ```

5. Within the 'fstab' console, type:
   ```
   n
   ```
   …then `<Enter>` for all defaults.

7. Within the 'fstab' console, write the partition changes to disk:
   ```
   w
   ```
   `fstab` will then quit and send you back to the normal shell command line.

9. Check the results:
   ```sh
   lsblk -o NAME,FSTYPE,SIZE,MOUNTPOINT
   ```

## Format the new disk using the `mkfs` command

1. Format as `ext4`:
   ```sh
   mkfs.ext4 /dev/sdb1
   ```

3. Check the results:
   ```sh
   lsblk -o NAME,FSTYPE,SIZE,MOUNTPOINT
   ```

## Mount the new disk using the `mount` command

1. Make the mount point:
   ```sh
   mkdir /mnt/crucial
   ```

3. Mount your partition to the mount point:
   ```sh
   mount /dev/sdb1 /mnt/crucial
   ```

5. Verify the mount is correct:
   ```sh
   df -HT
   ```

## Update `/etc/fstab` file so the drive will always be mounted at boot time

1. Open `fstab`:
   ```sh
   nano /etc/fstab
   ```

3. Append this line:
   ```EditorConfig
   /dev/sdb1               /mnt/crucial          ext4    defaults 0 2
   ```

5. Reboot and check
   ```sh
   reboot
   ```
   ```sh
   lsblk -o NAME,FSTYPE,SIZE,MOUNTPOINT
   ```
   ```
   df -HT
   ```
# Next Steps

~~[1 - Set up Proxmox from scratch](1%20-%20Proxmox%20Setup.md)~~

~~[2 - Ubuntu VM installation within Proxmox](2%20-%20Ubuntu%20VM%20Installation%20within%20Proxmox.md)~~

~~[3 - iGPU Hardware Passthrough Setup](3%20-%20iGPU%20Hardware%20Passthrough%20Setup.md)~~

~~[4 - USB Drive Hardware Passthrough Setup](4%20-%20USB%20Drive%20Hardware%20Passthrough%20Setup.md)~~

[5 - Ubuntu OS setup](5%20-%20Ubuntu%20OS%20Setup.md)
