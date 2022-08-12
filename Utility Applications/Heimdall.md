Create a new LXC (I used Ubuntu, but Debian should work).

Go to “github.com/linuxserver/Heimdall/” and look for the link to the latest .tar.gz file and replace the link in the command below with that.
If not, you’ll probably just have to update immediately after install.
	
	wget https://github.com/linuxserver/Heimdall/archive/refs/tags/2.4.12.tar.gz
	tar xvzf v2.*.tar.gz
	apt install php php-sqlite3 php-zip -y && apt-get update && apt-get upgrade -y && apt-get autoremove
	cd Heimdall*/
	php artisan serve

If you get an error (something about ArrayInput.php line 135), you have to fix it by:
Changing directories to find a file, edit the file by commenting out two lines of code, and then save and quit out of it to “apply” the changes, and then run a command to actually start the server service.

	nano ./vendor/symfony/console/Input/ArrayInput.php

Use ctrl+_ to “Jump To Line” and enter 135.  The two lines you have to comment out are as below:

	//} elseif ('-' === $key[0] {
	//	$this->addShortOption(substr($key,1), $value);

Then reun the below line (Note: I had to run this twice for it to take)

	php artisan serve

Now you can find Heimdall at the IP Address of the LXC (which you can find with ip addr show)

It looks like I'm missing a step here and nothing has told php to use the Heimdall folder/files as the site.
__________________________________________________________________________________________________________________

To add apps, download “App Requests” (Enhanced or Foundation) from https://apps.heimdall.site/request then unzip and open WinSCP (or similar) and connect to Heimdall.

“Upload” the extracted files to /opt/heimdall/app/SupportedApps/ then go to /opt/Heimdall in Terminal and run php artisan register:app all OR php artisan register:app APPNAME
Then click the “Update App List” button when adding a new shortcut on Heimdall before adding the new app and you should be able to find it no problem.
