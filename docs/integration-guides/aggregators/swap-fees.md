---
order: 7
title: Swap Fees
---

# Swap Fees

:::info Work in progress
Dynamic Swap Fees and Hooks are still WIP and not finalized
:::

[Swap fees](../../concepts/vault/swap-fee.md) come in two different forms for v3 pools:

* [Static Swap Fee](../../concepts/vault/swap-fee.md#setting-a-static-swap-fee): 
  * Initially set as part of the pool registration. 
  * Authorized addresses can then change the value by invoking the vault.setStaticSwapFeePercentage(address pool, uint256 swapFeePercentage) function.
  * If the staticSwapFeePercentage is changed, it will emit an event: `SwapFeePercentageChanged(pool, swapFeePercentage);`
  * `setStaticSwapFeePercentage` can also be called as part of a regular [hook](../../concepts/core-concepts/hooks.md)
* [Dynamic Swap Fee](../../concepts/vault/swap-fee.md#dynamic-swap-fee):
  * At registration pools can be set up to use dynamic swap fees.
  * The Vault uses the `_getSwapFeePercentage(PoolConfig memory config)` to fetch the swap fee from the pool. This function can implement arbitrary logic.
  * Even when a pool is set to use dynamic swap fees, it still maintains a static swap fee. However, this static fee is not utilized.

The pseudo logic to determine how swap fee is calculated looks like:
```
swapFeePercentage =
     Pool has DynamicSwapFee => call DynamicSwapFeeHook in the pool
     else => load static Swap fee percentage from Vault
```