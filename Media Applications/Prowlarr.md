There's a great overall sript at wiki.servarr.com for installing Lidarr, Sonarr, Radarr, Prowlarr, Readarr, and Whisparr...

but I'll show the hands-on version here because the big install script doesn't create a CT per app

On a new Debian CT, let's check for updates and install some prereq's:

	apt update && apt upgrade -y && apt autoremove
	apt install sudo curl sqlite3
	apt update && apt upgrade -y && apt autoremove
	
Now we need to create a Prowlarr user, Prowlarr group, and a directory for Prowlarr to use later on.

	sudo adduser -m prowlarr
	sudo groupadd prowlarr
	sudo usermod -a -G prowlarr prowlarr
	mkdir /var/lib/prowlarr
	
Now download the correct binaries for your system.  I'm assuming you're using AMD64 architecture, but change as needed.

After download, you uncompress the files and move to /opt/

	wget --content-disposition 'http://prowlarr.servarr.com/v1/update/develop/updatefile?os=linux&runtime=netcore&arch=x64'
	tar -xvzf Prowlarr*.linux*.tar.gz
	sudo mv Prowlarr/ /opt
	
Now set the permissions:

	sudo chown prowlarr:prowlarr -R /opt/Prowlarr

Now configure to run as a service by creating a service file:

	nano /etc/systemd/system/prowlarr.service
	
....and copy/paste in the below:

	[Unit]
	Description=Prowlarr Daemon
	After=syslog.target network.target
	[Service]
	User=prowlarr
	Group=prowlarr
	Type=simple

	ExecStart=/opt/Prowlarr/Prowlarr -nobrowser -data=/var/lib/prowlarr/
	TimeoutStopSec=20
	KillMode=process
	Restart=on-failure
	[Install]
	WantedBy=multi-user.target
	
Now reload the systemd daemon, enable the service, and remove the downloaded tarball file to free up some space:

	systemctl -q daemon-reload
	systemctl enable --now -q prowlarr
	rm Prowlarr*.linux*.tar.gz
	
Now you should be up-and running with Prowlarr. Accessing the WebUI should be as simple as visiting http://ipaddr:9696
