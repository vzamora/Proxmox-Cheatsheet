Create a new Debian LXC, and then open the terminal for it. 

To install PiHole with a single command:

	apt-get update && apt-get upgrade -y && apt-get autoremove && apt install curl -y && curl -sSL https://install.pi-hole.net | bash

To access the WebUI, go to http://ipaddr/admin

To get PiHole to update at regular intervals, go to the PiHole Terminal and edit your crontab file by running:

	crontab -e
	
Then paste in the below:

	30 2 * * * pihole -up
	
This will get PiHole to automatically update every morning at 2:30AM.  Use crontab.guru to help change the first 5 values of that line to suit your needs.
