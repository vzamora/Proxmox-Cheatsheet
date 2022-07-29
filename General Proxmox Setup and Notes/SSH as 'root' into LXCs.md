# Entering the shell of a container
After you've provisioned a container, you'll probably want to enter its shell to actually do stuff with it. Here's a quick how-to for that.

## The sys admin way
- 1 ``ssh root@your-proxmox-server``
- 2 ``pct enter [container-id]`` the container ID is the number you pick when you create a vm or container in proxmox. You can see the number on the list of your virtual machines. So if you created a container with the ID 101, you would type ``pct enter 101``
- 3 Done! You're in the shell. You can do everything you need here or you can go and change the sshd config and start an ssh server.



## Via proxmox web ui

To enable SSH as root user, go to the terminal for the LXC in the Proxmox WebUI and run:
	nano /etc/ssh/sshd_config

Scroll down using the arrow keys until you see the below line:
	#PermitRootLogin prohibit-password

Change it to the below line, then hit "Ctrl+X" > Y > 'Enter' to allow root SSH (note deletion of the # symbol, and it should change color in the terminal).
	PermitRootLogin yes
