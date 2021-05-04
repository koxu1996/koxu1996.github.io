```
# If you were running previous node on this box, this will clean up state
# If packages are not installed, the apt remove may give errors, this is not a problem
sudo systemctl stop casper-node-launcher.service
sudo apt remove -y casper-client
sudo apt remove -y casper-node
sudo apt remove -y casper-node-launcher
sudo rm /etc/casper/casper-node-launcher-state.toml
sudo rm -rf /etc/casper/1_*
sudo rm -rf /var/lib/casper/*

# Setup Casper Labs repo for packages
echo "deb https://repo.casperlabs.io/releases" bionic main | sudo tee -a /etc/apt/sources.list.d/casper.list
curl -O https://repo.casperlabs.io/casper-repo-pubkey.asc
sudo apt-key add casper-repo-pubkey.asc
sudo apt update

# Install casper-node-launcher, casper-client, and jq
sudo apt install -y casper-client
sudo apt install -y casper-node-launcher
sudo apt install -y jq

# Install all protocols. 
# This block is one command.  Copy the entire block to execute
while read protocol_version; do \
sudo -u casper /etc/casper/pull_casper-node_version.sh casper.conf $protocol_version; \
sudo -u /etc/casper/config_from_example.sh $protocol_version; \
done < <(curl -sf genesis.casperlabs.io/casper/protocol_versions)

# If you do not have keys, you can create them
cd /etc/casper/validator_keys
sudo -u casper casper-client keygen

# Get a trusted hash
sudo sed -i "/trusted_hash =/c\trusted_hash = '$(casper-client get-block --node-address http://3.14.161.135:7777 -b 20 | jq -r .result.block.hash | tr -d '\n')'" /etc/casper/1_0_0/config.toml

# Start the node
sudo logrotate -f /etc/logrotate.d/casper-node
sudo systemctl start casper-node-launcher; sleep 2
systemctl status casper-node-launcher

# Monitor the node syncing
watch -n 5 'echo -n "Peer Count: "; curl -s localhost:8888/status | jq ".peers | length"; echo; echo "last_added_block_info:"; curl -s localhost:8888/status | jq .last_added_block_info; echo; echo "casper-node-launcher status:"; systemctl status casper-node-launcher'
```