

# Sepolia Deployment Addresses

::: info More Details
Balancer v3 is in active development. This page shows the latest version of deployed contracts.
:::

## Pool Factories

| Name             | Address                                    | Deployment |
|------------------|--------------------------------------------|------------|
| StablePoolFactory| 0xcB107E7075add7a95ae7192c052b4e6814bf0ad5 | 11          |
| MockStablePool   | 0x5906b98aE7928676019D2B880F9C556bDEC5F4AA | 11          |
| WeightedPoolFactory| 0x9aAD2c188b4eACcA85C44E7A9250dDADcae1A2E9 | 11          |
| MockWeightedPool   | 0x88Ab7C08CcD43788738005e3da95598f9dFf4c16 | 11          |


## Core


| Name                 | Address                                    | Deployment |
|----------------------|--------------------------------------------|------------|
| ProtocolFeeController| 0x0cf640653230a5fA0edb4627660D62eefaBfF8cE | 11          |
| VaultAdmin           | 0x2AD9162D9b388b75eB40cBF996AbE8E968670c5C | 11          |
| VaultExtension       | 0x59657ebA33Bf0a7dBf03E74a242e6b4F58D6003a | 11          |
| Vault                | 0xBC582d2628FcD404254a1e12CB714967Ce428915 | 11          |
| Router               | 0x4D2aA7a3CD7F8dA6feF37578A1881cD63Fd3715E | 11          |
| BatchRouter          | 0x4232e5EEaA16Bcf483d93BEA469296B4EeF22503 | 11          |
| BufferRouter         | 0xD907aFAF02492e054D64da3A14312BdA356fc618 | 11          |
| CompositeLiquidityRouter | 0x2F118d8397D861354751709e1E0c14663e17F5C1 | 11          |
| VaultExplorer        | 0xa9F171e84A95c103aD4aFAC3Ec83810f9cA193a8 | 11          |


## Authorization

## Gauges and Governance

## Ungrouped Active/Current Contracts
    
| Name                    | Address                                    | Deployment |
|-------------------------|--------------------------------------------|------------|
| FeeTakingHookExample     | 0x790ae803b6c0467C6A4cbDc6d6d712DE34CfdB76 | 11          |
| ExitFeeHookExample       | 0x2Aa9D4066DAe16ef001765efF2cA8F41Bde0b019 | 11          |
| DirectionalFeeHookExample| 0xD9e535a65eb38F962B84f7BBD2bf60293bA54058 | 11          |
| LotteryHookExample       | 0x0E85194F9eD75F0EFf2b89B73b6AD3053be03853 | 11          |


    
# Deprecated Contracts

These deployments were in use at some point, and may still be in active operation, for example in the case of pools created with old factories.  In general it's better to interact with newer versions when possible.

#### If you can only find the contract you are looking for in the deprecated section and it is not an old pool, try checking the deployments tasks to find it or ask in the Discord before using a deprecated contract.


    
<style scoped>
table {
    display: table;
    width: 100%;
}
table th:first-of-type, td:first-of-type {
    width: 30%;
}
table th:nth-of-type(2) {
    width: 40%;
}
td {
    max-width: 0;
    overflow: hidden;
}
</style>

