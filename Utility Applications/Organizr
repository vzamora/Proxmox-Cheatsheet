To install Organizr manually (shamelessly stolen from Organizr.app docs page at https://docs.organizr.app/installation/installing-organizr)

Create a new Debian Container, and then install git & sudo and do an update and clean-up:
	apt-get install git sudo -y && apt-get update && apt-get upgrade -y && apt-get autoremove

Then download the installer, navigate to the install location, and run the installer:
	git clone https://github.com/elmerfdz/OrganizrInstaller /opt/OrganizrInstaller
	cd /opt/OrganizrInstaller/ubuntu/oui
	sudo bash ou_installer.sh

When the installer comes up, you’ll be greeted with a message.  You’ll want to select “Option 4” if this is a new container you’re installing Organizr on.

The next prompts, I’d suggest:
	2
	2a
	organizr.local
	(leave blank)
	LE
	y
	(any key)
	S

I think I'm missing a step here and haven't told the webserver which files to use as the website.  I can't get this to work now, although I have in the past.
