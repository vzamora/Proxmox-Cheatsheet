To Install OctoPrint:

Create a new VM on Debian.  I’m doing this with no GUI for a more lightweight install, similar to what would be a Debian 11 “Server” distro.
Deselect the GUI during install, but install SSH.  I’m using a 16GB hard drive, 2GB ram, and 2 cores.  Should be overkill compared to a Pi.
Create a second user called “pi” - the installer should walk you through that.  Initially, log in as root.

Make sure you have Python3 installed by running:

	python3 --version
	
If not, install using apt-get install python3

Make user ‘pi’ a sudo, and install sudo

	usermod -aG sudo pi
	apt-get install sudoer
	logout

Then, log in as user ‘pi’ and run:
	
	cd ~
	sudo apt update
	sudo apt install python3-pip python3-dev python3-setuptools python3-venv git libyaml-dev build-essential
	mkdir OctoPrint && cd OctoPrint
	python3 -m venv venv
	source venv/bin/activate

Now you have all of the prereqs and have started creating the system, and you’ve created a Virtual Environment (venv) for OctoPrint to run in.  Now time to install OctoPrint
	
	pip install pip --upgrade
	pip install octoprint

Now, to make sure the user ‘pi’ can dialout AND use serial ports, add it to those groups by:
	
	sudo usermod -a -G tty pi
	sudo usermod -a -G dialout pi

Now exit the venv by running:
	
	deactivate
	
Then run the following to create the service, and then enable the service:
	
	wget https://github.com/OctoPrint/OctoPrint/raw/master/scripts/octoprint.service && sudo mv octoprint.service /etc/systemd/system/octoprint.service
	sudo systemctl enable octoprint.service

Now you can access the WebUI by visiting http://ipaddr:80
