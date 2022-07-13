On install, look up how to get rid of the Enterprise repositories or PVE won’t update.
As of PVE V7, go to “Updates > Repositories” and click on the “Enterprise” repo (usually the last one) and then the “Disable” button then “Add” then select the “No subscription” repository.

You might need to run “pveam update” to update the list of container/VM Templates.

____________________________________________________________________________

To create an automated script to Update all of your LXC Containers daily at 2:00am:

In your Proxmox Host console, start by creating a new bash file named ‘autoupdate.sh’ by running:
        nano /opt/autoupdate.sh

And copy/paste in the below code.  It’ll start any off containers, update them, and then shut them down.  It’ll leave on but update any containers that are running.

        #!/bin/bash
        # update all containers

        # list of container ids we need to iterate through
        containers=$(pct list | tail -n +2 | cut -f1 -d' ')

        function update_container() {
        container=$1
        echo "[Info] Updating $container"
        # to chain commands within one exec we will need to wrap them in bash
        pct exec $container -- bash -c "apt update && apt upgrade -y && apt autoremove -y"
        }

        for container in $containers
        do
        status=`pct status $container`
        if [ "$status" == "status: stopped" ]; then
          echo [Info] Starting $container
          pct start $container
          echo [Info] Sleeping 5 seconds
          sleep 5
          update_container $container
          echo [Info] Shutting down $container
          pct shutdown $container &
        elif [ "$status" == "status: running" ]; then
          update_container $container
        fi
        done; wait

Now use Ctrl+x, Y, and Enter to close, save, and exit the “nano” text editor.

In your console, you can run /bin/bash /opt/autoupdate.sh and watch it run once to make sure it works….but this is optional.
Still in your Proxmox host console, enter: crontab -e to get into your cronjobs file.
Select “nano” if it asks, as “vim” is pretty complicated and offers little/no benefit.

At the bottom, create a new line that simply says:

        0 2 * * * /bin/bash /opt/autoupdate.sh

Then simply use Ctrl+x, Y, and Enter to close, save, and exit.  You’re all set.
