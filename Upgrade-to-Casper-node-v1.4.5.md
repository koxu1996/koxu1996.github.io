# Introduction and Timing
We are requesting that all Casper Mainnet participants stage the upgrade of their nodes to a new version of `casper_node` immediately, using the instructions below. _Staging an upgrade_ is a process in which you tell your node to download the upgrade files and prepare them so that they can automatically be applied at the pre-defined activation point.

DO NOT restart the node, only run the commands provided. The upgrade will automatically occur at the activation point.

**Make sure to see security information at the bottom after staging.**

## 1.4.5 Release
This includes some security fixes and code will be made public following activation.

For this upgrade to protocol version 1.4.5, the activation point is `Era 4417`, which will be approximately around:

 * 2022-04-04 13:45 UTC
 * 2022-04-04 06:45 US/PACIFIC
 * 2022-04-04 09:45 US/EASTERN
 * 2022-04-04 15:45 EUROPE/ZURICH
 * 2022-04-04 21:45 ASIA/HONG_KONG

# Upgrade Staging Instructions
The process to upgrade your node is very straightforward. Log in to your node, and execute the following two commands.

1. Before running the upgrade, view the script with this command (optional):

    `curl -s genesis.casperlabs.io/casper/1_4_5/stage_upgrade.sh`

2. Download and execute the upgrade:

    `cd ~; curl -sSf genesis.casperlabs.io/casper/1_4_5/stage_upgrade.sh | sudo bash -`

## 1.4.4 config change

The install script will modify your current running config in `/etc/casper/1_4_4/config.toml`.  

This section of the script is increasing the value for max_global_state_size.  There is a small chance that we will exceed the configured space prior to the 1.4.5 upgrade point.  If so, this will allow your node to continue properly after your node automatically restarts.

    # bump max global state for protocol 1_4_4:
    sudo sed -i "/max_global_state_size =/c\max_global_state_size = 1_539_316_281_344" /etc/casper/1_4_4/config.toml

# Verifying Successful Staging

After you have successfully executed the above commands, **wait a few minutes for a new block to be issued before checking** that your node is correctly staged with the upgrade. After a few minutes, take a look at your status end-point, as follows:

`curl -s http://127.0.0.1:8888/status | jq .next_upgrade`

You should expect this output if properly staged, prior to upgrading:

    $ curl -s localhost:8888/status | jq .next_upgrade
    {
      "activation_point": 4417,
      "protocol_version": "1.4.5"
    }

If you see `null` after waiting for a few minutes, then your upgrade staging was not executed successfully.

## OpenSSL security update

An OpenSSL bug was patched that has the possibility of affecting nodes.  This has been communicated on various channels previously and you may have updated it already.

To verify the version, run:

    sudo apt show openssl

If you see older versions than 1.1.1f-1ubuntu2.12 (20.04) or 1.1.1-1ubuntu2.1~18.04.15 (18.04), you should upgrade with:

    sudo apt update
    sudo apt install openssl

This will NOT restart your node.  The new version of OpenSSL will only be picked up with a restart.  You can wait for 1.4.5 to activate or manually restart your node to use the update OpenSSL immediately.

