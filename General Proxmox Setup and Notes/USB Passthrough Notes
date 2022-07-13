USB Passthrough: So first we need to locate the device we want to pass through. lsusb could be used to find the connecting USB devices.

Note: lsusb will not work inside the container because there are no devices there yet

Here is the output of my case:
	Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
	Bus 001 Device 013: ID 045e:0800 Microsoft Corp.
	Bus 001 Device 020: ID 04a9:1746 Canon, Inc.
	Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub

In my case, I want to pass through my Canon MP printer to the container. So, I look specifically at the “Canon, Inc.”

The bus and device numbers are what we are looking for now, they can guide us to the correct device path beneath /dev . In this case, they are 001 and 020.

So the device path, that we need to mount to the container, should be /dev/bus/usb/001/020 . How to mount the container with this path? we can do it via the:
	lxc.mount.entry option.

But, as I said before, it is not enough to just mount the device path, the container must be allowed to use it as well. We can do it via cgroup, more specifically option lxc.cgroup.device.allow . To use this option we need to know the device’s major and minor numbers which can be retrieved as follows:
	ls -al /dev/bus/usb/001/020

I got:
	crw-rw-r — 1 root lp 189, 19 Jul 10 17:32 001/020

The major and minor numbers are 189 and 19 respectively.

Start the configuration

In my case, I use Proxmox to manage my containers. I need to go to /etc/pve/lxc/<container_id>.conf .

I will have to put two lines more into the configuration file:
	lxc.cgroup.devices.allow: c 189:* rwm
	lxc.mount.entry: /dev/bus/usb/001/020 dev/bus/usb/001/020 none bind,optional,create=file

The former is for allowing the container privilege to access the device specified by its major and minor numbers.

Note: 189:* means we care only the major number, all the minors apply. rwm means read-write-mount.

The latter is for mounting the device file representation into the container space. In this case, I mount the exact device, however, it might be a good idea to map the whole directory (with all its siblings) to the container because it is more than likely that the filename will change.

Given that it can be done thus:
	lxc.mount.entry: /dev/bus/usb/001 dev/bus/usb/001 none bind,optional,create=dir

Note: the target mount path doesn’t begin with a slash! it is dev not /dev
Now, stop and start the container. You should now be able to run the command lsusb inside the container and see the same results as if in the host.


____________________________________________________________________________
I have an intermittent problem with the Aeon Z-Wave stick not showing up correctly under LXC. I discovered it had happened again tonight when all of the motion-controlled lights stopped working. I’m running Home Assistant, and its UI told me that no Z-Wave devices had been online for the last 11 hours. In order to have the USB dongle show up when I created the container, I had to expressly tell Proxmox to bind-mount it from the host:
	lxc.cgroup.devices.allow: c 166:* rwm
	lxc.mount.entry: /dev/ttyACM0 dev/ttyACM0 none bind,optional,create=file

This works, but sometimes the device resets or something happens that causes the bind-mount to fail. In those instances the device shows up in the container mode 000:
	c--------- 1 root dialout 166, 0 Sep 21 02:45 /dev/ttyACM0

This shows up in HA’s OZW_Log.txt as a mess of errors complaining about not being able to write to the device.
As usual, I poked at it for a few minutes and looked around to see if others are having similar issues. I came across this post, where Stéphane Graber recommends using mknod to create the device file in a different directory that exists alongside the entry in /dev and bind-mount that instead. The result should be that the non-/dev entry maintains permissions when the /dev entry resets, and the bind-mount entry inside of the container doesn’t reset to 000.
 
I created /var/lib/lxc/112/devices and then executed this:
	mknod -m 660 ttyACM0 c 166 0
 
This gave me the correct character file. I then modified the container’s config file in /etc/pve/lxc:
	lxc.mount.entry: /var/lib/lxc/112/devices/ttyACM0 dev/ttyACM0 none bind,optional,create=file
 
After rebooting the container the mount works correctly. Time will tell if it survives the random reset issue.
