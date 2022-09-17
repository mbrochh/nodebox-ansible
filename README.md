# Nodebox Ansible Script

This is an attempt to turn the awesome Ministry of Nodes video tutorials
into an Ansible Script: https://www.youtube.com/playlist?list=PLCRbH-IWlcW2A_kpx2XwAMgT0rcZEZ2Cg

I'm not affiliated with them in any way and I have no idea what I am getting
myself into. Let's see how far I can get.

# Progress

Currently at this point in the videos: https://youtu.be/fx_mLXISrfM?t=1751

* installed & configured tor
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

# Usage

* Clone this repo
* Create `hosts` file based on `hosts.sample`
* Create `./vars/external_vars.yml` file based on `./vars/external_vars.yml.sample`
* Execute with `ansible-playbook -i hosts step1.yml`
  * This will only prepare Ubuntu and install bitcoin core.
  * You should check if it worked via `tail -f ~/.bitcoin/debug.log`
  * Let it run over night until the blockchain is fully downloaded
  * You can run `bitcoin-cli getblockchaininfo` and compare the `blocks` value
    with the latest value shown at https://mempool.space/ to know if your
    blockchain has been fully synced. Also, `initialblockdownload` should
    be `false` at this point
* Then execute `ansible-playbook -i hosts step2.yml`
  * This will install Tor and restart the bitcoind service
  * Then it will install Fulcrum

# Manual Steps

After the script has run, you need to do the following:

* remove `addnode=` line from `bitcoin.conf`
* run `sudo systemctl restart bitcoind`
* check `bitcoin-cli getnetworkinfo` and see if `onion` has `"reachable": true`

I will probably script the things above as well at the very end.
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
* `bitcoin-cli getnetworkinfo`
  * Gets network info. Use to check if tor is working
  * see https://youtu.be/fx_mLXISrfM?t=1692
* `sudo systemctl status bitcoind`
  * commands to control bitcoind: start, stop, restart, status
  * see https://youtu.be/fx_mLXISrfM?t=1192
* `sudo systemctl status tor`
  * commands to control tor: start, stop, restart, status
  * see https://youtu.be/fx_mLXISrfM?t=1264
* `journalctl -fu bitcoind|fulcrum`
  * see logs of the given service in realtime