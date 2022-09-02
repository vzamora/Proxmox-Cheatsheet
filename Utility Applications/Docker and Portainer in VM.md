Create a new Ubuntu-based VM, I'm using Lubuntu for the lower-weight UX and same overall output.

Install some necessary dependencies:
	
	sudo apt-get update && apt-get upgrade -y && apt-get autoremove
	
	sudo apt-get install apt-transport-https ca-certificates curl gnupg lsb-release

Now add the repository by adding the Docker GPG key and then adding the repository:

	sudo apt-get install curl

	curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
	
	echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

And now it's time to install Docker!

	sudo apt-get update
	
	sudo apt-get install docker-ce docker-ce-cli containerd.io -y
	
Now add your user to the Docker group.  $USER below is the username you want to add to the Docker group.  In most cases, it'll be everything you see before the "@" symbol in your Terminal

	sudo usermod -aG docker $USER
	
Now check that it installed correctly by running the below.  You should get an output telling you the version number:

	docker version
	
If you get an error talking about "Got permission denied while trying to connect to the docker daemon socket at unix:...." at the bottom of your version output,
check to make sure you're in the right group by running:

	grep docker /etc/group
	
and checking for docker:<zero-width space>x:998:$USER.  As long as that's there, you can run:

	newgrp docker
	
Now try and run "docker version" again and see if you get a slightly longer output with no error at the bottom.
	
____________________________________________________________________________________

Now we can get on to Portainer.

Firstly, you'll want to create a database location

	docker volume create portainer_data
	
Then download and install the Portainer Server Container.  I'm downloading the Community Edition, below:

	docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
	
This will take a while, but be worth it.  It'll look for the files locally, fail, then go to the internet to find them. It'll install and run.

Now see if Docker has any containers running:

	docker ps

and you should get an ouptut that lists "portainer/portainer-ce:latest" as an image that's running.

Now you can log into your Portainer GUI on your Lubuntu VM by visiting https://localhost:9443 OR on another machine by going to https://ip.of.lubuntu.vm:9443
