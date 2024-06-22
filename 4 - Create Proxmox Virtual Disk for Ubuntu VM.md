4 - Create Proxmox Virtual Disk for Ubuntu VM
===================================================

# Contents
- [4 - Create Proxmox Virtual Disk for Ubuntu VM](#4---create-proxmox-virtual-disk-for-ubuntu-vm)
- [Contents](#contents)
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
   ```lsblk```

2. Create a partition for your drive:
   ```fdisk /dev/sdb```

3. Within the 'fstab' console, type:
   ```n```
   …then `<Enter>` for all defaults.

4. Within the 'fstab' console, write the partition changes to disk:
   ```w```
   `fstab` will then quit and send you back to the normal shell command line.

5. Check the results:
   ```lsblk -o NAME,FSTYPE,SIZE,MOUNTPOINT```

## Format the new disk using the `mkfs` command

1. Format as `ext4`:
   ```mkfs.ext4 /dev/sdb1```

2. Check the results:
   ```lsblk -o NAME,FSTYPE,SIZE,MOUNTPOINT```

## Mount the new disk using the `mount` command

1. Make the mount point:
   ```mkdir /mnt/crucial```

2. Mount your partition to the mount point:
   ```mount /dev/sdb1 /mnt/crucial```

3. Verify the mount is correct:
   ```df -HT```

## Update `/etc/fstab` file so the drive will always be mounted at boot time

1. Open `fstab`:
   ```nano /etc/fstab```

2. Append this line:
   ```/dev/sdb1               /mnt/crucial          ext4    defaults 0 2```

3. Reboot and check
   ```reboot```
   ```lsblk -o NAME,FSTYPE,SIZE,MOUNTPOINT```
     ```df -HT```










4. Enter in `pve` terminal or shell to get list of connected drives by ID. Note the ID for your USB drive: `[DISK-ID]`
   ```shell
   ls -n /dev/disk/by-id/
   ```
5. To pass through drive to your VM:
   ```shell
   /sbin/qm set [VM-ID] -virtio2 /dev/disk/by-id/[DISK-ID]
   ```

   EXAMPLE:
     ```shell
   /sbin/qm set 100 -virtio2 /dev/disk/by-id/usb-Micron_CT4000X9SSD9_2332E8DB05F5-0:0
   ```
  
#  Within the Ubuntu VM:
## Mount the passed-through drive
Set up the mount point and mount the USB drive partition
1. Switch to VM console
2. Find out your passed-through drive partition name (something like `/dev/sda1` or `/dev/vda1`)
   ```sh
   lsblk
   ```
4.  Create the mount point
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
## Set up `fstab` so the passed-through drive will mount on boot
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
# Next Steps

~~[1 - Set up Proxmox from scratch](1%20-%20Proxmox%20Setup.md)~~

~~[2 - Ubuntu VM installation within Proxmox](2%20-%20Ubuntu%20VM%20Installation%20within%20Proxmox.md)~~

~~[3 - iGPU Hardware Passthrough Setup](3%20-%20iGPU%20Hardware%20Passthrough%20Setup.md)~~

~~[4 - USB Drive Hardware Passthrough Setup](4%20-%20USB%20Drive%20Hardware%20Passthrough%20Setup.md)~~

[5 - Ubuntu OS setup](5%20-%20Ubuntu%20OS%20Setup.md)
