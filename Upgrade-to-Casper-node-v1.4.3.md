# Introduction and Timing
We are requesting that all Casper Mainnet participants stage the upgrade of their nodes to a new version of `casper_node` immediately, using the instructions below. _Staging an upgrade_ is a process in which you tell your node to download the upgrade files and prepare them so that they can automatically be applied at the pre-defined activation point.

## 1.4.3 Release
Information about this release can get found here: https://casper.network/network/blog/technical-release-and-changelog-for-v143

This includes a security update, so code will be published in the repo after it activates on MainNet.

For this upgrade to protocol version 1.4.3, the activation point is `Era 3111`, which will be approximately around:

 * 2021-12-16 14:30:43.128000+00:00 UTC
 * 2021-12-16 06:30:43.128000-08:00 US/PACIFIC
 * 2021-12-16 09:30:43.128000-05:00 US/EASTERN
 * 2021-12-16 15:30:43.128000+01:00 EUROPE/ZURICH
 * 2021-12-16 22:30:43.128000+08:00 ASIA/HONG_KONG

# Upgrade Staging Instructions
The process to upgrade your node is very straightforward. Log in to your node, and execute the following two commands.

1. Before running the upgrade, view the script with this command (optional):

    `curl -s genesis.casperlabs.io/casper/1_4_3/stage_upgrade.sh`

2. Download and execute the upgrade:

    `cd ~; curl -sSf genesis.casperlabs.io/casper/1_4_3/stage_upgrade.sh | sudo bash -`

# Verifying Successful Staging

After you have successfully executed the above commands, **wait a few minutes for a new block to be issued before checking** that your node is correctly staged with the upgrade. After a few minutes, take a look at your status end-point, as follows:

`curl -s http://127.0.0.1:8888/status | jq .next_upgrade`

You should expect this output if properly staged, prior to upgrading:

    $ curl -s localhost:8888/status | jq .next_upgrade
    {
       "activation_point": 3111,
       "protocol_version": "1.4.3"
    }



If you see `null` after waiting for a few minutes, then your upgrade staging was not executed successfully.