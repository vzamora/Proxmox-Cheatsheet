To install Home Assistant, you have to have Caddy set-up first, but then go to your Proxmox root console and run.
Be patient, there's a lot going on.  Thanks to tteck for putting the bash script together!!
	bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/vm/haos-vm-v3.sh)"

Open Home Assistant in the browser at the address specified.  Then, go to “Supervisor” and click “Add-On Store” at the top, and install “File Editor” Add-On.
Using the File Editor Add-On WebUI, edit the file /config/configuration.yaml and add the following lines:

	homeassistant:
	external_url: https://external.url.com
	Internal_url: http://192.168.1.420:8123 (replace the ip address with the client’s IP address)

	http:
		use_x_forwarded_for: true
		trusted_proxies:
	ip.address.of.caddy
