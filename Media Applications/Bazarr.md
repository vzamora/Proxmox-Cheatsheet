In a new Debian LXC, update then install prerequisites:

	apt update && apt upgrade -y && apt-get install libxml2-dev libxslt1-dev python3-libxml2 python3-lxml unrar-free ffmpeg libatlas-base-dev python3-pip unzip

Then download and unzip the latest bazarr
	
	wget https://github.com/morpheus65535/bazarr/releases/latest/download/bazarr.zip
	unzip -q bazarr.zip -d /opt/bazarr

cd to the folder of you unzipped bazarr into and install the requirements per the requirements.txt file:
	
	cd /opt/bazarr
	python3 -m pip install -r requirements.txt

Start Bazarr with:
	
	python3 bazarr.py
___________________________________________________________________________
To make Bazarr a service, create a new service file by:

	nano /etc/systemd/system/bazarr.service

And then copy/pasting the following in:

	[Unit]
	Description=My test service
	After=multi-user.target

	[Service]
	Type=simple
	Restart=always
	ExecStart=/usr/bin/python3 /opt/bazarr/bazarr.py

	[Install]
	WantedBy=multi-user.target

Then hit Ctrl+, Y, Enter to save, confirm, and quit nano.  Then you need to enable, start, and check on the service:
	
	systemctl -q daemon-reload && systemctl enable --now bazarr.service && systemctl status bazarr.service

To access Bazarr's WebUI, visit http://ipaddr:6767
