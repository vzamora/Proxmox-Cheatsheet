Shamelessly stolenfrom: https://wiki.owenyoung.com/qbittorrent-setup-for-debian/

Create a new Debian container, and install sudo and run updates:
	
	apt install sudo && apt update && apt upgrade -y && apt autoremove

Then: Download, prep, build, and install qBit.
	
	wget -qO ~/qbittorrent-nox-static.sh https://git.io/qbstatic 
	chmod +x ~/qbittorrent-nox-static.sh

Prep:
	sudo ~/qbittorrent-nox-static.sh
Build:
	~/qbittorrent-nox-static.sh all
Install:
	sudo ~/qbittorrent-nox-static.sh install

Configure qBit:
	
	mkdir ~/.config/
	mkdir ~/.config/qBittorrent/ 
	touch ~/.config/qBittorrent/qBittorrent.conf
	nano ~/.config/qBittorrent/qBittorrent.conf

And Paste in the below:
	
	[LegalNotice]
	Accepted=true

	[Preferences]
	WebUI\Port=8080
	WebUI\HostHeaderValidation=false

Configure qBit as a service by creating the service file first:
	
	nano /etc/systemd/system/qbittorrent.service 

And then Paste in the below:
	
	[Unit]
	Description=qBittorrent-nox service
	Wants=network-online.target
	After=network-online.target nss-lookup.target

	[Service]
	Type=exec
	ExecStart=/usr/local/bin/qbittorrent-nox
	Restart=on-failure
	SyslogIdentifier=qbittorrent-nox

	[Install]
	WantedBy=multi-user.target

Then run the below to reload the services list, enable qbit, and check on the status:
	
	systemctl daemon-reload && systemctl enable --now qbittorrent.service && systemctl stop qbittorrent && systemctl start qbittorrent && systemctl restart qbittorrent && systemctl status qbittorrent

Now you can access the WebUI by visiting http://ipaddr:8080
