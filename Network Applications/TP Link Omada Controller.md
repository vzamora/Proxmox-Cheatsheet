Start by installing Java8 and MongoDB v<4, as prerequisites:

	apt install gnupg
	apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 8AC3B29174885C03
	add-apt-repository --yes https://adoptopenjdk.jfrog.io/adoptopenjdk/deb/

If you get an error, run: apt-get install software-properties-common then try again

	apt update
	apt install adoptopenjdk-8-hotspot
	
To check that you have the right version installed, run:
	
	java -version
	
Should output openJDK version 1.8.X.  If it doesn’t, you probably have another java installed.

	update-alternatives --config java
	
Then download and install mongodb server
	
	wget https://repo.mongodb.org/apt/debian/dists/stretch/mongodb-org/3.6/main/binary-amd64/mongodb-org-server_3.6.23_amd64.deb 
	dpkg -i mongodb-org-server_3.6.23_amd64.deb

Then download and install the Omada SDN Software, substituting the link and version numbers below for the latest values:
	
	wget https://static.tp-link.com/software/2021/202107/20210701/Omada_SDN_Controller_v4.4.3_linux_x64.deb

	dpkg -i Omada_SDN_Controller_v4.4.3_linux_x64.deb

Check for errors, sometimes you need to run:
	
	apt install curl
	apt install jsvc
	apt --fix-broken install

You should get an output that looks like:

	==========================
	Omada Controller will start up with system boot. You can also control it by [/usr/bin/tpeap]. 
	check omada
	Starting Omada Controller. Please wait............................................
	Started successfully.
	You can visit http://localhost:8088 on this host to manage the wireless network.
	========================

Now you can visit the WebUI by going to http://ipaddr:8088
