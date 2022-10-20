To create Mount Points in an LXC container in Proxmox, simply go to the Proxmox Host Shell and navigate type in:

	nano /etc/pve/lxc/XXX.conf

Where XXX is the number assigned to your LXC Container in PVE.

Then, add a mount point line to the config file: (assuming "ZFSPool1" is the name of your ZFS Pool and "media" is the name of your dataset)

	mp0: /ZFSPool1/media,mp=/mnt/media
	mp1: /ZFSPool1/XXXX,mp=/mnt/YYYY

Where XXXX, YYYY are just the names of the directories/data sets you want to add.
You can keep adding lines for more mount points.  For example, you can easily keep going and add a “Shows” mount point by adding the line
	mp2: /ZFSPool1/Shows,mp=/mnt/Shows

When you’re done adding lines, just hit Ctrl+X to exit, Y to save, and then Enter to exit.
Note: Do not use spaces in the directory or dataset names.  There might be a way to do it, but I haven’t yet.
____________________________________________________________________________

To create, rename, or destroy ZFS Datasets, use:
	
	zfs create ZFSPool1/YYYY
	zfs destroy ZFSPool1/XXXX
	zfs rename ZFSPool1/XXXX ZFSPool1/YYYY

Where XXXX is the name of the existing Dataset, and YYYY is the name you want the dataset to be.  Notice the lack of leading slashes on the ZFS dataset locations.
____________________________________________________________________________

To check the current status of all ZFS Pools, use:
zpool status
This will list information in the following way:
“ZFS Pool Name”
	“ZFS Pool VDev0 Name”
		“Disk0 in vDev”
		“Disk1 in vDev”
….etc
____________________________________________________________________________

To see what ZFS Datasets you have, use:
	zfs list
This will simply list the datasets in each ZFS Pool
____________________________________________________________________________

To safely move data from one point to another, the “RSync” command is the best.
The trailing slash on the “From” directory is CRITICAL

	rsync -rva /ZFSPool1/storage/subvol-100-disk-0/XXXX/ /ZFSPool1
____________________________________________________________________________

To replace a drive in your ZPool:

It's simple, you just run:
	
	zpool replace ZFSPOOLNAME DRIVE_YOU_WANT_GONE DRIVE_YOU_WANT_TO_USE

At least for me, when I run "zpool status ZFSPOOLNAME" I get a bunch of wwn-0x5000???? type names for my drives.  I'm like 90% sure that's because I'm passing them through an HBA.

Either way, the way to figure out how to replace which drive is to figure out which drive you want to replace (like /dev/sda) and then figure out its /dev/disk/by-id/? address.  To do this, run:

	udevadm info --query=all --name=/dev/sda
	
Nearly at the bottom will be a line called "DEVLINKS" that lists the /dev/disk/by-id name in both wwn- format and ata-DRIVE_SKU-SERIAL_NUMBER format.

What matters is that you get the by-id/wwn- format number written down.

Do this for both drives as wwn- is more stable than /dev/sd# formatting.  Then your command will look like below.  Remember, the drive you're taking out is the first one, and you're replacing that drive with the second disk in the command:

	zpool replace ZFSPOOLNAME /dev/disk/by-id/wwn-0x5000######### /dev/disk/by-id/wwn-0x5000#########
