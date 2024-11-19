---
order: 1
title: Core Concepts
---

## Core Concepts

* In the Balancer v3 architecture [Routers](/concepts/router/overview.md) serve as the pivotal interface for users (not the Vault)
  * Single swaps: [Balancer Router](/developer-reference/contracts/router-api.md)
  * Multi-hop swaps: [Batch Router](/developer-reference/contracts/batch-router-api.md)
* Sender should use Permit2 to approve the Router to spend each swap input token
* Token amount inputs/outputs are always in the raw token scale, e.g. 1 USDC should be sent as 1000000 because it has 6 decimals
* There are two different swap kinds:
  * ExactIn: Where the user provides an exact input token amount.
  * ExactOut: Where the user provides an exact output token amount.
* There are two subsets of a swap:
  * Single Swap: A swap, tokenIn > tokenOut, using a single pool. This is the most gas efficient option for a swap of this kind.
  * Multi-path Swaps: Swaps involving multiple paths but all executed in the same transaction. Each path can have its own (or the same) tokenIn/tokenOut.
* Boosted Pools and Liquidity Buffers enable gas effective swaps through capital efficient pools (see [Boosted Pool section](./boosted-pools.md))
* Balancer v2 used the concept of poolIds, this is no longer used in v3 which always uses pool address
* Balancers new [API](/data-and-analytics/data-and-analytics/balancer-api/balancer-api.md) can be used to access pool data