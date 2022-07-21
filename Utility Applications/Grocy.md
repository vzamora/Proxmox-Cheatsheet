Create a clean Debian LXC
	apt update && apt full-upgrade -y && apt install -y nginx sqlite3 php-fpm php-sqlite3 php-gd php-intl unzip
	wget https://releases.grocy.info/latest
	unzip latest -d /var/www/html

Note: you might have to “mkdir” /var/www/html and/or /var/www/html/data
	chown -R www-data:www-data /var/www
	cp /var/www/html/config-dist.php /var/www/html/data/config.php
	
Now edit the fastcgiparams file by running:
	nano /etc/nginx/conf.d/fastcgi_params

....and paste in the following to the new file, then ctrl+x, y, enter to save:
	include fastcgi_params;
	fastcgi_split_path_info ^(.+?\.php)(|/.*)$;
	fastcgi_pass unix:/var/run/php/php7.3-fpm.sock; 

Now edit the nginx available sites list by running:
	nano /etc/nginx/sites-available/default

.....and paste in the following and delete everything else, then ctrl+x, y, enter to save (or run “rm /etc/nginx/sites-available/default” to delete it and then create new using the nano command above):
	server {
		listen 80 default_server;
		root /var/www/html/public;
		index index.html index.htm index.nginx-debian.html index.php;
		server_name _;
		location / {
			try_files $uri /index.php;
		}
		location ~ \.php$ {
			include snippets/fastcgi-php.conf;
			fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
		}
	}

Now you can access the WebUI by going to http://ipaddr
