---
title: User - Get pool balances
---

# Get pool balances for a user

This query returns all pools that the user has a balance. We differentiate between staked and wallet balances. 
Wallet balances mean that the user holds the BPT in his wallet. Staked balances mean that the BPT is staked in a supported service, such as a gauge or on Aura.

```graphql
{
  poolGetPools(where:{chainIn:[MAINNET], userAddress:"0x..."}){
    address
    userBalance{
      stakedBalances{
        balance
        balanceUsd
        stakingType
      }
      walletBalance
      walletBalanceUsd
      totalBalance
      totalBalanceUsd
    }
  }
}
```