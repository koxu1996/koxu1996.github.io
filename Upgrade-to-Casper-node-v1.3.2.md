# Introduction and Timing
We are requesting that all Casper Mainnet participants stage the upgrade of their nodes to a new version of `casper_node` immediately, using the instructions below. _Staging an upgrade_ is a process in which you tell your node to download the upgrade files and prepare them so that they can automatically be applied at the pre-defined activation point.

## 1.3.2 Release
Information on changes for the 1.3.2 release can be [found on the Github release](https://github.com/casper-network/casper-node/releases/tag/v1.3.2)


For this upgrade to protocol version 1.3.2, the activation point is `Era 1605`, which will be approximately around:

* 2021-08-12 13:16:44+00:00 Z
* 2021-08-12 06:16:44-07:00 P-US
* 2021-08-12 09:16:44-04:00 E-US
* 2021-08-12 15:16:44+02:00 C-EUR

# Upgrade Staging Instructions
The process to upgrade your node is very straightforward. Log in to your node, and execute the following two commands.

1. Before running the upgrade, view the script with this command:

    `curl -s genesis.casperlabs.io/casper/1_3_2/stage_upgrade.sh`

2. Download and execute the upgrade:

    `cd ~; curl -sSf genesis.casperlabs.io/casper/1_3_2/stage_upgrade.sh | sudo bash -`

# Verifying Successful Staging
After you have successfully executed the above commands, **wait a few minutes for a new block to be issued before checking** that your node is correctly staged with the upgrade. After a few minutes, take a look at your status end-point, as follows:

`curl -s http://127.0.0.1:8888/status | jq .next_upgrade`

You should expect this output if properly staged, prior to upgrading:

    $ curl -s localhost:8888/status | jq .next_upgrade
    {
      "activation_point": 1605,
      "protocol_version": "1.3.2"
    }


If you see `null` after waiting for a few minutes, then your upgrade staging was not executed successfully.