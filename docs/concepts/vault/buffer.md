---
title: Liquidity Buffers
order: 9
---

::: info
This page is a work in progress
:::

# ERC4626 Liquidity Buffers

Liquidity Buffers, an internal mechanism of the Vault, facilitate liquidity for pairs of an ERC4626 `asset` (underlying token like DAI) and the ERC4626 Vault Token (like waDAI). The Balancer Vault provides additional liquidity, enabling the entry into the ERC4626 Vault Token positions without the need to wrap or unwrap tokens, thereby avoiding higher gas costs.

ERC4626 liquidity buffers trade on a `previewDeposit` & `previewMint` basis. Meaning given an amount of 100 DAI, the liquidity buffer gives out waDAI based on the return value from `previewDeposit(100 DAI)`.

A significant benefit of the Vault's liquidity buffers is that Liquidity Providers (LPs) can now provide liquidity in positions of [100% boosted pools](/concepts/explore-available-balancer-pools/boosted-pool.html) (two yield-bearing assets) while simultaneously adding gas efficient batch-swaps routes.

It's important to note that ERC4626 liquidity buffers are not Balancer Pools. They are a concept internal to the Vault and only function with Tokens that comply with the ERC4626 Standard.

action: this section can use more wording work.
:::info
If your organization is a DAO and you're seeking to enhance liquidity for your ERC4626 compliant token, Balancer's ERC4626 liquidity buffers can be a valuable tool. By providing POL to these buffers, you can enable LPs of your token to gain increased access to yield-bearing tokens. This arrangement allows LPs to concentrate on [boosted pools](/concepts/explore-available-balancer-pools/boosted-pool.html), while your DAO contributes POL to the buffer.
:::


## Adding liquidity to a buffer
Liquidity can be added to a buffer for a specific token pair. This is done by invoking the `addLiquidityToBuffer` function, where you designate the ERC4626 Token as the buffer reference. You also specify the amounts of both the wrapped and underlying tokens that you want to add to the buffer. It's important to note that a buffer can still function with zero liquidity. It can be used to wrap and unwrap assets, meaning that even an empty buffer can facilitate swaps through the Vault.
```solidity
/**
 * @notice Adds liquidity to a yield-bearing token buffer (linear pools embedded in the vault).
 * @param wrappedToken Address of the wrapped token that implements IERC4626
 * @param amountUnderlyingRaw Amount of underlying tokens that will be deposited into the buffer
 * @param amountWrappedRaw Amount of wrapped tokens that will be deposited into the buffer
 * @param sharesOwner Address of the contract that will own the liquidity.
 *        Only this contract will be able to remove liquidity from the buffer
 * @return issuedShares the amount of tokens sharesOwner has in the buffer, denominated in underlying tokens
 *         (This is the BPT of the vault's internal "Linear Pools")
*/
function addLiquidityToBuffer(
    IERC4626 wrappedToken,
    uint256 amountUnderlyingRaw,
    uint256 amountWrappedRaw,
    address sharesOwner
) external returns (uint256 issuedShares);
```

## Removing liquidity from a buffer 
After you've added liquidity to a buffer, you have the option to remove a specified amount based on . This is done by invoking the function `removeLiquidityFromBuffer`. This function will subsequently burn a specified amount of your bufferShares and return the corresponding amount of tokens that you had previously provided.
```solidity
/**
 * @notice Removes liquidity from a yield-bearing token buffer (an embedded "Linear Pool").
 * @dev Only proportional withdrawals are supported, and removing liquidity is permissioned.
 * @param wrappedToken Address of a wrapped token that implements IERC4626
 * @param sharesToRemove Amount of shares to remove from the buffer. Cannot be greater than sharesOwner
 *        total shares
 * @return removedUnderlyingBalanceRaw Amount of underlying tokens returned to the user
 * @return removedWrappedBalanceRaw Amount of wrapped tokens returned to the user
*/
function removeLiquidityFromBuffer(
    IERC4626 wrappedToken,
    uint256 sharesToRemove
) external returns (uint256 removedUnderlyingBalanceRaw, uint256 removedWrappedBalanceRaw);
``` 


## Using a buffer to swap. 
The swapper has the responsibility to decide whether a specific swap route should use Buffers by indicating if a given `pool` is a buffer. Rember: You can always use a buffer even it is does not have liquidity (instead it will simply wrap or unwrap). This is done by setting the boolean entry in the `SwapPathStep` struct.

The `pool` param in this particular case is the wrapped Tokens entrypoint. Meaning the address where a user would call deposit in. In the case of Aave it would the waUSDC. 
``` solidity
struct SwapPathStep {
    address pool;
    IERC20 tokenOut;
    // if true, pool is a yield-bearing token buffer. Used to wrap/unwrap tokens if pool doesn't have
    // enough liquidity
    bool isBuffer;
}
```

The availability of sufficient liquidity in the buffer affects the gas cost of the swap. If the buffer lacks enough liquidity, the gas cost increases. This is because the Vault has to get the additional liquidity from the lending protocol, which involves either depositing into or withdrawing from it.

Buffers aim to streamline the majority of trades by eliminating the need to wrap or unwrap the swapper's tokens. Instead, they route these tokens through the Balancer trade paths. 

In the case of trading DAI to USDC via (DAI-waDAI Buffer, waDAI - waUSDC Boosted pool, USDC-waUSDC Buffer) for a 3 hop trade the `SwapPathExactAmountIn` would look like:
```solidity
struct SwapPathExactAmountIn {
        IERC20 tokenIn;
        // for each step:
        // if tokenIn == pool, use removeLiquidity SINGLE_TOKEN_EXACT_IN
        // if tokenOut == pool, use addLiquidity UNBALANCED
        SwapPathStep[] steps;
        uint256 exactAmountIn;
        uint256 minAmountOut;
    }
```

```solidity
SwapPathExactAmountIn({
    tokenIn: address(DAI),
    steps: [
        SwapPathStep({
            pool: address(waDAI), // the address where the Vault calls `deposit` or `mint` depending on SWAP_TYPE and Buffer liquidity
            tokenOut: IERC20(address(waDAI)),
            isBuffer: true
        }),
        SwapPathStep({
            pool: address(boostedPool)
            tokenOut: IERC20(address(waUSDC)),
            isBuffer: false
        }),
        SwapPathStep({
            pool: address(waUSDC)
            tokenOut: IERC20(address(USDC)),
            isBuffer: true
        })
    ],
    exactAmountIn: uint256(myExactAmountIn) // your defined amount
    minAmountOut: uint256(myMinAmountOut) // your calculated min amount out
})
```


The trade will execute regardless if the Buffer has enough liquidity or not. Remember: If the buffer does not have enough liquidity it will simply additionally wrap or unwrap (and incur additional gas cost).

### Swapping DAI to USDC via 3 hops.
Let's consider a swap from 10k DAI to USDC. the exchangeRate of 1waDAI - DAI is 1.1 & exchangeRate for waUSDC - USDC is 1.1. Involved pools & Buffers are:
- DAI - waDAI Buffer
- waDAI - waUSDC Boosted Pool (100% boosted)
- USDC - waUSDC Buffer

Considering these three pools only, the way to swap through them is via a `swapExactIn` operation on the BatchRouter.

1. Swap DAI to waDAI via the DAI - waDAI Buffer
2. Swap waDAI to waUSDC via the waDAI - waUSDC 100% Boosted pool
3. swap waUSDC to USDC via the USDC - waUSDC Buffer

#### Balances with enough buffer liquidity available

Balances of pool & buffers before the batchswap:

| DAIBufferBalance before Swap         | DAIBufferBalance after Swap                                      | waDAIBufferBalance before Swap         | waDAIBufferBalance after Swap |
| --------                             | -----------------------------------------------------            | --------                               | -  |
| 110k DAI                             | 120k DAI <span style="color:green">(+10k DAI)</span>             | 91k waDAI                              | 81909.1 waDAI  <span style="color:red">(-9090.9 waDAI)</span> |

| BoostedPool waDAI Balance before Swap|  BoostedPool waDAI Balance after Swap                            | BoostedPool waUSDC Balance before Swap | BoostedPool waUSDC Balance after Swap |
| --------                             | -----------------------------------------------------            | --------                               | - |
| 900k waDAI                           | 909090.9 waDAI  <span style="color:green">(+9090.9 waDAI)</span> | 900k waUSDC                            | 890909.1 waUSDC <span style="color:red">(-9090.9 waUSDC)</span> |

| USDCBufferBalance before Swap        | USDCBufferBalance after Swap                                     | waUSDCBufferBalance before Swap        | waUSDCBufferBalance after Swap |
| --------                             | -----------------------------                                    | --------                               | - |
| 100k USDC                            | 90000 USDC <span style="color:red">(-10k USDC)</span>            | 91k waUSDC                             | 100090.9 waUSDC <span style="color:green">(+9090.9 waUSDC)</span> |


### Balances without enough buffer liquidity available in DAI - waDAI buffer

Consider now an EXACT_IN trade of 60k DAI to USDC. The DAI - waDAI buffer does not have enough liquidity to support the trade from it's reserves, so it calls into the waDAI contract to wrap DAI to waDAI (amount) and additionally rebalances the buffer to balanced reserves.

| DAIBufferBalance before Swap         | DAIBufferBalance after Swap                                              | waDAIBufferBalance before Swap         | waDAIBufferBalance after Swap |
| --------                             | -----------------------------------------------------                    | --------                               | -  |
| 50k  DAI                             | 44761 DAI <span style="color:red">(-5239 DAI)</span>                     | 40k waDAI                              | 40692 waDAI  <span style="color:green">(+692 waDAI)</span> |

| BoostedPool waDAI Balance before Swap|  BoostedPool waDAI Balance after Swap                                    | BoostedPool waUSDC Balance before Swap | BoostedPool waUSDC Balance after Swap |
| --------                             | -----------------------------------------------------                    | --------                               | - |
| 900k waDAI                           | 954545.45 waDAI  <span style="color:green">(+54545.45 waDAI)</span>      | 900k waUSDC                            | 845454.55 waUSDC <span style="color:red">(-54545.45 waUSDC)</span> |

| USDCBufferBalance before Swap        | USDCBufferBalance after Swap                                             | waUSDCBufferBalance before Swap        | waUSDCBufferBalance after Swap |
| --------                             | -----------------------------                                            | --------                               | - |
| 100k USDC                            | 40000 USDC <span style="color:red">(-60000 USDC)</span>                  | 91k waUSDC                             | 145545.45 waUSDC <span style="color:green">(+54545.45 waUSDC)</span> |

Even though the DAI - waDAI buffer did not have enough liquidity the trade was successfully routed via Balancer. The difference now is that the Vault utilized the buffer internal wrapping capability to wrap DAI into waDAI & rebalanced itself.


:::info TODO
add DAI - WETH swap route (where the last step is a trade with pool rather than buffer)
:::

<style scoped>
table {
    display: table;
    width: 100%;
}
</style>