---
sidebar_position: 70
description: >-
  Common processor configuration issues
---

# Caveats

- Processor data subscription methods guarantee that all data matching their data requests will be retrieved, but for technical reasons non-matching data may be added to the [batch context iterables](/evm-indexing/context-interfaces). As such, it is important to always filter the data within the batch handler. For example, a config like this
  ```ts title=src/processor.ts
  export const processor = new EvmBatchProcessor()
    .addLog({
      address: ['0xdac17f958d2ee523a2206206994597c13d831ec7'],
      topic0: [erc20abi.events.Transfer.topic]
    })
  ```
  must always be matched with a filter like this
  ```ts title=src/main.ts
  procesor.run(database, async ctx => {
    // ...
    for (let block of ctx.blocks) {
      for (let log of block.logs) {
        // ⌄⌄⌄ this filter ⌄⌄⌄
        if (log.address === '0xdac17f958d2ee523a2206206994597c13d831ec7' &&
            log.topics[0] === erc20abi.events.Transfer.topic) {
          // ...
        }
      }
    }
  })
  ```
  *even if no other event types (including from other addresses) were requested*.

- The meaning of passing `[]` as a set of parameter values has been changed in the ArrowSquid release: now it _selects no data_. Some data might still arrive (see above), but that's not guaranteed. Pass `undefined` for a wildcard selection:
  ```typescript
  .addStateDiff({address: []}) // selects no state diffs
  .addStateDiff({}) // selects all state diffs
  ```
