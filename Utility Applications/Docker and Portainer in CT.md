To install Docker and Portainer: (stolen shamelessly from docs.docker.com)

Create a privileged Debian 11 CT but DO NOT START it.

First, go to your Proxmox Host Console and run the below, where ???? is the CT number.
	nano /etc/pve/lxc/????.conf

Next, add this line(s) to the bottom of the config file:
	features: nesting=1
	
Now start the container and do the usual updating before you start:
	apt-get update && apt-get upgrade -y && apt-get autoremove

Then install some prereqs and add Docker’s official GPG key:
	apt-get install ca-certificates curl gnupg lsb-release
	apt-get update && apt-get upgrade -y && apt-get autoremove
	curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

Now use the following command to set up the stable repository.
	echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null

Now install Docker Engine:
	apt-get update
	apt-get install docker-ce docker-ce-cli containerd.io

Now to install Portainer, you create the volume and then download and install the Portainer Server container:
	docker volume create portainer_data

	docker run -d -p 8000:8000 -p 9443:9443 --name portainer \
	    --restart=always \
	    -v /var/run/docker.sock:/var/run/docker.sock \
	    -v portainer_data:/data \
	    cr.portainer.io/portainer/portainer-ce:2.9.3

To access the Portainer WebUI, go to http://ipaddr:9443/
