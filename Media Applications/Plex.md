To install Plex, go to the Plex Downloads Page (https://www.plex.tv/media-server-downloads/#plex-media-server) and copy the download link for the latest release for Debian.
The example below will use the latest link at the time of creating this, but replace the URL with the latest one.

	wget https://downloads.plex.tv/plex-media-server-new/1.32.5.7349-8f4248874/debian/plexmediaserver_1.32.5.7349-8f4248874_amd64.deb
	
Then you can type in the beginning of the next command, such as “dpkg -i pl” and hit “tab” and it’ll autocomplete to the latest.  In this example, it’s:
	
	dpkg -i plexmediaserver_*.deb
	
Then you can check the status with:
	
	systemctl status plexmediaserver

You can start it with:
	
	systemctl start plexmediaserver

Run this to allow updates: 
	
	apt install gnupg gnupg1 gnupg2 curl -y
 	echo deb https://downloads.plex.tv/repo/deb public main | sudo tee /etc/apt/sources.list.d/plexmediaserver.list
	curl https://downloads.plex.tv/plex-keys/PlexSign.key | apt-key add -

To visit the Plex WebUI, visit: http://ipaddr:32400/web/index.html#!/
