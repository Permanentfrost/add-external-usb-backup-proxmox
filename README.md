# Overview
This guide shows how to wipe, format, mount, and register an external USB SSD for backups in Proxmox.

## 1. Identify the disk
List all disks and find your USB device (e.g. `/dev/sdb`):
```bash
lsblk
fdisk -l
```

---

## 2. Wipe old partition table
```bash
wipefs -a /dev/sdb
```
⚠️ Replace `/dev/sdb` with your actual disk. **This deletes all data.**

---

## 3. Create new GPT + partition (fdisk method)
```bash
fdisk /dev/sdb
```
Inside `fdisk`:
```
g    # new GPT partition table
n    # new partition (accept defaults for full disk)
w    # write changes & exit
```

---

## 4. Format partition as ext4
```bash
mkfs.ext4 -L proxmox-backup /dev/sdb1
```

---

## 5. Create mount point
```bash
mkdir -p /mnt/usb-backup
```

---

## 6. Auto-mount via fstab
Find UUID:
```bash
blkid /dev/sdb1
```
Edit fstab:
```bash
nano /etc/fstab
```
Add:
```
UUID=xxxx-xxxx   /mnt/usb-backup   ext4   defaults   0   2
```

Apply:
```bash
mount -a
```

---

## 7. Register in Proxmox GUI
- Datacenter → Storage → **Add → Directory**  
- ID: `usb-backup`  
- Directory: `/mnt/usb-backup`  
- Content: check **Backup** only  

---

✅ Now your external USB SSD is ready for Proxmox backups.
