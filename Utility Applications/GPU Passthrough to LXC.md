Currently, these are just notes as I haven't gotten it working yet.  Most of this is stolen from a combination of [TheOrangeOne](https://theorangeone.net/posts/lxc-nvidia-gpu-passthrough/), and [Craft Computing's vGPU Guide](https://www.youtube.com/redirect?event=video_description&redir_token=QUFFLUhqbGFYUXhMWU92bmNNbTZZRU0zRnhKRGZ4Xzktd3xBQ3Jtc0tsNjA1cnBCYVVIVDRNZ3ExMFpFN0dJVzZhUUV2cHZXbU5OVUxDSXZudEZDVkdVTFluYWQwVEFSZV9naXNsNi1MZHhYSHJRTTJhbDE4LWdzaHk5dnpHdG8wNFh4RmIxaExDLUtreHRRRF9ESzdSLVVTYw&q=https%3A%2F%2Fdrive.google.com%2Fdrive%2Ffolders%2F1KHf-vxzUCGqsWZWOW0bXCvMhXh5EJxQl%3Fusp%3Dsharing&v=jTXPMcBqoi8).

In Proxmox, install the latest Nvidia drivers.  At the time of writing, the file name is "NVIDIA-Linux-x86_64-510.85.02.run"

Do this by going to [the Nvidia website](https://www.nvidia.com/download/index.aspx?lang=en-us) and downloading the latest.

While you can use wget in Proxmox, I recommend downloading to your client computer and using WINSCP (or similar) to transfer it to /root/ in Proxmox.  This is because it is CRITICAL that everything be running the same version of the driver.

Next, run the following to make the .run file executable and then run it:

	chmod +x NVIDIA*.run
	./NVIDIA------.run --dkms
  
Now you need to set linux to load drivers on boot.  To do this, run:

	nano /etc/modules-load.d/modules.conf

and paste into it the below:

	# Nvidia modules
	nvidia
	nvidia_uvm

Then update initramfs with:

	update-initramfs -u -k all
	
The final step is to add a udev rule to create the required device files for the nvidia driver, which for reasons aren’t created automatically. This is done in the /etc/udev/rules.d/70-nvidia.rules file:

	KERNEL=="nvidia", RUN+="/bin/bash -c '/usr/bin/nvidia-smi -L && /bin/chmod 666 /dev/nvidia*'"
	KERNEL=="nvidia_uvm", RUN+="/bin/bash -c '/usr/bin/nvidia-modprobe -c0 -u && /bin/chmod 0666 /dev/nvidia-uvm*'"
	SUBSYSTEM=="module", ACTION=="add", DEVPATH=="/module/nvidia", RUN+="/usr/bin/nvidia-modprobe -m"


Reboot and run "nvidia-smi" to see if something comes up.  It should look like this:

![image](https://user-images.githubusercontent.com/449075/201356065-7398355f-10dc-4f66-a8c2-eab5802933a0.png)

Congrats, Proxmox can now see/use the GPU.  Don't get too excited, though, you're not even that close.

Now, run:

	ls -l /dev/nvidia*

and you should get an output similar to the below:

	crw-rw-rw- 1 root root 195, 254 Dec 22 20:51 /dev/nvidia-modeset
	crw-rw-rw- 1 root root 243,   0 Dec 22 20:51 /dev/nvidia-uvm
	crw-rw-rw- 1 root root 243,   1 Dec 22 20:51 /dev/nvidia-uvm-tools
	crw-rw-rw- 1 root root 195,   0 Dec 22 20:51 /dev/nvidia0
	crw-rw-rw- 1 root root 195, 255 Dec 22 20:51 /dev/nvidiactl
	
If you don't see those files, something likely got loaded wrong.  Go back and make sure yo udidn't miss a step.

Now, look at the first number after "root root" - this is your device's "major number."  You'll need 195 and 243 in the example above.

Now we need to give your LXC access and mount it somewhere, so

	nano /etc/pve/lxc/<id>.conf
	
And Paste the following into the bottom:

	# Allow cgroup access
	lxc.cgroup2.devices.allow: c 195:* rwm
	lxc.cgroup2.devices.allow: c 243:* rwm

	# Pass through device files
	lxc.mount.entry: /dev/nvidia0 dev/nvidia0 none bind,optional,create=file
	lxc.mount.entry: /dev/nvidiactl dev/nvidiactl none bind,optional,create=file
	lxc.mount.entry: /dev/nvidia-uvm dev/nvidia-uvm none bind,optional,create=file
	lxc.mount.entry: /dev/nvidia-modeset dev/nvidia-modeset none bind,optional,create=file
	lxc.mount.entry: /dev/nvidia-uvm-tools dev/nvidia-uvm-tools none bind,optional,create=file

Now restart your container, and you've got an Nvidia GPU in it!!

Now it gets fun and picky.....your container still doesn't have any drivers installed.

Remember that driver we downloaded to our client instead of using wget?  Now's the time to use it!  If you ran wget, just make sure you're using the exact same link.  Either way, have the driver .run file in /root and make sure you install it without kernel modules.

	chmod +x NVIDIA*.run
	./NVIDIA*.run --no-kernel-module

This will likely throw errors referencing something like X.Org and libglvnd

Install these by running:

	apt install libglvnd-dev xorg-dev

Now re-run the drivers:

	./NVIDIA*.run --no-kernel-module
	
And reboot the container.

Now run the same "nvidia-smi" line as above and you should get the same looking output where it lists driver version, cuda version, utilization, etc.

Congratulations, you've now got your GPU set up to be able to be utilized by your LXC.
