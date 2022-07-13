To install Guacamole:

Create a new Debian LXC, then:
	apt update && apt upgrade -y
	apt install software-properties-common gnupg -y
	add-apt-repository ppa:remmina-ppa-team/freerdp-daily
	apt update && apt install freerdp2-dev freerdp2-x11 sudo -y
	adduser remotegod
	usermod -aG sudo remotegod
	systemctl reboot

Log into the container using the newly created user “remotegod”
	wget https://raw.githubusercontent.com/MysticRyuujin/guac-install/master/guac-install.sh
	chmod +x guac-install.sh
	sudo ./guac-install.sh

Answer “N” to TOTP and Duo, answer “Y” to mySQL.

You must provide passwords when prompted.

You can now access guacamole by using http://ipaddr:8080/guacamole
