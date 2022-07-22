This guide is compiled from several sources, but mostly the official github page here: https://github.com/pawelmalak/flame/wiki/Installation-without-docker

I can't recommend this dashboard.  It seems like there's a lot of deprecated packages that it's built on, and Heimdall and Organizr both run so well that I recommend those over Flame.

Start with a new Debian CT, and let's update everything and install some prerequisites (this part might take some time):

	apt update && apt upgrade -y && apt autoremove -y
	curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
	apt install nodejs
	apt install git npm mkdirp -y
	npm install -g npm@8.15.0
	apt update && apt upgrade -y && apt autoremove -y

Now download the source code, cd into it, and then install some dependencies (this part might take some time):

	git clone https://github.com/pawelmalak/flame
	cd flame
	npm run dev-init
	
The installer seems old, and there are still plenty of vulnerabilities left, but to reduce the amount of deprecated packages is to run the fix, that you'll likely be prompted to do:
	
	npm audit fix
	
Build the client, create data and public directories in the project root and move all contents of client/build to the public directory:
	
	cd client
	npm run build
	mkdir ~/flame/data
	mkdir ~/flame/public
	mv ~/flame/client/build/* ~/flame/public

Now edit the .env file in the root directory:

	nano ~/flame/.env
