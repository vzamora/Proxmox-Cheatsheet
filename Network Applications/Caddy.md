Create a new Debian LXC, and run the below  to get a clean slate to start from.
	
	apt-get update && apt-get upgrade -y
	
Now install the keyring, add the repo keys, update repo sources, and install caddy.
	
	apt install -y debian-keyring debian-archive-keyring apt-transport-https
	curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | tee /etc/apt/trusted.gpg.d/caddy-stable.asc
	curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | tee /etc/apt/sources.list.d/caddy-stable.list
	apt update
	apt install caddy
	
____________________________________________________________________________________________________________________

Each program may need its own specific Caddy setup, but here's an example Caddy file I have created for many of the LXCs in the Proxmox-Cheatsheet overall project.

To create it, run the following:
	
	nano /etc/caddy/Caddyfile
	
Then paste in the below (obviously editing the IP addresses, domain, and/or ports as needed):
	
	radarr.domain.org {
		reverse_proxy 192.168.1.XXX:7878 {
		}
	}

	sonarr.domain.org {
		reverse_proxy 192.168.1.XXX:8989 {
		}
	}

	plex.domain.org {
		reverse_proxy 192.168.1.XXX:32400 {
		}
	}

	nextcloud.domain.org {
		reverse_proxy 192.168.1.XXX:443 {
			transport http {
				tls_insecure_skip_verify
			}
		}
		header {
			Strict-Transport-Security max-age=31536000;
		}
		redir /.well-know/webfinger /public.php?service=webfinger 301
	}

	hassos.domain.org {
		reverse_proxy http://192.168.1.XXX:8123 {
		}
	}

	Overseerr.domain.org {
		reverse_proxy 192.168.1.XXX:5055 {
		}
	}

	dashboard.domain.org {
		reverse_proxy 192.168.1.XXX:8989 {
		}
	}

	heimdall.domain.org {
		reverse_proxy 192.168.1.XXX {
		}
	}

	passwords.domain.org {
		reverse_proxy 192.168.1.XXX:8000 {
		}
	}

	guac.domain.org {
		reverse_proxy 192.168.1.XXX {
		}
	}

Now navigate to the Caddyfile and reload Caddy to make it work by running:
	
	cd /etc/caddy
	caddy reload
