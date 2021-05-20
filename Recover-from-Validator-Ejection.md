# Detecting Ejection (cspr.live)

The validator selection occurs at the end of an Era. Due to the bonding delay, this determines the Validators for the Era after the Era about to start.

When your validating node does not participate in consensus for a length of time, it will be marked as invalid. It will be ejected at the end of the next Era.

For example, if we are in Era 100 and your node is invalid, your node will marked for ejection to be removed at the start of Era 102. This is due to the bonding delay of 1 Era.

If you were a previous validator and still exist on the Validators Auction tab on this page [https://cspr.live/validatiors](https://cspr.live/validatiors), but not in Validators, you may have been ejected or out bid. 

# Detecting Ejection (casper-client)

The full auction information is returned with `casper-client get-auction-info`.  We need to filter this down to your public_key.

Replace <public_key> below with your public_key.

`casper-client get-auction-info | jq '.result.auction_state.bids[] | select( .public_key == "<public_key>")'`

This will return your bid and show an `"inactive"` field.  If this is true, you are ejected.
# Correcting your node issue

Before fixing the ejection, you need to correct whatever problem caused your node to be ejected.

Stage missed upgrades or correct any node issues and get your node in sync. If you cannot figure out the issue, as for help in the node-tech-support channel on discord.

To check your node is in sync, compare the current block height at [https://cspr.live/](https://cspr.live/) with the height from your node with:

`curl -s localhost:8888/status | jq .last_added_block_info`

# Activate Bid

With your node in sync and ready to validate again, we need to activate your invalid bid.  We do that with the activate_bid.wasm contract.  This will be part of the normal contracts compile.

Run the following transaction to re-activate your bid and re-join the network. You will require a balance of at least 0.3 CSPR for this contract.

```
casper-client put-deploy --secret-key /etc/casper/validator_keys/secret_key.pem --chain-name casper-test --session-path "$HOME/casper-node/target/wasm32-unknown-unknown/release/activate_bid.wasm" --payment-amount 300000000 --session-arg "validator_public_key:public_key='$(cat /etc/casper/validator_keys/public_key_hex)'"
```

Check that the deploy was successful with the `casper-client get-deploy <deploy_hash>` or by searching for the deploy_hash on [https://cspr.live/](https://cspr.live/).

# Checking Activation

Once your deploy processes, you can use the `casper-client` to check your bid again with the Detecting Ejection command above. You should now see `"inactive": false,`.

If you wait until the next Era starts, you should also see your public_key as a future validator on [https://cspr.live/validators](https://cspr.live/validators).