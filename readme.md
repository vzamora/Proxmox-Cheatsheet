# VZamora's Proxmox cheatsheet

	In most of the guides, code blocks (like this note is in) is used exclusively for things you put in the console.
	In this guide, I'm just using code blocks as another separator.
	Everything is still very much a work-in-progress, but most of these will get you a functioning CT.
	Ones that don't will say so in the guide (usually at the bottom).

## General Proxmox Setup and Notes folder contains the following files:

1- Initial Proxmox Setup:

	Rough notes on general Proxmox setup

LXC Updater - CronTab:

	This guide teaches you how to automatically run updates on all of your containers.  The example script runs updates daily at 2AM.
	This guide is complete and functioning.

SSH as 'root' into LXCs:

	Notes/guide on how to SSH into your LXCs as root.
	This guide is complete and functioning.

USB Passthrough Notes
	
	Notes on passing-through USB devices to containers.  This very much isn't a guide, just a bunch of notes I compiled.
	You can scrape together a "guide" from this, but I'm not confident enough in it to do a pretty/good version yet.
	I consider this guide for the "I'm starting to feel confident in CLI" crowd.

ZFS Info:

	General information on ZFS setup, checking status, and handling large amounts of data.
	
## Media Applications

Overall, this folder is for guides/information pertaining to media and management on your home network.

Bazarr

	Subtitles downloader/finder, links with Sonarr and Radarr.
	This guide is complete and functioning
	
Jackett

	Connects qBit to torrent trackers.
	This guide is complete and functioning
	
Jellyfin

	Media server, similar to Plex.
	This guide is complete and functioning
	
Plex

	Media server, similar to Jellyfin but with more devices supported natively.
	This guide is complete and functioning
	
Prowlarr

	Connects qBit to torrent trackers, similar to Jackett.
	This guide is complete and functioning
	
Radarr

	Movies - Can track upcoming/wanted movies and download automatically with qBit.
	This guide is complete and functioning
	
Sonarr

	Shows - Can track upcoming/wanted shows and download automatically with qBit.
	This guide is complete and functioning
	
qBittorrent LXC
	
	Bittorrent Client with WebUI and RSS feed compatibility.
	This guide is complete and functioning
	

## Network Applications

Overall, this folder is for guides/information pertaining to Network Apps/Utilities, both LAN and WAN.

Caddy

	Very powerful web-server and HTTPS/TLS Deployment Solution.
	Follow this guide to allow you to access your self-hosted apps from outside your LAN.
	This guide is complete and functioning.
	
OpnSense

	OPNSense is an incredibly powerful FOSS router OS.
	You can follow this guide to run a full dedicated router on your home network.
	This guide is complete and functioning.

PiHole

	PiHole is a great whole-network ad-blocking program.
	This guide is complete and functioning.
	
SMB Fileserver

	This gives computers on your LAN access to your bulk data storage hosted on your Proxmox machine.
	This guide is complete and functioning.

TP Link Omada Controller

	The Omada Controller lets you use TP Link Omada Access Points on your network
	This guide is complete and functioning.
	
## Utility Applications

Overall, this folder is for guides/information pertaining to other general applications that didn't have a specific home.

Docker and Portainer in CT

	This guide will set-up Docker and Portainer in an LXC on Proxmox.
	Docker is *like* a Container-manager, but some things are much easier to install/manage in Docker than manually via LXC.
	This guide is complete and functioning.

Duplicati

	Duplicati is a good-but-not-great encrypted backup tool that can connect to lots of backup destinations.
	I have had some issues with it, but it's a fairly stable backup solution that can backup to Google Drive.
	This guide is complete and functioning.

Flame Dashboard

	I can't recommend this solution, but Flame is a dashboard primarily designed for your self-hosted apps.
	This guide is complete and functioning.
	I recommend using Heimdall or Organizr instead.

Grocy

	Grocy is a self-hosted ERP system, specifically designed around Grocery management.
	This guide is complete and functioning.

Guacamole

	Apache Guacamole is a clientless remote desktop gateway.
	It allows remote connection to all of your computers in one place.
	It supports standard protocols like VNC, RDP, and SSH.
	This guide is complete and functioning.

Heimdall

	Heimdall is a dashboard primarily designed for your self-hosted apps.
	This guide is mostly complete and functioning, but is missing one step I haven't been able to track down yet.

Home Assistant OS



NextCloud Manual Install (wip)

	NextCloud is a self-hosted cloud storage server similar to Google Drive or OneDrive.
	This guide is mostly complete and functioning, but has proven to be a bit inconsistent on the pre-reqs.

NextCloud TKL Install

	NextCloud is a self-hosted cloud storage server similar to Google Drive or OneDrive.
	This guide is mostly complete and functioning, but has proven to be a bit inconsistent on the requirements for updating.

OctoPrint

	VM for connecting to 3D Printers via USB.  This replaces needing a dedicated Raspberry Pi for wirelessly mana
	This guide is complete and functioning.

Organizr

	Organizr is a dashboard primarily designed for your self-hosted apps.
	This guide is mostly complete and functioning, but is missing one step I haven't been able to track down yet.

VaultWarden
	
	Self-hosted password management Vault for BitWarden.
	This guide is complete and functioning.
