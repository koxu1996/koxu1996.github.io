# Summary
This go-no-go describes the user acceptance criteria for release of patch version 1.4.3 to Mainnet. There are couple of notable additional fixes that is included in this release, patch ([patch](https://github.com/casper-network/casper-node/pull/2396/commits)) and ([patch](https://github.com/casper-network/casper-node/pull/2395)), compared to the v1.4.2 on testnet(to be upgraded to 1.4.3 as well).
The release of v1.4.3 is primarily contingent to the acceptance of remediation of a breaking change introduced in v1.4.2, impacting users of the Casper Network JS SDK, Casper Signer,  casper-cep47-js-client , casper-erc20-js-client, casper-js-client-helper. 
## Acceptance Criteria
Users(Exchanges, Dapp Developers et al) of the Casper network must upgrade to the following versions of software, based on their usage, to be forward compatible with network.
### Call for action: upgrade
*  JS SDK 2.7.1 or above.
*  Casper-Signer 1.4.4 or above.
*  Casper-cep47-js-client  - 0.5.4 or above.
*  Casper-erc20-js-client  - 0.0.3 or above.
*  Casper-js-client-helper  - 0.0.3 or above.

If the user has upgraded the relevant software please provide your approval as a comment([here](https://github.com/casper-network/roadmap/issues/82)).

Note: Users of Casper-Client should immediately update to client version 1.4.3 post Mainnet upgrade to 1.4.3, to maintain backward compatibility. 
