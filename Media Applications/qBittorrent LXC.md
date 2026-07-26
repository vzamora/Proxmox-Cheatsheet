Create a new Debian container, and install sudo and run updates:
	
	apt install sudo && apt update && apt upgrade -y && apt autoremove

Then install qbittorrent-nox using the provided/official script:

	bash <(curl -sL usrdx.github.io/s/qi.bash) #-lt v1

Note, "-lt v1" flag can be added if you want to install the libtorrent v1.2 package version of qBit.  Without the flag, you'll install the default libtorrent v2 package.

To update the container, you just stop the container, run the script, and restart it.

	systemctl stop qbittorrent.service
	bash <(curl -sL usrdx.github.io/s/qi.bash)
	
If you installed via the old guide, below, you'll need to change one thing when you upgrade, so ignore if you installed via script, but:

	nano /etc/systemd/system/qbittorrent.service

and update the "ExecStart=" line to be "ExecStart=/root/bin/qbittorrent-nox"

Then reload the daemon and restart the service and you're good-to-go:

	systemctl daemon-reload
	systemctl restart qbittorrent.service

Now you can access the WebUI by visiting http://ipaddr:8080

________________________________________________________________________________
All of the below is deprecated now.

<del>
Shamelessly stolenfrom: https://wiki.owenyoung.com/qbittorrent-setup-for-debian/

Create a new Debian container, and install sudo and run updates:
	
	apt install sudo && apt update && apt upgrade -y && apt autoremove

Then: Download, prep, build, and install qBit.
	
	wget -qO ~/qbittorrent-nox-static.sh https://git.io/qbstatic 
	chmod +x ~/qbittorrent-nox-static.sh

#Prep:

	sudo ~/qbittorrent-nox-static.sh
#Build:
	
	~/qbittorrent-nox-static.sh all

#Install:
	
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
</del>
