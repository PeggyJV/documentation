# Security Considerations

All adaptors are designed such that calls to their protocol-specific functionality run in a `delegatecall` [context](https://github.com/PeggyJV/cellar-contracts/blob/main/src/base/Cellar.sol#L1343). Therefore, adaptor developers must consider that at execution time, adaptor functionality will run within the context of the calling cellar. Some of the consequences of this design that should be considered include:

* During adaptor calls, `address(this)` will return the Cellar's address, and `msg.sender` will return the caller of the `callOnAdaptor` function (the Gravity Bridge).
* Adaptor contract code should not use contract storage or expect to be able to access adaptor storage. Adaptor calls should access Cellar storage with care.
* Any funds "managed" by positions belonging to the adaptor will be owned by the Cellar on-chain.
* Any approvals set by the adaptor will be approvals to spend from the Cellar itself, [and leftover approvals may be used by other adaptors.](#user-content-fn-1)[^1] This is another context in which zeroing out leftover approvals during adaptor calls is extremely important for the adaptor developer.

Another consequence of adaptor design is that there is no per-function permissioning granularity. Since adaptor calls flow through the Cellar's `callOnAdaptor` function, any function on a Cellar's approved adaptors are eligible to be called by the strategist. Adaptor developers should also take care to make sure to only expose functions that cannot be exploited by malicious strategists.

The main entry point to adaptor calls, `callOnAdaptor`, has a re-entrancy guard attached. Adaptor calls cannot re-enter the Cellar to make new calls to `callOnAdaptor`, or mint or burn new Cellar shares. This protects against strategist-initiated attack vectors which rely on minting or burning shares while retaining constant TVL.

[^1]: I'm a bit confused by this, do we not want to have the revokeApproval() function called at the end of each approval?
