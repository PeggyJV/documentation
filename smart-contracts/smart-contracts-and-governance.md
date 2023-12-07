# Smart Contracts & Governance

Sommelier Governance plays a core role in the continued deployment and support of newly-released Cellars. The ["Roles & Permissions" ](roles-and-permissions.md)section enumerates all operations available to governance: this section will contain guidelines around the usage of those operations.\


<figure><img src="../.gitbook/assets/Registry-Focused High-Level Schematic.jpg" alt=""><figcaption><p>Permission-Layer Focused Schematic</p></figcaption></figure>

#### Registry Updates

* Governance should take **extreme care** when using the `setAddress` function. Cellars are deployed expecting certain registry slots to fulfill certain interfaces (for instance, the assumption that the registry slot `2` will fulfill the `PriceRouter` interface).\
  \
  Malicious addresses in slots that Cellars use can lead to unexpected behavior, up to and including loss of funds. In the case of honest updates, where `setAddress` is used to update a protocol component to a contract version with new functionality, **backwards compatibility must be maintained.** Breaking changes that are not backwards-compatible can end up freezing previously-deployed Cellars that expect certain interfaces in certain registry slots.\

* When using `trustAdaptor` to add a new adaptor to the protocol, Governance should exercise due diligence in making sure that the given adaptor does not add risk to either the entire protocol, or Cellars which may use the adaptor. Before trusting an adaptor, governance should **undertake proper due diligence,** including reviewing code and audit reports.\

* When using `trustPosition` to enable new applications for a given adaptor (interaction with new tokens), governance should take care to **undertake proper due diligence** so that the `adaptorData` being associated with the position does not introduce any additional risk. For instance, if the `adaptorData` bytes specify particular tokens, those tokens should be checked for possible malicious or nonstandard behaviors. For example, positions should not be trusted if they are associated with fee-on-transfer tokens.

#### Asset Pricing

* When adding new assets to the Price Router, governance **should always** ensure that the assets being added derive pricing from robust sources and will not be subject to oracle manipulation attacks. Low-liquidity tokens should be avoided.\

* Governance should ensure that the `_expectedAnswer` when calling `addAsset` is **always** in USD terms, even if that asset is not directly priced in USD (e.g. only ETH oracle available).

#### Cellar Operations

* Governance has the ability to change allowed rebalance deviations with `allowedRebalanceDeviation`. In general, the allowed deviations should be kept to the minimum amount that allows the cellar to perform normal operations without disruption.\
  \
  In some cases - for exotic strategies or high-reputation strategists - this deviation amount can be increased in order to enable specific use cases. When doing so, governance should ensure that the **strategist is not in a position to abuse larger rebalance deviations.** Hypothetical bugs and security issues in adaptors may have their value at risk bounded by the rebalance deviation: therefore governance should also consider that larger rebalance deviations decrease Cellar safety, and should only be used with robust and battle-tested sets of adaptors.\

* Governance also has the ability to call `setShareLockingPeriod` to change the initial deposit lock for new funds that are deposited into the cellar. Lower share lock periods reduce the opportunity cost of sandwiching Cellar operations (directing outsize profits to short-term depositors). However, they can also be more convenient for UX purposes. When lowering share lock periods, governance should take care to consider the sorts of adaptor calls the strategist makes, and if a lower share lock period might increase the risk of sandwich attacks and/or unfair distribution of profits.\

* Governance should only use `initiateShutdown` in **extreme scenarios.** Shutdowns disallow new deposits and adaptor calls, but do not block withdrawals. If using shutdowns to mitigate a security vulnerability, governance must carefully consider whether the vulnerability can still be exploited during shutdown, and should additionally be aware that calling `initiateShutdown` may further highlight the existence of the vulnerability to outside parties.\
