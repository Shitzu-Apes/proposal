# Proposal to transition SHITZU to Near

Hey all,

SHITZU is the very first meme token that is native to Aurora. It has been minted back then at the 1st of April '21 when Bastion held an April fools campaign. Via "Proof of Finger" consensus the community minted a total of 576,167,000 tokens. Ever since then the Shitzu community has been actively promoting the Near/Aurora ecosystem. Their [Telegram](https://t.me/Shitzu_Community) is also very active. For more information about the Shitzu community you should visit our [website](https://shitzuapes.xyz).

## The problem of being isolated on Aurora

The SHITZU token has always been isolated on Aurora without an existing bridging solution to bring it to other chains.

One might be wondering why it is not possible to use [Rainbow Bridge](https://rainbowbridge.app/). The problem with Rainbow Bridge is that it can only transfer native Near tokens (NEP-141) to Aurora and Ethereum. There is a [WIP solution](https://github.com/aurora-is-near/native-erc20-connector) to also allow Aurora native ERC-20 tokens to be bridged to Near, but development on this solution has been halted.
It might be a possibility to ask the Aurora team to fully implement and deploy this solution. However the novel design might lead to problems with interoperability across various smart contracts, if it involves sending tokens from Near to Aurora, because the bridging looks different.

Another solution would include usage of [Wormhole](https://wormhole.com/) as a bridging solution. Via [Portal Bridge](https://portalbridge.com/) it is possible to permissionlessly bridge any token across a wide range of chains. However due to stability/security reasons they stopped supporting Aurora at some point.

![](https://cdn.discordapp.com/attachments/899637214894518284/1186951208548704317/b19abda3-1241-4fe9-90d5-719a146a81e1.png?ex=65951d74&is=6582a874&hm=085435a61b6a282ffbad541f1d80f130170ce273879857e360d01c729551015d&)

## The proposed solution

The Shitzu core contributors came up with the idea to create a new NEP-141 token smart contract on Near (called SHITZUv2 as of now in this document) and migrate the existing SHITZU token (called SHITZUv1) via Aurora's possibility to do cross contract calls to Near smart contracts. This solution allows to have a seamless migration for the SHITZUv1 without users loosing money. Let me explain the technical details:

On Aurora a "bridging" smart contract will be deployed to perform the burn and mint mechanism to burn SHITZUv1 and mint SHITZUv2. For this purpose the [Aurora SDK](https://github.com/aurora-is-near/aurora-contracts-sdk) will be used to facilitate cross contract communication to the new SHITZUv2 smart contract.
When doing cross contract calls from Aurora to Near a new smart contract will be deployed on Near as a sub-account of the Aurora engine smart contract:

![](https://user-images.githubusercontent.com/13788876/218560980-c685accd-a0c1-45ac-9885-9d5715b32f20.png)

SHITZUv2 will have a `mint` function that is entirely controlled by this sub-account, so no other account will be able to call it. The SHITZUv1 bridging smart contract serves as a one way bridge. It is not possible to burn SHITZUv2 and mint SHITZUv1. The SHITZUv2 smart contract will always be available and it doesn't matter when people decide to initiate their SHITZUv1 migration.
In order for users to perform the migration a BOS component will be developed.

Once this proposal passed and the smart contracts for SHITZUv2 have been deployed both on Aurora and Near side, we will immediately migrate a big chunk of SHITZUv1 and create a liquidity pool for SHITZUv2 on Ref Finance with the same price as SHITZUv1 on Aurora. If the price of SHITZUv1 is lower than the price of SHITZUv2 this represents an arbitrage opportunity to buy SHITZUv1, migrate it and sell for more than the initial token amount that has been used to do the swap for SHITZUv1. With this arbitrage mechanism it is ensured that the price of SHITZUv1 will never be lower than SHITZUv2. If the price of SHITZUv2 is lower than the price of SHITZUv1 no such arbitrage opportunity exists, because the migration mechanism only works in a single direction. In order to make sure that some liquidity persists for SHITZUv1 we will lock some of our LP shares on Trisolaris (need to check with Aurora team, if there is a burn address to where we can send LP shares).

SHITZUv2 can be bridged back to Aurora via Rainbow Bridge, which produces a version of the SHITZU token, that is different from SHITZUv1. This effectively means there will be two versions of SHITZU on Aurora once someone registered SHITZUv2 via Rainbow Bridge. The bridging itself is permissionless, so anyone can initiate this, but we will discourage it for the beginning of the migration phase to prevent confusion. Later when most of SHITZUv1 have been migrated we can also create new liquidity pools on Trisolaris.

I (Mario) have been already working with the Aurora SDK and have been the co-author of an [extended bridging example involving Fungible Token refunds](https://github.com/aurora-is-near/aurora-contracts-sdk/pull/13/commits/6188e540a4d5c9df293b3f2162708ad086fa0f8d). At my work at Orderly I also implemented a cross-chain orderbook using the Aurora SDK. This is orderbook is not live and development has been halted due to market conditions, but it allowed me to get extensive knowledge about how to develop the proposed migration solution for SHITZU.

## Vote for this proposal

We want to do an on chain vote via EasyPoll on BOS. In order for this proposal to get approved by the Near & Aurora community a minimum quorum of 200 votes is required & at least 70% "yes" votes.
