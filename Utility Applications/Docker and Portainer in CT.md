To install Docker and Portainer: (stolen shamelessly from docs.docker.com)

Create a privileged Debian CT but DO NOT START it.  Before you start it, you need to enable Nesting and Fuse, which can be done in the GUI or via CLI.

To do it in the GUI, select the newly created Docker/Portainer CT and select "Options" in the second column.

Select the "Features" row and click the "Edit" button across the top.  Now select "Nesting" and select "Fuse" and hit "OK"


To do it in CLI, go to your Proxmox Host Console and run the below, where ???? is the CT number.
	
	nano /etc/pve/lxc/????.conf

Next, add this line(s) to the bottom of the config file:
	
	features: nesting=1
	features: fuse=1
	
Now that Nesting has been enabled, start the container and do the usual updating before you start:
	
	apt update && apt upgrade -y && apt autoremove

Then install some prereqs and add Docker’s official GPG key:
	
	apt install -y ca-certificates curl gnupg lsb-release fuse-overlayfs && \
	apt update && apt upgrade -y && apt autoremove -y && \
	curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

Now use the following command to set up the stable repository.
	
	echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null

Now install Docker Engine:
	
	apt update && apt install -y docker-ce docker-ce-cli containerd.io

Now to install Portainer, you create the volume and then download and install the Portainer Server container:
	
	docker volume create portainer_data
Then:

	docker run -d -p 8000:8000 -p 9000:9000 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data cr.portainer.io/portainer/portainer-ce:latest
	    
	    
Now you have to modify a file to enable overlayfs by opening the file in nano and changing a line:

	nano /etc/docker/daemon.json
	
....and set "storage-driver" to "fuse-overlayfs."

Now restart the container.

To access the Portainer WebUI, go to http://ipaddr:9443/
