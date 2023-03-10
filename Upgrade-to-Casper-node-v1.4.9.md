# Introduction and Timing
We are requesting that all Casper Mainnet participants stage the upgrade of their nodes to a new version of `casper_node` immediately, using the instructions below. _Staging an upgrade_ is a process in which you tell your node to download the upgrade files and prepare them so that they can automatically be applied at the pre-defined activation point.

DO NOT restart the node, only run the commands provided. The upgrade will automatically occur at the activation point.

## 1.4.9 Release
This includes Security updates for the network, prior to 1.5 release.

For this upgrade to protocol version 1.4.9, the activation point is `Era 7210`, which will be approximately around:

 * 2022-11-23 14:42 UTC
 * 2022-11-23 06:42 PST
 * 2022-11-23 09:42 EST
 * 2022-11-23 15:42 CEST
 * 2022-11-23 22:42 HKST

# Upgrade Staging Instructions

The process to upgrade your node is very straightforward. Log in to your node, and execute the following two commands.

1. Before running the upgrade, view the script with this command (optional):

    `curl -s genesis.casperlabs.io/casper/1_4_9/stage_upgrade.sh`

2. Download and execute the upgrade:

    `cd ~; curl -sSf genesis.casperlabs.io/casper/1_4_9/stage_upgrade.sh | sudo bash -`

# Verifying Successful Staging

After you have successfully executed the above commands, **wait a few minutes for a new block to be issued before checking** that your node is correctly staged with the upgrade. After a few minutes, take a look at your status end-point, as follows:

`curl -s http://127.0.0.1:8888/status | jq .next_upgrade`

You should expect this output if properly staged, prior to upgrading:

    $ curl -s localhost:8888/status | jq .next_upgrade
    {
      "activation_point": 7210,
      "protocol_version": "1.4.9"
    }


If you see `null` after waiting for a few minutes, then your upgrade staging was not executed successfully.
