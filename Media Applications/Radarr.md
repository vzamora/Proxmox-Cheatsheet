To install Radarr:
Install sqlite3, a prereq for Radarr:

	apt-get update && apt-get upgrade -y && apt-get autoremove && apt install curl mediainfo sqlite3 -y

Download the latest file

	wget --content-disposition 'http://radarr.servarr.com/v1/update/master/updatefile?os=linux&runtime=netcore&arch=x64'

Uncompress the tarball

	tar -xvzf Radarr*.linux*.tar.gz

Move the files to /opt:

	mv Radarr /opt
	adduser radarr
	chown radarr:radarr -R /opt/Radarr
____________________________________________________________________________
To make Radarr a service and start on boot
Create a new document by:
	
	nano /etc/systemd/system/radarr.service

Then add the following information into the blank document:

	[Unit]
	Description=Radarr Daemon
	After=syslog.target network.target
	[Service]
	Type=simple
	ExecStart=/opt/Radarr/Radarr -nobrowser -data=/var/lib/radarr/
	TimeoutStopSec=20
	KillMode=process
	Restart=always
	[Install]
	WantedBy=multi-user.target

Then refresh your Daemon List:

	systemctl -q daemon-reload

Then start the service and verify by running:
	
	systemctl enable --now radarr.service

	systemctl status radarr.service

Now you can access the WebUI by going to: http://ipaddr:7878
