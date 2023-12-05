---
sidebar_position: 40
description: >-
  Track storage changes with addStateDiff()
---

# Storage state diffs

:::info
Check out the [Caveats](../caveats) page to avoid common `EvmBatchProcessor` configuration issues.
:::

:::tip
State diffs for historical blocks are [currently available](/arrowsquid-docs-v0/evm-indexing/supported-networks) from Archives on the same basis as all other data stored there: for free. If you deploy a squid that indexes traces [in real-time](/arrowsquid-docs-v0/basics/unfinalized-blocks) to Subsquid Cloud and use our [RPC proxy](/arrowsquid-docs-v0/deploy-squid/rpc-proxy), the necessary `trace_` or `debug_` RPC calls made will be counted alongside all other calls and [the price](/arrowsquid-docs-v0/deploy-squid/pricing/#rpc-requests) will be computed for the total count. There are no surcharges for traces or state diffs.
:::

#### `addStateDiff(options)` {#add-state-diff}

Subscribe to changes in the [contract storage](https://coinsbench.com/solidity-layout-and-access-of-storage-variables-simply-explained-1ce964d7c738). This allows for tracking the contract state changes that are difficult to infer from events or transactions, such as the changes that take into account the output of internal calls. `options` has the following structure:
```typescript
{
  // data requests
  address?: string[]
  key?: string[]
  kind?: ('=' | '+' | '*' | '-')[]
  range?: {from: number, to?: number}

  // related data retrieval
  transaction?: boolean
}
```
The data requests here are:
+ `address`: the set of addresses of contracts to track. Leave undefined or set to `[]` to subscribe to state changes of all contracts from the whole network.
+ `key`: the set of storage keys that should be tracked. Regular hexadecimal contract storage keys and [special keys](/arrowsquid-docs-v0/evm-indexing/configuration/data-selection/#state-diffs) (`'balance'`, `'code'`, `'nonce'`) are allowed. Leave undefined or set to `[]` to subscribe to all state changes.
+ `kind`: the set of diff kinds that should be tracked. Refer to the [`StateDiff` section](/arrowsquid-docs-v0/evm-indexing/configuration/data-selection/#state-diffs) of data items documentation for an explanation of the meaning of the permitted values.
+ `range`: the range of blocks within which the storage changes should be tracked.

Enabling the `transaction` flag will cause the processor to retrieve the transaction that gave rise to each state change and add it to the [`transactions` iterable of block data](/arrowsquid-docs-v0/evm-indexing/context-interfaces).

Note that state diffs can also be requested by the [`addTransaction()`](../transactions) method as related data.

[//]: # (???? Check whether the final version adds the transaction to the items, too)
[//]: # (???? Check that the statement about all fields being disable-only for state diffs still holds in the final version)

Selection of the exact data to be retrieved for each state diff item and its optional parent transaction is done with the `setFields()` method documented on the [Field selection](../data-selection) page. Unlike other data items, state diffs do not have any fields that can be enabled, but some can be disabled for improved sync performance.

[//]: # (!!!! Add example)
