On install, look up how to get rid of the Enterprise repositories or PVE won’t update.
As of PVE V7, go to “Updates > Repositories” and click on the “Enterprise” repo (usually the last one) and then the “Disable” button then “Add” then select the “No subscription” repository.

You might need to run “pveam update” to update the list of container/VM Templates.
