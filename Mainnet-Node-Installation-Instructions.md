## Networking requirements
Ports used by the node
 - 35000 (required to be externally visible)
 - 7777 RPC endpoint for interaction with casper-client
 - 8888 REST endpoint for status and metrics (having this accessible allows your node to be part of network status)
 - 9999 SSE endpoint for event stream.

Of these 35000 is the only port required to be open for your node to function, however, opening 8888 will allow others to know general network health.

## OS Requirements
Recommended OS version is Ubuntu 18.04 or 20.04.

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

This block is one command.  Copy the entire block to execute
```
while read protocol_version; do \
sudo -u casper /etc/casper/pull_casper_node_version.sh casper.conf $protocol_version; \
sudo -u casper /etc/casper/config_from_example.sh $protocol_version; \
done < <(curl -sf genesis.casperlabs.io/casper/protocol_versions)
```

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
sudo logrotate -f /etc/logrotate.d/casper-node
sudo systemctl start casper-node-launcher; sleep 2
systemctl status casper-node-launcher
```

## Monitor the node syncing
```
watch -n 5 'echo -n "Peer Count: "; curl -s localhost:8888/status | jq ".peers | length"; echo; echo "last_added_block_info:"; curl -s localhost:8888/status | jq .last_added_block_info; echo; echo "casper-node-launcher status:"; systemctl status casper-node-launcher'
```

When you run the watch command, expect to see something like this:
```
Every 5.0s: echo -n "Peer Count: "; curl -s localhost:8888/status | jq ".peers | length...  joe-test.casperlabs.io: Tue May  4 18:58:09 2021
Peer Count: 67

last_added_block_info:
{
  "hash": "19d1b7ac6364effa54616bbe6b7a47dc3b0f933f63df684cb1fac694adc5c6c8",
  "timestamp": "2021-04-26T20:07:12.640Z",
  "era_id": 109,
  "height": 12042,
  "state_root_hash": "978460dfb9798c5b371ab5377e4c22a65e2c5b41210a5376b54e2204630dfc25",
  "creator": "01d62fc9b894218bfbe8eebcc4a28a1fc4cb3a5c6120bb0027207ba8214439929e"
}

casper-node-launcher status:
● casper-node-launcher.service - Casper Node Launcher
   Loaded: loaded (/lib/systemd/system/casper-node-launcher.service; enabled; vendor preset: enabled)
   Active: active (running) since Sat 2021-04-17 22:20:57 UTC; 2 weeks 2 days ago
     Docs: https://docs.casperlabs.io
 Main PID: 30632 (casper-node-lau)
    Tasks: 11 (limit: 4915)
   CGroup: /system.slice/casper-node-launcher.service
           ├─30632 /usr/bin/casper-node-launcher
           └─30634 /var/lib/casper/bin/1_1_0/casper-node validator /etc/casper/1_1_0/config.toml
Apr 17 22:20:57 joe-test.casperlabs.io systemd[1]: Started Casper Node Launcher.
```

If your `casper-node-launcher status` for not show active (running) with an increasing time, you are either not running or restarting. 

If your `last_added_block_info` is not increasing

## Monitor your node once it is up.

Get a monitoring tool in place.  The community has a created a few tools such as:
- Status.py: https://github.com/RapidMark/casper-tools
- Grafana: https://github.com/matsuro-hadouken/casper-tools
