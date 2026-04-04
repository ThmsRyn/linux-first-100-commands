# Disk and Storage

10 commands for monitoring disk usage, managing partitions, and working with storage devices.

---

### df

**What it does:** Reports available and used disk space on all mounted filesystems.

**Basic usage:**
```bash
df -h
```

**Real-world example:**
```bash
# Check disk usage on all filesystems and alert if any is over 90% full
df -h | awk 'NR>1 && $5+0 > 90 {print "WARNING: " $6 " is at " $5}'

# Show only real filesystems, excluding tmpfs and devtmpfs
df -h -x tmpfs -x devtmpfs
```

**Tip:** `-h` shows human-readable sizes (GB, MB). `-T` adds the filesystem type. Run this first when a server slows down — a full disk is a common culprit.

---

### du

**What it does:** Shows disk usage of files and directories, recursively.

**Basic usage:**
```bash
du -sh /var/log/
```

**Real-world example:**
```bash
# Find the top 10 largest directories in /var to identify what is eating disk space
du -h --max-depth=1 /var/ | sort -rh | head -10

# Find large directories in the current path
du -h --max-depth=2 . | sort -rh | head -20
```

**Tip:** `-s` gives a summary (total only). `-h` is human-readable. `--max-depth=1` prevents infinite recursion on large filesystems.

---

### lsblk

**What it does:** Lists block devices (disks, partitions, LVM volumes) in a tree layout.

**Basic usage:**
```bash
lsblk
```

**Real-world example:**
```bash
# Show all block devices with filesystem type and mount point
lsblk -f

# Verify that a new disk has been detected after hot-plugging
lsblk -d -o NAME,SIZE,MODEL
```

**Tip:** This is the first command to run after attaching a new disk. It shows you the device name (`/dev/sdb`, `/dev/nvme1n1`) you will need for partitioning and formatting.

---

### fdisk

**What it does:** Manages disk partition tables — creates, deletes, and modifies partitions.

**Basic usage:**
```bash
fdisk -l /dev/sdb
```

**Real-world example:**
```bash
# List all partitions on all disks
fdisk -l

# Start the interactive partitioning tool on a new disk
fdisk /dev/sdb
# Inside fdisk:
# n = new partition, p = primary, accept defaults for size
# t = change type (82 = Linux swap, 83 = Linux, 8e = LVM)
# w = write and exit
```

**Tip:** `fdisk` supports GPT since util-linux 2.23. For large disks (>2TB) or UEFI systems, use `gdisk` or `parted` instead — they handle GPT more reliably.

---

### mount

**What it does:** Attaches a filesystem to the directory tree at a specified mount point.

**Basic usage:**
```bash
mount /dev/sdb1 /mnt/data
```

**Real-world example:**
```bash
# Mount a new ext4 partition
mount /dev/sdb1 /mnt/data

# Mount an NFS share from a remote server
mount -t nfs 192.168.1.50:/exports/backups /mnt/nfs-backups

# List all currently mounted filesystems in a readable format
mount | column -t
```

**Tip:** Mounts made with `mount` are not persistent across reboots. Add the entry to `/etc/fstab` for persistence.

---

### umount

**What it does:** Detaches a filesystem from the directory tree.

**Basic usage:**
```bash
umount /mnt/data
```

**Real-world example:**
```bash
# Safely unmount a filesystem before removing the disk
umount /mnt/data

# Force unmount a busy filesystem (use with caution)
umount -f /mnt/nfs-backups

# Lazy unmount: detaches immediately and cleans up when all processes release it
umount -l /mnt/data
```

**Tip:** If `umount` fails with "target is busy", use `lsof +D /mnt/data` or `fuser -m /mnt/data` to find which process is holding the mount open.

---

### mkfs

**What it does:** Creates a filesystem on a partition or block device.

**Basic usage:**
```bash
mkfs.ext4 /dev/sdb1
```

**Real-world example:**
```bash
# Format a new partition as ext4 with a label
mkfs.ext4 -L "data-volume" /dev/sdb1

# Format a partition as XFS (better for large files)
mkfs.xfs /dev/sdb1

# Format a small partition as FAT32 (for USB or EFI)
mkfs.vfat -F 32 /dev/sdb1
```

**Tip:** `mkfs` is destructive and irreversible. Triple-check the device name with `lsblk` before running it.

---

### dd

**What it does:** Copies data at the block level — used for cloning disks, creating images, and writing bootable ISOs.

**Basic usage:**
```bash
dd if=/dev/sda of=/dev/sdb bs=64K status=progress
```

**Real-world example:**
```bash
# Write a bootable ISO to a USB drive
dd if=/tmp/ubuntu-24.04-server.iso of=/dev/sdb bs=4M status=progress && sync

# Create a compressed backup image of a disk
dd if=/dev/sda bs=64K status=progress | gzip > /mnt/nas/sda-backup-$(date +%F).img.gz

# Benchmark disk write speed
dd if=/dev/zero of=/tmp/testfile bs=1G count=1 oflag=direct
```

**Tip:** `dd` is often called "disk destroyer" — wrong `if`/`of` values wipe the wrong disk. Always verify with `lsblk` before running. Use `status=progress` to see real-time progress.

---

### iostat

**What it does:** Reports CPU and disk I/O statistics — useful for identifying storage bottlenecks.

**Basic usage:**
```bash
iostat -x 1 5
```

**Real-world example:**
```bash
# Monitor disk activity every 2 seconds to diagnose an I/O bottleneck
iostat -x 2

# Show extended stats for a specific device
iostat -x -d /dev/sda 2 10
```

**Tip:** Watch `%util` (how busy the device is) and `await` (average wait time per I/O request in ms). High `%util` combined with high `await` points to a saturated disk.

---

### ncdu

**What it does:** Interactive ncurses-based disk usage analyzer — browse directories visually to find what is consuming space.

**Basic usage:**
```bash
ncdu /var
```

**Real-world example:**
```bash
# Analyze disk usage on the entire system, excluding other filesystems
ncdu -x /

# Scan a specific directory tree
ncdu /home
```

**Tip:** Not installed by default. Install with `apt install ncdu`. Navigate with arrow keys, press `d` to delete a file/directory, `q` to quit. Far faster than running `du` manually.
