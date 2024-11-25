---
title: Rate Providers
order: 1
---

# Rate Providers

A rate provider is a contract that provides a rate relative to the underlying asset. For example, the rate of wstETH to stETH. Examples can be found in [this repo](https://github.com/balancer/code-review/tree/main/rate-providers).

::: tip
- Technical information on rate providers can be found [here](../../concepts/core-concepts/rate-providers.md)
- To submit your own rate provider contract for review, create an issue [here](https://github.com/balancer/code-review/issues/new?assignees=mkflow27&labels=request&projects=&template=review-request.yml)
- If a pool is created with tokens that use unreviewed rate providers, it will not show on [balancer.fi](https://balancer.fi/pools)
:::

## What are the requirements for a rate provider contract?
- Must implement the [`IRateProvider`](https://github.com/balancer/balancer-v3-monorepo/blob/main/pkg/interfaces/contracts/solidity-utils/helpers/IRateProvider.sol) interface, which simply requires a `getRate()` function that returns an 18 decimal fixed point number that is the exchange rate of the token to some other underlying token.
- If the rate provider or any underlying portion of the rate can be upgraded, changed, manipulated in any way it will not be owned by or executed by an EOA. It must be a multisig with a minimum set up of 3/5 signers.
- Timelock is encouraged but not required.
- Should not be susceptible to donation attacks.
- Should be monotonic (up only) in nature.

## When is a rate provider needed?
The primary use-case of a rate provider is the deployment of liquidity in a composable stable pool consisting of correlated or non-correlated assets containing a yield-bearing asset. If 50% or more of the tokens are yield-bearing, the pool is eligible to be flagged as a [core pool](/partner-onboarding/balancer-v2/core-pools.html) to receive a share of fees as voting incentives

## What are the steps needed to set up a rate provider with Balancer?
1. Build a rate provider based on the above mentioned requirements and consult the Balancer RP registry
2. Make an [issue](https://github.com/balancer/code-review/issues/new?assignees=mkflow27&labels=request&projects=&template=review-request.yml) against the RP Registry to start the code review process
3. Wait for the review to finish (takes approx. 1-2 weeks depending on the backlog)
4. Implement any needed changes that are required to pass the review
5. Upon merge the rate provider is vetted and a composable stable pool can be set up with the newly implemented rate provider set.


## Resources
- [Rate provider review](https://github.com/balancer/code-review/issues/new?assignees=mkflow27&labels=request&projects=&template=review-request.yml)
- [Rate provider registry](https://github.com/balancer/code-review/tree/main/rate-providers)
- [IRateProvider interface](https://github.com/balancer/balancer-v3-monorepo/blob/main/pkg/interfaces/contracts/solidity-utils/helpers/IRateProvider.sol)
