---
title: Rate scaling
order: 3
---

# Rate scaling

With the successful rollout of [The Merge](https://ethereum.org/roadmap/merge) and the adoption of [ERC-4626](https://docs.openzeppelin.com/contracts/4.x/erc4626), the ecosystem has seen a proliferation of yield bearing tokens. Recognizing the pivotal role that LSTs will play in the liquidity landscape moving forward, Balancer seeks to position itself as the definitive yield-bearing hub in DeFi.

To facilitate the adoption of yield bearing liquidity, Balancer abstracts the complexity of managing LSTs by centralizing all rate scaling in the vault, providing all pools with uniform rate scaled balances and input values by default, drastically reducing LVR and ensuring that YB yield is not captured by arbitrage traders.

## What is a token rate
The classical example of a token with a rate is Lido's [wstETH](https://help.lido.fi/en/articles/5231836-what-is-lido-s-wsteth). As [stETH](https://help.lido.fi/en/articles/5230610-what-is-steth) accrues value from staking rewards, the exchange rate of wstETH -> stETH grows over time.

TODO: something about LVR mitigation as a result of applying token rates, possibly a link to a dedicated page

## How does the Balancer Vault utilize token rates

TODO: scaling done in vault to standardize approach, no longer handled in the pool. allow fo simplicity in pool

Besides [decimal scaling](decimal-scaling.md) a tokens rate is taken into account in Balancer under in the following scenarios:
- price computation as part of Stable & boosted pools
- Yield fee computation when tokens with a rate are part of pools

A token's rate is defined as a 18 decimal fixed point number. It represents a factor of value difference relative to it's underlying. For example a rate of 11e17 of rETH means that 1reth has the value of 1.1 eth.


## Creating a pool with tokens that have rates

On pool [register](https://github.com/balancer/balancer-v3-monorepo/blob/main/pkg/interfaces/contracts/vault/IVaultExtension.sol#L116C9-L116C20) a [TokenConfig](https://github.com/balancer/balancer-v3-monorepo/blob/main/pkg/interfaces/contracts/vault/VaultTypes.sol#L66-L70) is provided for each of the pool's tokens.
To define a token with a rate, specify the token type as  `TokenType.WITH_RATE`. Additionally, you must provide a `rateProvider` address that implements the `IRateProvider` interface. Refer to [Token types](/concepts/vault/tokentypes.html) for a detailed explanation on each token type.  

:::info
`ERC-4626` vault tokens do not require an external `rateProvider`. For `ERC-4626` tokens, you can provide the `ZERO_ADDRESS` for the `rateProvider` function argument.
:::

## Rate scaling usage
Rate scaling technically is used on every `swap`, `addLiqudity` & `removeLiquidity` operations. If the token has been registered as a `TokenType.WITH_RATE` an external call to the Rate Provider is made via `getRate` if the `TokenType.STANDARD` is selected the rate is set as `1e18`. These rates are used to upscale the `amountGiven` as part of the Vault's primitives.
:::info
1. Calling a swap has amount as [`amountGivenRaw`](https://github.com/balancer/balancer-v3-monorepo/blob/main/pkg/interfaces/contracts/vault/VaultTypes.sol#L112)
2. [`AmountGivenRaw` is upscaled](https://github.com/balancer/balancer-v3-monorepo/blob/main/pkg/vault/contracts/Vault.sol#L230C14-L230C33)
3. [`AmountGivenScaled18`](https://github.com/balancer/balancer-v3-monorepo/blob/main/pkg/vault/contracts/Vault.sol#L332) is forwarded to the pool.
4. Rates are undone before returning either `amountIn` or `amountOut`
:::


