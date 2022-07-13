The below guide is mostly following heinoldenhuis's guide from here: https://gist.github.com/heinoldenhuis/f8164f73e5bff048e76fb4fff2e824e1

Note: This requires a minimum of 2GB RAM to build, but you  can reduce the memory available to the LXC after you build it.
If you don't have at least 2GB RAM, the build will fail.

On a new Debian LXC, update and prep the required packages:
	apt update && apt upgrade -y && apt install sudo git nano curl wget htop pkg-config openssl libssl1.1 libssl-dev -y
	apt install build-essential -y
	
Now install Rust:
	curl https://sh.rustup.rs -sSf | sh
I suggest selecting Option 1 here, and just installing as suggested.
	echo 'export PATH=~/.cargo/bin:$PATH' >> ~/.bashrc
	export PATH=~/.cargo/bin:$PATH
	which rustc
		
Now install Node JS:
	curl -fsSL https://deb.nodesource.com/setup_16.x | sudo -E bash -
	sudo apt-get install -y nodejs
	npm -g install npm@7
	which npm
	npm i npm@latest -g

Now to build VaultWarden:
	git clone https://github.com/dani-garcia/vaultwarden && pushd vaultwarden
	cargo clean && cargo build --features sqlite --release
	file target/release/vaultwarden

And now the web-vault component:
	pushd target/release/
	git clone --recurse-submodules https://github.com/bitwarden/web.git web-vault.git && cd web-vault.git

Now patches:
	git checkout v2.28.0
	git submodule update --init --recursive
	wget https://raw.githubusercontent.com/dani-garcia/bw_web_builds/master/patches/v2.28.0.patch
	git apply v2.28.0.patch
	npm ci && npm audit fix || true && npm run dist:oss:selfhost
	
NEARLY THERE! Now just copy the build/ directory to Vaultwarden release/ as web-vault
	cp -a build ../web-vault
	
And, finally, run it:
	cd ..
	mkdir data
	./vaultwarden

To make this a systemd service, so it starts on boot and runs in the background, create a text file using nano:
	nano /etc/systemd/system/vaultwarden.service
	
And paste-in the below:
	[Unit]
	Description=Bitwarden Server (Powered by Vaultwarden)
	Documentation=https://github.com/dani-garcia/vaultwarden
	# If you use a database like mariadb,mysql or postgresql, 
	# you have to add them like the following and uncomment them 
	# by removing the `# ` before it. This makes sure that your 
	# database server is started before vaultwarden ("After") and has 
	# started successfully before starting vaultwarden ("Requires").


	# Only sqlite
	After=network.target

	# MariaDB
	# After=network.target mariadb.service
	# Requires=mariadb.service

	# Mysql
	# After=network.target mysqld.service
	# Requires=mysqld.service

	# PostgreSQL
	# After=network.target postgresql.service
	# Requires=postgresql.service


	[Service]
	# The user/group vaultwarden is run under. the working directory (see below) should allow write and read access to this user/group
	User=vaultwarden
	Group=vaultwarden

	# The location of the .env file for configuration
	EnvironmentFile=/etc/vaultwarden.env

	# The location of the compiled binary
	ExecStart=/usr/bin/vaultwarden

	# Set reasonable connection and process limits
	LimitNOFILE=1048576
	LimitNPROC=64

	# Isolate vaultwarden from the rest of the system
	PrivateTmp=true
	PrivateDevices=true
	ProtectHome=true
	ProtectSystem=strict

	# Only allow writes to the following directory and set it to the working directory (user and password data are stored here)
	WorkingDirectory=/var/lib/vaultwarden
	ReadWriteDirectories=/var/lib/vaultwarden

	# Allow vaultwarden to bind ports in the range of 0-1024
	AmbientCapabilities=CAP_NET_BIND_SERVICE

	[Install]
	WantedBy=multi-user.target

Then reload systemd, start, and enable, then check the status:
	systemctl daemon-reload && systemctl start vaultwarden.service && systemctl enable vaultwarden.service
	systemctl status vaultwarden.service
