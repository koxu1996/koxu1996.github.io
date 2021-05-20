# Preliminary

We do not expect to stage the MainNet upgrade for 1.2.0 until May 24th.  These instructions are preliminary and will continue to be updated closer to that time.

# Introduction and Timing
We are requesting that all Casper Mainnet participants stage the upgrade of their nodes to a new version of `casper_node` immediately, using the instructions below. _Staging an upgrade_ is a process in which you tell your node to download the upgrade files and prepare them so that they can automatically be applied at the pre-defined activation point.

For this upgrade to `casper-node v1.2.0`, the activation point is `Era 694`, which will be approximately around:

* 2021-05-28 12:43:45+00:00 UTC    
* 2021-05-28 05:43:45-07:00 PDT    
* 2021-05-28 08:43:45-04:00 EDT    
* 2021-05-28 14:43:45+02:00 CET

In order to not have points deducted for your Mainnet reward score, you have to make sure you have properly staged the upgrade well ahead of the activation point so that your node will be upgraded on time.

# Upgrade Staging Instructions
The process to upgrade your node is very straightforward. Log in to your node, and execute the following two commands.

1. Before running the upgrade, view the script with this command:

    `curl -s genesis.casperlabs.io/casper/1_2_0/stage_upgrade.sh`

2. Download and execute the upgrade:

    `curl -sSf genesis.casperlabs.io/casper/1_2_0/stage_upgrade.sh | sudo bash -`

# Verifying Successful Staging
After you have successfully executed the above commands, wait a few minutes for a new block to be issued before checking that your node is correctly staged with the upgrade. After a few minutes, take a look at your status end-point, as follows:

`curl -s http://127.0.0.1:8888/status | jq .next_upgrade`

You should expect this output if properly staged, prior to upgrading:

    $ curl -s http://127.0.0.1:8888/status | jq .next_upgrade
    {
     "activation_point": 694,
     "protocol_version": "1.2.0"
    }

If you see another value here, for example, `null`, then your upgrade staging was not executed successfully.