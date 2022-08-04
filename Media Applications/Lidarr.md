There's a great overall sript at wiki.servarr.com for installing Lidarr, Sonarr, Radarr, Prowlarr, Readarr, and Whisparr...

but I'll show the hands-on version here because the big install script doesn't create a CT per app

On a new Debian CT, let's check for updates and install some prereqs:

	apt install curl mediainfo sqlite3 libchromaprint-tools

Now you have to create a user called "lidarr" and create a home directory for it:

	adduser \
		--system \
		--disabled-password \
		--group media\
		--home /var/lib/lidarr \
		lidarr

Now download, uncompress, and move to /opt/:

	wget --content-disposition 'http://lidarr.servarr.com/v1/update/master/updatefile?os=linux&runtime=netcore&arch=x64'
	tar -xvzf Lidarr*.linux*.tar.gz
	mv Lidarr/ /opt
	
This next part assumes you created the user "lidarr" as above, but you can change "lidarr:media" to "root:root" if you'd like, even though it's less secure.
We're changing ownership of the directory for permissions.

	chown -R lidarr:media /opt/Lidarr

Now that Lidarr is installed, we'll make configure a systemd file to let it run in the background and start at boot:

	nano /etc/systemd/system/lidarr.service
	
And paste in the below, then exit by hitting "CTRL+X", "Y", "Enter"

	[Unit]
	Description=Lidarr Daemon
	After=syslog.target network.target
	[Service]
	User=lidarr
	Group=media
	Type=simple

	ExecStart=/opt/Lidarr/Lidarr -nobrowser -data=/var/lib/lidarr/
	TimeoutStopSec=20
	KillMode=process
	Restart=on-failure
	[Install]
	WantedBy=multi-user.target
	
Then reload the daemon, enable the service, and check the status.

	systemctl -q daemon-reload
	systemctl enable --now -q lidarr
	
And remove the tarball for file space savings:
	
	rm Lidarr*.linux*.tar.gz
