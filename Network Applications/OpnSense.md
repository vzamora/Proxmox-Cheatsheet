For a more complete guide, see: https://homenetworkguy.com/how-to/run-opnsense-in-proxmox-vm/

The summary is:
Go to OpnSense.com and download the latest iso, in DVD format.

Upload this to your Proxmox (or download directly in PVE v7)

Create a new VM, as usual, with a few exceptions:
	-“Hard Disk” Tab: give the VM 40+GB of storage, and enable “IO Thread” in the advanced options.
	-“CPU” Tab: Give the VM 4 cores and turn “aes” to “On” in the advanced options
	-“Memory” Tab: 8GB RAM, Ballooning Device checked
	-“Network” Tab: Any bridge will work.  You can only add one now, but can add the rest later.
	-“Confirm” Tab: Uncheck “Start after created”
