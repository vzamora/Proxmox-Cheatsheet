To enable SSH as root user, go to the terminal for the LXC in the Proxmox WebUI and run:
	nano /etc/ssh/sshd_config

Scroll down using the arrow keys until you see the below line:
	#PermitRootLogin prohibit-password

Change it to the below line, then hit "Ctrl+X" > Y > 'Enter' to allow root SSH (note deletion of the # symbol, and it should change color in the terminal).
	PermitRootLogin yes
