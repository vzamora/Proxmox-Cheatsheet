To install NextCloud (or probably OwnCloud), use the TKL installer like a normal LXC but DO NOT start it.  Edit the config file:
	nano /etc/pve/lxc/####.conf

and enable nesting by adding the following line:
	features: nesting=1 

THEN start the LXC.
Once you’ve started the lxc, you can find the address of the WebUI address by entering the command:
	ip addr show 

Then go to the WebUI by visiting http://ipaddr and click on the “Admin” user circle in the top-right, go to “Apps” and enable “External Storage Support.”  This will allow you to link NextCloud to any mounted ZFS Datasets you may have.
Note: I've only been able to access the WebUI by setting up Caddy.

NOTE: THE ABOVE ONLY INSTALLS THE LATEST ON TKL AND HAS HURDLES TO CLEAR PRIOR TO UPDATING.  I KNOW I NEED TO UPDATE THIS SECTION FOR INSTALLING NEXTCLOUD LATEST-REV

Nextcloud install is located in /var/www/nextcloud and /var/www/nextcloud-data

Running occ commands requires the use of “sudo -u www-data” prior to whatever Nextcloud tells you to use, like if it says “run ./occ update” then you’d need to run “sudo -u www-data /var/www/nextcloud occ update” from anywhere or run “sudo -u www-data occ update” from inside the /var/www/nextcloud directory.
