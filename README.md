# Nodebox Ansible Script

This is an attempt to turn the awesome Ministry of Nodes video tutorials
into an Ansible Script: https://www.youtube.com/playlist?list=PLCRbH-IWlcW2A_kpx2XwAMgT0rcZEZ2Cg

I'm not affiliated with them in any way and I have no idea what I am getting
myself into. Let's see how far I can get.

# Progress

Please note: I'm still chugging along all those videos. 

I have attempted a full run up until role `samourai_dojo` with a fresh Ubuntu
Server 22.04 installation on 2022-09-18 and it worked well.

Currently at this point in the videos: https://youtu.be/cKTThYKsiEo?list=PLCRbH-IWlcW2A_kpx2XwAMgT0rcZEZ2Cg&t=643

* installed Bitcoin Core
* installed Docker
* installed Tor
* installed Fulcrum
* installed mempool.space
* installed Samourai Dojo

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
* NOTE: The `ansible-playbook` commands can be re-run as often as you want
* Execute with `ansible-playbook -i hosts step1.yml`
  * This will only prepare Ubuntu and install docker and Bitcoin Core.
  * You should check if it worked via `tail -f ~/.bitcoin/debug.log`
  * Also check if `docker ps` shows `CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES`
  * Let it run over night until the blockchain is fully downloaded
  * You can run `bitcoin-cli getblockchaininfo` and compare the `blocks` value
    with the latest value shown at https://mempool.space/ to know if your
    blockchain has been fully synced. Also, `initialblockdownload` should
    be `false` at this point
* Then execute `ansible-playbook -i hosts step2.yml`
  * This will install Tor, Fulcrum, Mempool, Dojo
  * Fulcrum will need a long time to create its DB as well

# Manual Steps

After the script has run, you need to do the following:

* remove `addnode=` line from `bitcoin.conf`
* run `sudo systemctl restart bitcoind`
* check `bitcoin-cli getnetworkinfo` and see if `onion` has `"reachable": true`

I will probably script the things above as well at the very end.

# Caveats

These are things that I had to do differently because they didn't work as
shown in the videos:

* I had to download and install `libssl1.1.1` 
  (see discussion here https://github.com/cculianu/Fulcrum/issues/126);
  I will remove this when a new Fulcrum version is released
* The docker installation is slightly different because the installation
  instructions in the docker docs have since changed.
  * we can now use `docker compose` instead of `docker-compose`

# TODOs

Notes to self:

* Remove libssl1.1.1 installation when Fulcrum1.8.1 is out
* It seems like it can all be done in one step, we just should not enable
  the tor settings in `bitcoin.conf` for the initial chain download, so
  maybe step2 should only be for insertin those settings and everything
  else should be in step1.
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
* `~/dojo-app/docker/my-dojo/dojo.sh logs`
  * see all outputs from all containers that belong to dojo
* `~/dojo-app/docker/my-dojo/dojo.sh onion`
  * get the onion address of the dojo management interface

# Troubleshooting

## `txindex is catching up on block notifications`

* If this appears in `tail -f ~/.bitcoin/debug.log` and it seems stuck, run
  `sudo systemctl restart bitcoind`, now it should continue to sync the chain.