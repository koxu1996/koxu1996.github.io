## Networking requirements
Ports used by the node
 - 35000 (required to be externally visible)
 - 7777 RPC endpoint for interaction with casper-client
 - 8888 REST endpoint for status and metrics (having this accessible allows your node to be part of network status)
 - 9999 SSE endpoint for event stream.

Of these 35000 is the only port required to be open for your node to function, however, opening 8888 will allow others to know general network health.

## OS Requirements
Recommended OS version is Ubuntu 18.04 or 20.04 (recommended).

## Number of open files

Please read through [this page on nofiles configuration](https://github.com/casper-network/casper-node/wiki/Increasing-default-nofile-HARD-limit-for-a-node) to put settings in `/etc/security/limits.conf` for your node to assure proper operation.

## Clean up

If you were running previous node on this box, this will clean up state
If packages are not installed, the apt remove may give errors, this is not a problem
```
sudo systemctl stop casper-node-launcher.service
sudo apt remove -y casper-client
sudo apt remove -y casper-node
sudo apt remove -y casper-node-launcher
sudo rm /etc/casper/casper-node-launcher-state.toml
sudo rm -rf /etc/casper/1_*
sudo rm -rf /var/lib/casper/*
```

## Setup Casper Labs repo for packages
```
echo "deb https://repo.casperlabs.io/releases" bionic main | sudo tee -a /etc/apt/sources.list.d/casper.list
curl -O https://repo.casperlabs.io/casper-repo-pubkey.asc
sudo apt-key add casper-repo-pubkey.asc
sudo apt update
```

## Install casper-node-launcher, casper-client, and jq
```
sudo apt install -y casper-client
sudo apt install -y casper-node-launcher
sudo apt install -y jq
```

## Install all protocols 

`sudo -u casper /etc/casper/node_util.py stage_protocols casper.conf`

## Validator Keys 

If you do not have keys, you can create them
```
sudo -u casper casper-client keygen /etc/casper/validator_keys
```

## Get a trusted hash
```
sudo sed -i "/trusted_hash =/c\trusted_hash = '$(casper-client get-block --node-address http://3.14.161.135:7777 -b 20 | jq -r .result.block.hash | tr -d '\n')'" /etc/casper/1_0_0/config.toml
```

## Start the node
```
sudo /etc/casper/node_util.py rotate_logs
sudo /etc/casper/node_util.py start
```

## Monitor the node syncing
```
/etc/casper/node_util.py watch
```

When you run the watch command, expect to see something like this:
```
Last Block: 630151 (Era: 4153)
Peer Count: 297
Uptime: 4days 6h 40m 18s 553ms
Build: 1.4.5-a7f6a648d-casper-mainnet
Key: 0147b4cae09d64ab6acd02dd0868722be9a9bcc355c2fdff7c2c244cbfcd30f158
Next Upgrade: None

RPC: Ready

● casper-node-launcher.service - Casper Node Launcher
   Loaded: loaded (/lib/systemd/system/casper-node-launcher.service; enabled; vendor preset: enabled)
   Active: active (running) since Wed 2022-03-16 21:08:50 UTC; 4 days ago
     Docs: https://docs.casperlabs.io
 Main PID: 2934 (casper-node-lau)
    Tasks: 12 (limit: 4915)
   CGroup: /system.slice/casper-node-launcher.service
           ├─ 2934 /usr/bin/casper-node-launcher
           └─16842 /var/lib/casper/bin/1_4_5/casper-node validator /etc/casper/1_4_5/config.toml```

If your `casper-node-launcher status` for not show active (running) with an increasing time, you are either not running or restarting. 

## Monitor your node once it is up.

Get a monitoring tool in place.  The community has created a few tools such as:
- Status.py: https://github.com/RapidMark/casper-tools
- Grafana: https://github.com/matsuro-hadouken/casper-tools
