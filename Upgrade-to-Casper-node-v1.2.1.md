# Introduction and Timing
We are requesting that all Casper Mainnet participants stage the upgrade of their nodes to a new version of `casper_node` immediately, using the instructions below. _Staging an upgrade_ is a process in which you tell your node to download the upgrade files and prepare them so that they can automatically be applied at the pre-defined activation point.

## Reason for the Patch
A vulnerability was discovered that a uRef need not be part of the named keys of an account when transferring tokens to an account from a purse.  Only a single exploit was carried out by the reporter of the issue. 

The transaction is: https://cspr.live/deploy/1728d7defa1185805b8812bc986cf2c40e654c343b0e2a3f3f9e55f4b74cc193. 

To verify that no other exploits have taken place, look for transactions where the same `fromAccount` and different `sourcePurse`s are used.


For this upgrade to protocol version 1.2.1, the activation point is `Era 1281`, which will be approximately around:

* 2021-07-16 12:28:21+00:00 Z
* 2021-07-16 05:28:21-07:00 P-US
* 2021-07-16 08:28:21-04:00 E-US
* 2021-07-16 14:28:21+02:00 C-EUR

# Upgrade Staging Instructions
The process to upgrade your node is very straightforward. Log in to your node, and execute the following two commands.

1. Before running the upgrade, view the script with this command:

    `curl -s genesis.casperlabs.io/casper/1_2_1/stage_upgrade.sh`

2. Download and execute the upgrade:

    `curl -sSf genesis.casperlabs.io/casper/1_2_1/stage_upgrade.sh | sudo bash -`

# Verifying Successful Staging
After you have successfully executed the above commands, **wait a few minutes for a new block to be issued before checking** that your node is correctly staged with the upgrade. After a few minutes, take a look at your status end-point, as follows:

`curl -s http://127.0.0.1:8888/status | jq .next_upgrade`

You should expect this output if properly staged, prior to upgrading:

    $ curl -s localhost:8888/status | jq .next_upgrade
    {
      "activation_point": 1281,
      "protocol_version": "1.2.1"
    }


If you see `null`, then your upgrade staging was not executed successfully.