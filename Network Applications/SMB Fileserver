For an SMB FileServer:

The easiest way by a mile is to download the CT Template for the Turnkey Linux Filserver.

To do this, click on the “local” storage device on the far left of the Proxmox UI, then click on the “CT Templates” option in the second column of information.
Then click the “Templates” button across the top.  This will bring up a list of templates, and you can search through it for one called “turnkey-fileserver.”
If it doesn’t appear, try running “pveam update“ in the main Proxmox node shell and look again.

Once you’ve downloaded the template, click “Create CT” in the top-right corner and use the “turnkey-fileserver” template file to create a new LXC.
It doesn’t need much CPU, Memory, or Storage resources as it’s just letting Windows (and Linux, Mac, iOS, and Android via smb apps) see the storage location.

DO NOT START the container upon build.  First, click on the newly created LXC, select “Options”, then “Features” and enable “Nesting.”

Secondly, follow the instructions for mounting a ZFS dataset to the container.

NOW you can start the container, and use the WebUI (ipaddr then click “webmin” or ipaddr:12321) to get to the WebUI.

From there, it’s a matter of using the WebUI to set up the file sharing.  Most likely, you’ll want to go to “Servers > Samba Windows File Sharing” on the left-hand side, delete all of the suggested file shares, and then add a new file share to your bind-mounted storage device.

I strongly suggest you keep all of your data in Proxmox directly, and only use the File Server LXC for SMB sharing, and the best way to do that is via bind-mounting.
