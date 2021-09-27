# Introduction and Timing
We are requesting that all Casper Mainnet participants stage the upgrade of their nodes to a new version of `casper_node` immediately, using the instructions below. _Staging an upgrade_ is a process in which you tell your node to download the upgrade files and prepare them so that they can automatically be applied at the pre-defined activation point.

## 1.3.4 Release
This is a security release and information will be shared after the activation point.

For this upgrade to protocol version 1.3.4, the activation point is `Era 2193`, which will be approximately around:

 * 2021-09-30 14:46:44.424000+00:00 UTC
 * 2021-09-30 07:46:44.424000-07:00 US/PACIFIC
 * 2021-09-30 10:46:44.424000-04:00 US/EASTERN
 * 2021-09-30 16:46:44.424000+02:00 EUROPE/ZURICH
 * 2021-09-30 22:46:44.424000+08:00 ASIA/HONG_KONG

# Upgrade Staging Instructions
The process to upgrade your node is very straightforward. Log in to your node, and execute the following two commands.

1. Before running the upgrade, view the script with this command:

    `curl -s genesis.casperlabs.io/casper/1_3_4/stage_upgrade.sh`

2. Download and execute the upgrade:

    `cd ~; curl -sSf genesis.casperlabs.io/casper/1_3_4/stage_upgrade.sh | sudo bash -`

# Verifying Successful Staging
After you have successfully executed the above commands, **wait a few minutes for a new block to be issued before checking** that your node is correctly staged with the upgrade. After a few minutes, take a look at your status end-point, as follows:

`curl -s http://127.0.0.1:8888/status | jq .next_upgrade`

You should expect this output if properly staged, prior to upgrading:

    $ curl -s localhost:8888/status | jq .next_upgrade
    {
       "activation_point": 2193,
       "protocol_version": "1.3.4"
    }


If you see `null` after waiting for a few minutes, then your upgrade staging was not executed successfully.