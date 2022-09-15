# Nodebox Ansible Script

This is an attempt to turn the awesome Ministry of Nodes video tutorials
into an Ansible Script: https://www.youtube.com/playlist?list=PLCRbH-IWlcW2A_kpx2XwAMgT0rcZEZ2Cg

I'm not affiliated with them in any way and I have no idea what I am getting
myself into. Let's see how far I can get.

# Progress

Currently at this point in the videos: https://youtu.be/fx_mLXISrfM?t=1265 

* installed Bitcoin Core 
* started the daemon
* created bitcoin.conf
* downloaded & executed rpcauth.py
* updated bitcoin.conf
* registered bitcoind as service that starts on boot


# Prerequisites

* Get some hardware and install Ubuntu Server on it
  * I'll explain my setup when the script is done
* Make sure to install Python & Ansible on your machine.
  * I'll explain the fastest way to do that when the script is done
  * for Ansible, see https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html
* Create `hosts` file based on `hosts.sample`
* Create `./vars/external_vars.yml` file based on `./vars/external_vars.yml.sample`

# CLI Commands

Just taking some notes here about useful CLI commands that k3tan shares in 
his videos:

* `bitcoin-cli getblockchaininfo`
  * Shows all kinds of info about the downloaded blockchain
  * see https://youtu.be/fx_mLXISrfM?list=PLCRbH-IWlcW2A_kpx2XwAMgT0rcZEZ2Cg&t=451
* `bitcoin-cli getconnectioncount`
  * Shows number of conntected peers
  * see https://youtu.be/fx_mLXISrfM?list=PLCRbH-IWlcW2A_kpx2XwAMgT0rcZEZ2Cg&t=512
* `bitcoin-cli stop`
  * Stops the daemon (don't use this, use the systemctl command instead)
  * see https://youtu.be/fx_mLXISrfM?t=1161
* `sudo systemctl status bitcoind`
  * commands to control bitcoind: start, stop, restart, status
  * see https://youtu.be/fx_mLXISrfM?t=1192