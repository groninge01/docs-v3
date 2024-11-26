---
title: Pools - Swap Events
---

# Get swap events for one or more pools

This query returns all Swap events for the two pools in the filter. One is a CowAMM pool and the other a v2 weighted pool. 
As they have different event types, we also use the expanded `GqlPoolSwapEventCowAmm` and `GqlPoolSwapEventV3` types to get CowAMM and "standard" pool specific swap datas.

```graphql
{
  poolEvents(
    where: {
      typeIn: [SWAP], 
      chainIn: [MAINNET], 
      poolIdIn: ["0xf08d4dea369c456d26a3168ff0024b904f2d8b91", "0x3de27efa2f1aa663ae5d458857e731c129069f29000200000000000000000588"]
      },
    first: 1000
  ) {
    type
    valueUSD
    timestamp
    poolId
    ... on GqlPoolSwapEventCowAmm {
      surplus {
        address
        amount
        valueUSD
      }
      fee {
        address
        amount
        valueUSD
      }
    }
    ... on GqlPoolSwapEventV3 {
      fee {
        address
        amount
        valueUSD
      }
    }
  }
}

```