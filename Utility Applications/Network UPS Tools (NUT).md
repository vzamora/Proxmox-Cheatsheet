Firstly, I have to say that this is just my combining of [TechnoTim's](https://docs.technotim.live/posts/NUT-server-guide/) Guide and [TheSmartHomeBook's Guide](https://www.thesmarthomebook.com/2022/09/02/setting-up-monitor-your-ups-proxmox-home-assistant/)

Now, I'm installing this directly on my Proxmox host.  I probably shouldn't, and I should definitely probably use USB passthrough (detailed elsewhere in this repo), but both guides I found have me doing it directly on the Proxmox host, and I've had enough small issues with USB Passthrough to CTs that I've opted to go the basic-but-slightly-riskier route.  The good news is that nuking /etc/nut/*.* should be a pretty safe way to recover.  Plus, NUT is installed via apt, so "apt remove nut nut-client nut-server" should effectively finish the clean-up.

If you want to go the container route, then create a new Debian11-based LXC, follow my USB passthrough guide to give that LXC access to the UPS's USB cable, and then follow from here.

Start by plugging your UPS into your server via USB cable.  Typically it's USB-B to USB-A, for reference.

Next, let's find that device.

	lsusb
	
Should output something like this:

![image](https://user-images.githubusercontent.com/449075/231248768-16e86d4d-f558-409f-8e4c-dfe776022722.png)

My UPS is on Bus 004 Device 005.

To do an initial validation that NUT will work, I'd recommend the following (note, I've input "4:5" for [bus]:[device] in the below, because it's where my UPS is mounted).

	lsusb -v -s [bus]:[device]

You should see an output like this:

![image](https://user-images.githubusercontent.com/449075/231249512-88b294b1-f855-4937-b39b-335ba47c2b44.png)

If it looks like that, then NUT _should_ work fine once installed.

Next, we update (per usual) and then install NUT, NUT Client, and NUT Server:
	
	apt update && apt install -y nut nut-client nut-server

Now you're going to let NUT scan your server:

	nut-scanner -U
	
and you're looking for an output like this:

![image](https://user-images.githubusercontent.com/449075/231250506-b1c909de-8358-41a1-b2be-ad05d1379375.png)

The guides I've linked don't make a backup of the nut.conf file, but I'm going to just to be sure.  Then I'll edit it and add what I need.

	cp /etc/nut/nut.conf /etc/nut/nut_backup.conf && cp /etc/nut/ups.conf /etc/nut/ups_backup.conf
	nano /etc/nut/nut.conf
	
Change "Mode=none" to "Mode=netserver"  Now the other conf file:

	nano /etc/nut/ups.conf
	
and paste in the below, and repalce what you need got from nut-scanner -U above:

	pollinterval = 15
	maxretry = 3

	offdelay = 120
	ondelay = 240

	NOW_PASTE_HERE_YOUR_OUTPUT_FROM_NUT-SCANNER-U

To test, run the following command:

	upsdrvctl start
	
and you should get something that looks like this:

![image](https://user-images.githubusercontent.com/449075/231256205-14be6a85-c850-4186-ab5c-06e0d297b031.png)

Now backup your upsd.conf file, create a new one, and past in the below to get your server listening on the right ports:

	mv /etc/nut/upsd.conf /etc/nut/upsd_backup.conf
	nano /etc/nut/upsd.conf
	
		LISTEN 0.0.0.0 3493
		LISTEN :: 3493

Now it's time to set-up users:
	
	mv /etc/nut/upsd.users /etc/nut/upsd_backup.users
	nano /etc/nut/upsd.users
	
Clear the file and replace with:

	[upsadmin]
	password = insert.your.password.here
	actions = SET
	actions = FSD
	instcmds = ALL
	upsmon master

	[upsuser]
	password = insert.your.password.here
	upsmon slave

....and now we're setting up monitoring:

	mv /etc/nut/upsmon.conf /etc/nut/upsmon_backup.conf
	nano /etc/nut/upsmon.conf

and paste in the below:

	RUN_AS_USER root
	MONITOR apc@localhost 1 upsadmin insert.your.password.here master

	MINSUPPLIES 1
	SHUTDOWNCMD “/sbin/shutdown -h”
	NOTIFYCMD /usr/sbin/upssched
	POLLFREQ 4
	POLLFREQALERT 2
	HOSTSYNC 15
	DEADTIME 24
	MAXAGE 24
	POWERDOWNFLAG /etc/killpower

	NOTIFYMSG ONLINE “UPS %s on line power”
	NOTIFYMSG ONBATT “UPS %s on battery”
	NOTIFYMSG LOWBATT “UPS %s battary is low”
	NOTIFYMSG FSD “UPS %s: forced shutdown in progress”
	NOTIFYMSG COMMOK “Communications with UPS %s established”
	NOTIFYMSG COMMBAD “Communications with UPS %s lost”
	NOTIFYMSG SHUTDOWN “Auto logout and shutdown proceeding”
	NOTIFYMSG REPLBATT “UPS %s battery needs to be replaced”
	NOTIFYMSG NOCOMM “UPS %s is unavailable”
	NOTIFYMSG NOPARENT “upsmon parent process died – shutdown impossible”

	NOTIFYFLAG ONLINE SYSLOG+WALL+EXEC
	NOTIFYFLAG ONBATT SYSLOG+WALL+EXEC
	NOTIFYFLAG LOWBATT SYSLOG+WALL+EXEC
	NOTIFYFLAG FSD SYSLOG+WALL+EXEC
	NOTIFYFLAG COMMOK SYSLOG+WALL+EXEC
	NOTIFYFLAG COMMBAD SYSLOG+WALL+EXEC
	NOTIFYFLAG SHUTDOWN SYSLOG+WALL+EXEC
	NOTIFYFLAG REPLBATT SYSLOG+WALL
	NOTIFYFLAG NOCOMM SYSLOG+WALL+EXEC
	NOTIFYFLAG NOPARENT SYSLOG+WALL

	RBWARNTIME 43200
	NOCOMMWARNTIME 600

	FINALDELAY 5
	
Next we set up the scheduling:
	
	mv /etc/nut/upssched.conf /etc/nut/upssched_backup.conf
	nano /etc/nut/upssched.conf

And paste in this:

	CMDSCRIPT /etc/nut/upssched-cmd
	PIPEFN /etc/nut/upssched.pipe
	LOCKFN /etc/nut/upssched.lock

	AT ONBATT * START-TIMER onbatt 30
	AT ONLINE * CANCEL-TIMER onbatt online
	AT ONBATT * START-TIMER earlyshutdown 30
	AT LOWBATT * EXECUTE onbatt
	AT COMMBAD * START-TIMER commbad 30
	AT COMMOK * CANCEL-TIMER commbad commok
	AT NOCOMM * EXECUTE commbad
	AT SHUTDOWN * EXECUTE powerdown
	AT SHUTDOWN * EXECUTE powerdown

Now, scheduling commands

	nano /etc/nut/upssched-cmd

paste this in:

	#!/bin/sh
	 case $1 in
		   onbatt)
			  logger -t upssched-cmd "UPS running on battery"
			  ;;
		   earlyshutdown)
			  logger -t upssched-cmd "UPS on battery too long, early shutdown"
			  /usr/sbin/upsmon -c fsd
			  ;;
		   shutdowncritical)
			  logger -t upssched-cmd "UPS on battery critical, forced shutdown"
			  /usr/sbin/upsmon -c fsd
			  ;;
		   upsgone)
			  logger -t upssched-cmd "UPS has been gone too long, can't reach"
			  ;;
		   *)
			  logger -t upssched-cmd "Unrecognized command: $1"
			  ;;
	 esac
	 
Now, we're really coming in to the final stretch!!  We have to make it executable....because why wouldn't we?

	chmod +x /etc/nut/upssched-cmd

That’s it. Now you can either reboot the system --- which both guides suggest, but mine is a pain to reboot, so, I just run through the following commands (pause after each upsdrvctl command, they take a moment).

	service nut-server restart
	service nut-client restart
	systemctl restart nut-monitor
	upsdrvctl stop
	upsdrvctl start

Now, you can test to validate it's working.  Below, your server name in the above command is whatever you called it in the ups.conf file.  In my case, it's "nutdev1"

	upsc [your.server.name]@localhost

You should get an output that has a bunch of criteria names (battery.charge, battery.charge.low, battery.charge.warning, etc) and values for each, and then no real "failures" showing up.  If you have any failures, go back and see what happened.

[TechnoTim's Ultimate Guide]{https://www.youtube.com/watch?v=vyBP7wpN72c) is absolutely awesome, and worth checking out.

___________________________________________________________________________________________________________________________________

Now, linking to HomeAssistant:

You simply add the "Network UPS Tools (NUT)" Integration.  When you click to add it, it will ask what the address is, plus the username and password.

Since I installed it on my Proxmox host, the "127.0.0.1" or "localhost" address is wrong, and it won't autofill.  Use the address of your Proxmox Host (or LXC) as the Host address.

My username was "upsadmin" as defined in the /etc/nut/upsd.users file, and my password is the same as what I typed into that file.  Port should come filled in with 3493, which is correct unless you changed it in the earlier steps.

![image](https://user-images.githubusercontent.com/449075/231262501-fcfa8eee-5b92-4040-be0e-d1b9aad8cd8b.png)

That's it.  You should have it on Home Assistant and have everything

