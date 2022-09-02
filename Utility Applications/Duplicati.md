In a new Debian CT, run the following commands:

Initial update/upgrade/bloat remove:
	
	apt-get update && apt-get upgrade -y && apt-get autoremove

Install some requirements and add keys to allow Mono to install:
	
	apt install apt-transport-https dirmngr gnupg ca-certificates
	apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 3FA7E0328081BFF6A14DA29AA6A19B38D3D831EF

Enable the mono repo, update repos, and install mono.
	
	echo "deb https://download.mono-project.com/repo/debian stable-buster main" | tee /etc/apt/sources.list.d/mono-official-stable.list
 	apt update
 	apt install mono-complete (or mono-devel)

You can check to make sure it’s working by running:
	
	mono --version
________________________________________________________________________________________

Download Duplicati (update version number as needed)
	
	wget https://updates.duplicati.com/beta/duplicati_2.0.6.3-1_all.deb

Now run the installer:
	
	sudo apt-get install ./duplicati_2.*.deb -y

Create and edit the file /etc/systemd/system/duplicati.service
	
	sudo nano /etc/systemd/system/duplicati.service

In Nano, paste in the below:

	[Unit]
	Description=Duplicati web-server
	After=network.target

	[Service]
	Nice=19
	IOSchedulingClass=idle
	EnvironmentFile=-/etc/default/duplicati
	ExecStart=/usr/bin/duplicati-server $DAEMON_OPTS
	Restart=always

	[Install]
	WantedBy=multi-user.target	

Edit the file /etc/default/duplicati and add DAEMON_OPTS options to your liking:
	
	sudo nano /etc/default/duplicati

In Nano, paste in the below:
	
	# Defaults for duplicati initscript
	# sourced by /etc/init.d/duplicati
	# installed at /etc/default/duplicati by the maintainer scripts

	#
	# This is a POSIX shell fragment
	#

	# Additional options that are passed to the Daemon.
	DAEMON_OPTS="--webservice-interface=any --webservice-port=8200 --portable-mode”

Now enable, start, and check running status of the duplicati service:
	
	sudo systemctl enable duplicati.service
	sudo systemctl daemon-reload
	sudo systemctl start duplicati.service	
	sudo systemctl status duplicati.service

Now you can access the Duplicati WebUI by going to: http://ipaddr:8200/ngax/index.html#/
