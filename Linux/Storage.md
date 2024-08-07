## Disks
/dev/sda
/dev/xvda
## Partitions
/dev/sda1
/dev/xvda1
MBR supports up to 2TB disk and 4 primary partitions
GPT -- UEFI, 128 partitions, no disk limit
### Tools
fdisk (mbr)  -- edits the partition table of a disk (/dev/sda)
	4 primary partitions. any number of extended
	n to add a new partition
	types: 83 Linux, 8e Linux LVM, 82 Swap
gdisk (gpt)
	128 partitions
lsblk : with -f gives more info
parted -- saves changes with no warning
	mklabel -- creates a new partition table 
		can be msdos or gpt
	**mkpart primary ext4 1m 100m**
	**resizepart 3 350m**
gparted -- graphical parted
mkfs
resize2fs -- after defining a partition and filesystem

du 
	du -sh *
	**du -Shc** exclude subdirectories, c gives Total at the end
	-d depth
df
	df -hT -- includes filesystem type
		**--output=target,source,fstype,pcent**
#### Swap
3 steps:
fdisk - set type of partition to 82
	or gdisk - set type to 8200
mkswap thepartition -- creates the swap filesystem
swapon

## Filesystems
mkfs 
#### Native Linux
xfs -- high performance and reliability -- better for multi-thread, advanced journaling, journal section can be separate device
	-l logdev=DEVICE
ext2,3,4 -- better for single thread read-write, has journaling
exFAT -- from MS, supported by win, lin, mac, good for usb drives
btrfs -- builtin raid, copy on write, snapshots, quota subvolumes
	mkfs.btrfs /dev/sda1 /dev/sdb1 -m raid1  -- creates raid1: mirror
#### Non-native
FAT, FAT16, FAT32, VFAT

#### Mount
mount umount
	mount -t TYPE DEVICE MOUNTPOINT
	umount -f  -- force only for unresposive nfs 
	umount -r  -- try to remount read-only
lsof - to find open files if you cant umount
/etc/fstab
systemd .mount file: To work correctly, the mount unit _must_ have the same name as the mount point. In this case, the mount point is `/mnt/external`, so the file should be named `mnt-external.mount`. 
Can also have .automount

#### Tune
fsck - fix errors -- make sure to umount the fs before running
	-a auto repair - dangerous
	-p attempt to auto fix -- if it cannot, exits
xfs_repair
tune2fs

#### File Types
Normal file has -
d: directory
l: symlink
b: block
c: character
s: socket

## Permissions
setuid = 4xxx = chmod u+s
	applies to files and has no effect on directories
	the process will run with the privileges of the _user_ who owns the file
setgid = 2xxx = chmod g+s
	on files: make the process run with the privileges of the group who owns the file
	on dirs: make every file or directory created under it inherit the group from the parent directory
sticky bit = 1xxx = **chmod 1755 Another_Directory** or  chmod +t testdir
	works only on dirs, no effect on files
	Prevents users from removing or renaming a file in a directory unless they own that file or directory.
umask - mask (opposite) of permissions - sets default 
	dirs can be exe, files never exe by def
	0077 - allows all for owner, none for group and others
	umask -S -- display in symb mode
chmod -- with octals or with symbols and + and -
	**chmod ug+rw-x,o-rwx text.txt**
	**chmod a=rw- text.txt**
	-R recursive
	octal: read=4, write=2, exe=1
chgrp
chown

#### Links
ln
hard links cannot point to a target in a different partition or device

### File heirarchy FHS
/bin - essential binaries
/sbin - system binaries
/usr - Read-only user data, for apps
/boot - Files needed by the boot process, including the Initial RAM Disk (initrd) and the Linux kernel itself
/dev
/lib - shared libs needed to boot the operating system and to run the binaries under `/bin` and `/sbin`.
/media - removable media mounts
/mnt - temp filesystem mounts
/run - Run-time variable data
/proc - virt fs
/var - variable data written in runtime
/tmp - temp files cleared on boot
/var/tmp - temp file preserved between boots