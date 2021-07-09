# What is Delegation?

Instead of having to operate and maintain a casper node (server that stores a copy of the blockchain), you can instead delegate your tokens to someone on the network who has indicated they intend to operate a server on the network. These server operators are called Validators, and they keep a certain percentage of rewards generated from your staked tokens, similar to a commission. 

Validators set their own fee, as well as earn rewards for their own staked tokens. By participating in the protocol this way, you help to improve decentralization and security of the network, and earn rewards in return. Each block in the chain is proposed by a validator, and a validator is given reward tokens for proposing the block, and rewards for any delegations are also paid out.

# How do I choose a validator to delegate to?

You have complete control over who you delegate your CSPR to. To get to know who is an active member of the community and works to support their validating node with high availability, join our official [discord server](https://discord.com/invite/Q38s3Vh). Validators (identified with the @Validator role, and a red tint to their display names) often post in the various channels and are usually active community members.

You can also view the official casper blockchain explorer, [Cspr.live Validator Page](https://cspr.live/validators) to select any validator from the list to delegate your tokens to.

The validator page will show a validator's public key, total staked CSPR, and percent of weight of the total staked amount on the network. Clicking on a validator in the list will open the details of that Validator. The Validator details page will show the following information:
  - Public Key
  * Commission Rate
  + Self Stake
  * Total Stake

As well as second section that can show you either blocks proposed by that validator, or delegations received by that validator (public key of delegator and amount delegated).


# Delegating, the command-line way

We are hard at work developing a UI-based way to stake your CSPR tokens. While this is in development, the current method for delegating tokens on the network is accomplished via the command-line. Casper-client is currently tested and compatible with Ubuntu 18.04 or 20.04. To delegate your tokens on the network, follow the base casper install instructions here:

## Install Casper-client

    echo "deb https://repo.casperlabs.io/releases" bionic main | sudo tee -a /etc/apt/sources.list.d/casper.list
    curl -O https://repo.casperlabs.io/casper-repo-pubkey.asc
    sudo apt-key add casper-repo-pubkey.asc
    sudo apt update
    sudo apt install casper-client -y
    sudo apt install jq -y

## Install pre-requisites for building smart contracts:

    cd ~
    sudo apt purge --auto-remove cmake
    wget -O - https://apt.kitware.com/keys/kitware-archive-latest.asc 2>/dev/null | gpg --dearmor - | sudo tee /etc/apt/trusted.gpg.d/kitware.gpg >/dev/null
    sudo apt-add-repository 'deb https://apt.kitware.com/ubuntu/ focal main'   
    sudo apt update
    sudo apt install cmake -y

    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

    sudo apt install libssl-dev -y
    sudo apt install pkg-config -y
    sudo apt install build-essential -y

    BRANCH="1.0.20" \
        && git clone --branch ${BRANCH} https://github.com/WebAssembly/wabt.git "wabt-${BRANCH}" \
        && cd "wabt-${BRANCH}" \
        && git submodule update --init \
        && cd - \
        && cmake -S "wabt-${BRANCH}" -B "wabt-${BRANCH}/build" \
        && cmake --build "wabt-${BRANCH}/build" --parallel 8 \
        && sudo cmake --install "wabt-${BRANCH}/build" --prefix /usr --strip -v \
        && rm -rf "wabt-${BRANCH}"

## Compile Smart Contracts:

    cd ~

    git clone git://github.com/CasperLabs/casper-node.git
    cd casper-node/
    git checkout release-1.0.0
    make setup-rs
    make build-client-contracts -j

## Generate keys

If you do not have a pair of keys already generated, you can create them. Create new keys to transfer to and from exchanges with. Keep a copy of your keys in a safe place! 

**IMPORTANT:** whatever path you save your keys to, you will need to edit further commands that reference the correct path.

    casper-client keygen /path/to/your/key/storage
    PUBLIC_KEY_HEX=$(cat /path/to/your/key/storage/public_key_hex)

## Start Delegating

Once you have set up your smart contracts, installed the casper-client, and chosen a Validator on the network to whom you wish to delegate, you can send a deployment to a node on the network with an open RPC endpoint. The delegate smart contract requires just under 3 CSPR in gas, noted by the "payment-amount" command-line argument.

The below command is set to delegate 555 CSPR to the indicated validator:

    VALIDATOR_PUBLIC_KEY="the public key hex of your desired validator, from cspr.live"

    casper-client put-deploy \
        --chain-name casper \
        --node-address http://<MAINNET_PEER_IP>:7777 \
        --secret-key /path/to/secret_key.pem \
        --session-path "$HOME/casper-node/target/wasm32-unknown-unknown/release/delegate.wasm" \
        --payment-amount 3000000000 \
        --session-arg "validator:public_key='$VALIDATOR_PUBLIC_KEY'" \
        --session-arg="amount:u512='555000000000'" \
        --session-arg "delegator:public_key='$PUBLIC_KEY_HEX'"

After deploying the smart contract, you will receive output from the casper-client in the form of the following (example hash is from testnet):

    {
        "id": 1821918088518704118,
        "jsonrpc": "2.0",
        "result": {
          "api_version": "1.2.0",
          "deploy_hash": "2af6618bb2a83245cba4f4fd64ad731c86e1a8eb6ddd057747ad3a666c87f488"
        }
    }

Confirm the delegate deploy was successful after a few minutes:

    casper-client get-deploy <DEPLOY_HASH_HERE> | jq .result.execution_results

which will return the json body of the deploy's execution results:

    [
      {
        "block_hash": "1f73378fead588baa14462695fb255106f80ffeeb7838b0742856fa4a699c32d",
        "result": {
          "Success": {
            "cost": "2949625600",
            "effect": {
              "operations": [
                ...
               ]
            "transforms": [
                 ...
               ]
            "transfers": [
                 ...
               ]
        }
    ]
            
# Checking your staked amount with a validator

While your tokens are staked with a validator, your rewards are automatically re-added to the delegation, increasing the amount you will receive in rewards for future blocks. You can confirm your total tokens staked with a validator by entering:


    casper-client get-auction-info --node-address http://<MAINNET_PEER_IP>:7777 | jq -r '.result.auction_state.bids[] | select( .public_key == "$VALIDATOR_PUBLIC_KEY") | .bid.delegators[] | select( .public_key == "$PUBLIC_KEY_HEX") | .staked_amount | tonumber / 1000000000'

Which will return a human readable amount of CSPR (testnet example):

    casper-client get-auction-info --node-address http://64.225.74.66:7777 | jq -r '.result.auction_state.bids[] | select( .public_key == "017d96b9a63abcb61c870a4f55187a0a7ac24096bdb5fc585c12a686a4d892009e") | .bid.delegators[] | select( .public_key == "01360dbdb9fdcd720f560c9b94815481b0d0109bc1ec761b2e99e974ee00022079") | .staked_amount | tonumber / 1000000000'
    600


## Undelegating tokens

In order to undelegate tokens when able, the smart contract syntax is similar to the delegate contract. To undelegate 100 CSPR from your Validator, enter:

    casper-client put-deploy --chain-name casper \
    --node-address http://<MAINNET_PEER_IP>:7777/ \
    --secret-key "/path/to//secret_key.pem" \
    --session-path "$HOME/casper-node/target/wasm32-unknown-unknown/release/undelegate.wasm" \
    --payment-amount 1000000000 \
    --session-arg="validator:public_key='$VALIDATOR_PUBLIC_KEY'" \
    --session-arg="amount:u512='100000000000'" \
    --session-arg="delegator:public_key='$PUBLIC_KEY_HEX'"

Once deployed, the network will return a deploy hash:

    {
      "id": 3482245447580049949,
      "jsonrpc": "2.0",
      "result": {
        "api_version": "1.2.0",
        "deploy_hash": "f569fa6a1de134758f76533e34a082761d74dff45813f6aed106d2f9813935ff"
      }
    }

Confirm the undelegate deploy was successful after a few minutes:

    casper-client get-deploy <UNDEPLOY_HASH_HERE> | jq .result.execution_results

which will return the json body of the deploy's execution results:

    [
      {
        "block_hash": "1f73378fead588baa14462695fb255106f80ffeeb7838b0742856fa4a699c32d",
        "result": {
          "Success": {
            "cost": "2949625600",
            "effect": {
              "operations": [
                ...
               ]
            "transforms": [
                 ...
               ]
            "transfers": [
                 ...
               ]
        }
    ]

# Send tokens to another wallet (like an exchange)

Transfer 5 CSPR:

    casper-client transfer --node-address http://<MAINNET_PEER_IP>:7777 --amount 5000000000 --target-account {destination Public Key Hex} --payment-amount 10000 --secret-key /path/to/secret_key.pem --chain-name casper --id 1 | jq -r


# Closing Notes

Your delegation will start to earn rewards beginning in the next era (under 2 hours). You can check your staked amount as the network moves on to see how many CSPR tokens have been earned. 