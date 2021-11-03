# Mainnet outage 2021-11-03 13:45 UTC

Mainnet went down during the upgrade from 1.3.4 to 1.4.1, due to the following chain of events:

* The mainnet upgrade point on 1.3.4 was reached. This version did not have [the patch (#2236)](https://github.com/casper-network/casper-node/pull/2236) applied, which fixes a race condition that causes the final switch block to not be persisted before shutting down a node for upgrading.
* The majority of the network did not persist the switch block due to the aforementioned bug, with the exception being our patched node brought live with the fix backported as a safety measure.
* In the 1.4 release we [introduced a requested namespacing feature (#1532)](https://github.com/casper-network/casper-node/pull/1532) for databases requiring a database move. This renaming of the database files resulted in a state where any older version will not find the database if restarted.
* As version 1.4.1 starts up, it shuts down due a missing switch block as a safety measure. The launcher falls back to an earlier version, refusing to start up due to a missing database.
* All nodes other than patched nodes commence syncing from genesis.


## Why didn't testnet fail?

Testnet has less auction state and did not trigger the condition causing the missed write of the final switch block.


## What is the fix?

As a fix, we enabled previous versions to find the databases again, hardlinking the files to their original locations. This allowed all previous versions to start, find the files and therefore progress normally. Once 1.4.1 started up, it requested the missed switch block from peers that have it and then proceeded normally.

As soon as enough stake was brought back online, the network resumed operation.


## Outcome

No blocks or confirmed deploys or transactions were lost.

The network itself was unable to process new deploys from the start of the outage at block 283,521 (2021-11-03 13:50:46) until mainnet resumed normal operations at block 283,522 (2021-11-03 18:42:57) for a total outage of 4:21h.

Deploys that were submitted, but not included in blocks before the outage may need to be resubmitted.