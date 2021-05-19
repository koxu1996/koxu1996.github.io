# Introduction and Timing
We are requesting that all Casper Mainnet participants stage the upgrade of their nodes to a new version of `casper_node` immediately, using the instructions below. _Staging an upgrade_ is a process in which you tell your node to download the upgrade files and prepare them so that they can automatically be applied at the pre-defined activation point.

For this upgrade to `casper-node v1.2.0`, the activation point is `Era 574`, which will be approximately around:

* To be announced

In order to not have points deducted for your Mainnet reward score, you have to make sure you have properly staged the upgrade well ahead of the activation point so that your node will be upgraded on time.

# Upgrade Staging Instructions
The process to upgrade your node is very straightforward. Log in to your node, and execute the following two commands, one by one:

`sudo -u casper /etc/casper/pull_casper_node_version.sh casper.conf 1_2_0`

`sudo -u casper /etc/casper/config_from_example.sh 1_2_0`

# Verifying Successful Staging
After you have successfully executed the above commands, you can verify that your node is correctly staged with the upgrade by taking a look at your status end-point, as follows:

`curl -s http://127.0.0.1:8888/status | jq .next_upgrade`

This is the output you should expect if properly staged, prior to upgrading:

    $ curl -s http://127.0.0.1:8888/status | jq .next_upgrade
    {
     "activation_point": 574,
     "protocol_version": "1.2.0"
    }

If you see another value here, for example, `null`, then your upgrade staging was not executed successfully.