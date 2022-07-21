Run the following line just to initialize/upgrade and automatically remove bloat, plus install some dependencies.

	apt-get update && apt-get upgrade -y && apt-get autoremove && apt-get install sudo gnupg2 -y

This is adding a key to your keyserver, allowing sonarr to be accepted as an install source.

	sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 2009837CBFFD68F45BC180471F4F90DE2A9B4BF8
  	echo "deb https://apt.sonarr.tv/debian buster main" | sudo tee /etc/apt/sources.list.d/sonarr.list

Now to update repositories, install sonarr, update again, and then remove bloat again.  A little unnecessary, but minor and makes life easier.
	
	sudo apt update && apt install sonarr -y && apt-get update && apt-get upgrade -y && apt-get autoremove

Without getting into major permissions issues, just set the user and group to “root” when prompted, NOT “sonarr”
This isn't the safest way, and there's better options here, but I know it works.  I'll try to update the "safest" way of going about this later.

Now you can go to the Sonarr WebUI by going to http://ipaddr:9898
