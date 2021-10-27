# Introduction and Timing
We are requesting that all Casper Mainnet participants stage the upgrade of their nodes to a new version of `casper_node` immediately, using the instructions below. _Staging an upgrade_ is a process in which you tell your node to download the upgrade files and prepare them so that they can automatically be applied at the pre-defined activation point.

## 1.4.1 Release
Information about this release can get found here: https://github.com/casper-network/casper-node/releases/tag/v1.4.1

 - Native Transfer cost increased to 0.1 CSPR
 - Block Time is reduced to 32s and other latency improvements for deploy processing
 - Final stage of syncing has been improved
 - Optimizations to reduce IO load for lmdb
 - DB files will move into /var/lib/casper/casper-node/[network_name] to eliminate collision

For this upgrade to protocol version 1.4.1, the activation point is `Era 2600`, which will be approximately around:

 * 2021-11-03 13:45:21.872000+00:00 UTC
 * 2021-11-03 06:45:21.872000-07:00 US/PACIFIC
 * 2021-11-03 09:45:21.872000-04:00 US/EASTERN
 * 2021-11-03 14:45:21.872000+01:00 EUROPE/ZURICH
 * 2021-11-03 21:45:21.872000+08:00 ASIA/HONG_KONG

# Upgrade Staging Instructions
The process to upgrade your node is very straightforward. Log in to your node, and execute the following two commands.

1. Before running the upgrade, view the script with this command:

    `curl -s genesis.casperlabs.io/casper/1_4_1/stage_upgrade.sh`

2. Download and execute the upgrade:

    `cd ~; curl -sSf genesis.casperlabs.io/casper/1_4_1/stage_upgrade.sh | sudo bash -`

# Verifying Successful Staging
After you have successfully executed the above commands, **wait a few minutes for a new block to be issued before checking** that your node is correctly staged with the upgrade. After a few minutes, take a look at your status end-point, as follows:

`curl -s http://127.0.0.1:8888/status | jq .next_upgrade`

You should expect this output if properly staged, prior to upgrading:

    $ curl -s localhost:8888/status | jq .next_upgrade
    {
       "activation_point": 2600,
       "protocol_version": "1.4.1"
    }


If you see `null` after waiting for a few minutes, then your upgrade staging was not executed successfully.