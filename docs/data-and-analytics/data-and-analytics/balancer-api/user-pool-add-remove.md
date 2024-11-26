---
title: User - Get add and remove events for a pool
---

# Get add and removes for a user
This query returns all add and removes of a specific user for a specific pool. It also returns the token amounts with which the user has added or removed liquidity from the pool.

```graphql
{
  poolEvents(
    where: {
      typeIn: [ADD, REMOVE], 
      chainIn: [MAINNET], 
      poolIdIn: ["0x3de27efa2f1aa663ae5d458857e731c129069f29000200000000000000000588"], 
      userAddress: "0x741AA7CFB2c7bF2A1E7D4dA2e3Df6a56cA4131F3"
    }
    first: 1000
  ) {
    type
    valueUSD
    timestamp
    poolId
    ... on GqlPoolAddRemoveEventV3{
      tokens{
        address
        amount
        valueUSD
      }
    }
  }
}

```