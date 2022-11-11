In a new Debian LXC, create a user "jackett" by running the following:

	adduser \
		--system \
		--gecos "jackett service" \
		--disabled-password \
		--group \
		--home /var/lib/jackett \
		jackett

Now extract and copy the release to a temp directory (updating the wget link as necessary):

	cd `mktemp -d`
	wget https://github.com/Jackett/Jackett/releases/download/v0.20.1300/Jackett.Binaries.LinuxAMDx64.tar.gz
	tar zxvf *.tar.gz

Copy the binaries to the user home and chown the directory:

	cp -r ./Jackett /var/lib/jackett
	chown -R jackett:jackett /var/lib/jackett/Jackett
	
Now run the script:
	
	cd /var/lib/jackett/Jackett
	./install_service_systemd.sh

Verify the service is running:
	
	systemctl status jackett
	
Now you can access the WebUI via http://ipaddr:9117/UI/Dashboard
