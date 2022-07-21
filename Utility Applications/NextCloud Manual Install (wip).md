To install NextCloud manually:

Create a new Debian LXC, and run the usual apt-get update && apt-get upgrade -y to get a clean slate to start from.

First, we need to install PHP and a bunch of php modules.  Note, php-libxml must be >=2.7.0.
	apt install apache2 mariadb-server libapache2-mod-php7.4 sudo php

	apt install php-ctype php-curl php-dom php-iconv php7.4-mbstring php-posix php7.4-zip php-fileinfo php-bz2 php-intl php-ldap php-ftp php-imap php7.4-bcmath php-exif php-apcu php-memcached php-redis zlib1g-dev libgd-dev php-mysql sudo php7.4-gd php7.4-mysql php7.4-curl php7.4-mbstring php7.4-xml php7.4-intl php7.4-gmp php7.4-imagick

I think I’m missing:php-libxml, php-openssl, but I'm not sure.

Now you’re going to start mariaDB and start into the database setup.
	/etc/init.d/mariadb start
	mariadb -uroot -p

Now you set a password for mariaDB.

Now some user creation, database creation, and privileges stuff.  Note: Caps matter below, and the password being created below is actually “password”
Leave the single quotes in place but PLEASE change the password to something useful.
	CREATE USER ‘admin’@’localhost’ IDENTIFIED BY ‘password’; 
	CREATE DATABASE IF NOT EXISTS nextcloud CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;
	GRANT ALL PRIVILEGES ON nextcloud.* TO 'admin'@'localhost';
	FLUSH PRIVILEGES;
	quit;

Go to the Nextcloud Download Page and wget the link for either the tar.bz2 or .zip archives.  I’m using the tar.bz2 format.

Then you’re extracting it and copying the new directory to your Apache HTTP server.
	wget https://download.nextcloud.com/server/releases/nextcloud-24.0.2.tar.bz2
	tar -xjvf nextcloud-24.*.tar.bz2 (or “unzip nextcloud-x.y.z.zip”)
	cp -r nextcloud /var/www/html

Now it’s a matter of telling Apache to use the nextcloud site instead of the default landing page.  You create a file with the first line, and paste in the next paragraph and ctrl+x, y, enter to save and exit.
	nano /etc/apache2/sites-available/nextcloud.conf

And paste-in the below:
	Alias /nextcloud "/var/www/nextcloud/"

	<Directory /var/www/nextcloud/>
	Require all granted
	AllowOverride All
	Options FollowSymLinks MultiViews

	<IfModule mod_dav.c>
	Dav off
	</IfModule>
	</Directory>

Now some additional Apache configurations and cleanup:
	a2ensite nextcloud.conf
	a2enmod rewrite
	a2enmod headers
	a2enmod env
	a2enmod dir
	a2enmod mime
	systemctl reload apache2
	chown -R www-data:www-data /var/www

Now use a browser to navigate to the IP Address of that container (command is “ip addr show” to find it) and use the GUI to set it all up.
Note: You MUST enable “External Storage Support” under “Your apps” if you want to let Nextcloud see your bind-mount that I suggest you attach to the LXC.
