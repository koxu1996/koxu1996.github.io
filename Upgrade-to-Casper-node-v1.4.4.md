# Introduction and Timing
We are requesting that all Casper Mainnet participants stage the upgrade of their nodes to a new version of `casper_node` immediately, using the instructions below. _Staging an upgrade_ is a process in which you tell your node to download the upgrade files and prepare them so that they can automatically be applied at the pre-defined activation point.

DO NOT restart the node, only run the commands provided. The upgrade will automatically occur at the activation point.

## 1.4.4 Release
Information about this release can get found here: https://github.com/casper-network/casper-node/releases/tag/v1.4.4

For this upgrade to protocol version 1.4.4, the activation point is `Era 3435`, which will be approximately around:

 * 2022-01-12 15:18 UTC
 * 2022-01-12 07:18 US/PACIFIC
 * 2022-01-12 10:18 US/EASTERN
 * 2022-01-12 16:18 EUROPE/ZURICH
 * 2022-01-12 23:18 ASIA/HONG_KONG

# Upgrade Staging Instructions
The process to upgrade your node is very straightforward. Log in to your node, and execute the following two commands.

1. Before running the upgrade, view the script with this command (optional):

    `curl -s genesis.casperlabs.io/casper/1_4_4/stage_upgrade.sh`

2. Download and execute the upgrade:

    `cd ~; curl -sSf genesis.casperlabs.io/casper/1_4_4/stage_upgrade.sh | sudo bash -`

# Verifying Successful Staging

After you have successfully executed the above commands, **wait a few minutes for a new block to be issued before checking** that your node is correctly staged with the upgrade. After a few minutes, take a look at your status end-point, as follows:

`curl -s http://127.0.0.1:8888/status | jq .next_upgrade`

You should expect this output if properly staged, prior to upgrading:

    $ curl -s localhost:8888/status | jq .next_upgrade
    {
      "activation_point": 3435,
      "protocol_version": "1.4.4"
    }


If you see `null` after waiting for a few minutes, then your upgrade staging was not executed successfully.