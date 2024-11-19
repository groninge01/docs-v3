---
order: 3
title: Events
---

# Events

All Vault Events are detailed in [IVaultEvents](https://github.com/balancer/balancer-v3-monorepo/blob/4b85fc30fbbffc3a0b7aa84e1dc0b63082d0768e/pkg/interfaces/contracts/vault/IVaultEvents.sol)

For event based systems the following Vault events can be useful to construct/track pool state:

* Swap(address indexed pool, IERC20 indexed tokenIn, IERC20 indexed tokenOut, uint256 amountIn, uint256 amountOut, uint256 swapFeePercentage, uint256 swapFeeAmount);
   * A swap has occurred.

* LiquidityAdded(address indexed pool, address indexed liquidityProvider, AddLiquidityKind indexed kind, uint256 totalSupply, uint256[] amountsAddedRaw, uint256[] swapFeeAmountsRaw);
  * Liquidity has been added to a pool (including initialization).

* LiquidityRemoved(
        address indexed pool,
        address indexed liquidityProvider,
        RemoveLiquidityKind indexed kind,
        uint256 totalSupply,
        uint256[] amountsRemovedRaw,
        uint256[] swapFeeAmountsRaw
    );
  * Liquidity has been removed from a pool.

* AggregateSwapFeePercentageChanged(address indexed pool, uint256 aggregateSwapFeePercentage)
  * A protocol or pool creator fee has changed, causing an update to the aggregate swap fee.

* SwapFeePercentageChanged(address indexed pool, uint256 swapFeePercentage);
  * Emitted when the swap fee percentage of a pool is updated.

* PoolPausedStateChanged(address indexed pool, bool paused)
  * A Pool's pause status has changed.
