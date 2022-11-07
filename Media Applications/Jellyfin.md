This guide is borrowing heavily from the official documentation at: https://jellyfin.org/docs/general/administration/installing.html#debian

If you're not on a supported architecture (amd64, arm64, armhf), then it may be required to do the full build and install found here: https://jellyfin.org/docs/general/administration/installing.html#linux-generic-amd64

In a new Debian CT, let's start by running a command to get everything up-to-date as a clean slate to start from, plus a couple of dependencies.

This step takes a little longer than the absolutely minimum required, but I like doing it to ensure things are fine.  Shouldn't be more than a couple of additional minutes.

	apt update && apt upgrade -y && apt autoremove -y && apt install -y sudo apt-transport-https gnupg lsb-release curl && apt update && apt upgrade -y && apt autoremove -y

Jellyfin is already supported on Debian Buster/Bullseye by the Jellyfin team, so:
	
	sudo apt install -y extrepo && sudo extrepo enable jellyfin
	
Now, update again and install Jellyfin:

	sudo apt update && sudo apt install -y jellyfin

Lastly, check on the status of Jellyfin:
	
	sudo service jellyfin status

Now you should be up-and running with Jellyfin.  Accessing the WebUI should be as simple as visiting http://ipaddr:8096

The best thing to do is to bind-mount your media storage to this container, and then point Jellyfin at that bind mount.
