---
layout: post
title: "Scribbles"
author: "Bad"
permalink: /nfts/scribbles

---

## Scribbles

Scribbles has been programmed and minted as a contract on the Harmony blockchain. The contract has been designed to produce 1024 randomly generated artistic pieces using rectangles and squares relating to a hash generated at mint. All results are created during mint and everything is stored on-chain, within the single contract. As Scribbles do not rely on other services, they will continue to exist providing the Harmony network continues to exist (so they will out live you and me!)

No 3rd party hosts or off-chain solutions have been used to produce the outcome. A user simply communicates with the contract, mints for a fee of 100 $ONE (+ gas) and they are assigned their Scribble. A section of the [BadProjects website](https://badprojects.one/scribbles) has been crafted to allow contract interaction through the use of Metamask but a user can communicate directly with the contract too if they prefer.

Like what you hear so far? Go mint a Scribble [here](https://badprojects.one/scribbles/mint.html) or continue reading for more information about the contract and project!

The address of the live contract is:

```
0xDB18774dCa16F1c5C2F7Af640EBA3edb19343D7d
```

The transaction hash of the contract minting process is: 

```
0xc6e5f90c75316da2b449a8d2449d1cbfdf5db5358cc2c62815b908ebbe6633ab
```

The complete transaction can be viewed on the Harmony explorer here: [https://explorer.harmony.one/tx/0xc6e5f90c75316da2b449a8d2449d1cbfdf5db5358cc2c62815b908ebbe6633ab](https://explorer.harmony.one/tx/0xc6e5f90c75316da2b449a8d2449d1cbfdf5db5358cc2c62815b908ebbe6633ab)

The viewer will notice the following within the transactions (brief):

- Ownership is transferred to the minter.
- 10 Scribbles were randomly minted at this time and placed in the owners wallet. These can be seen in the screenshot below. Reserve function is boolean locked after completion so can never be called again. Function is also set to private and cannot be invoked from outside the contract.

A couple of things to note about the contract:

- Owner can mint 32 Scribbles for free. Once 32 have been minted, this function is locked and unusable again. 
- Owner cannot mint any (despite the limit) if the total supply is already maxed at 1024.
- The price is static and set at 100 $ONE forever.
- All generated data is stored in the contract, relating to the current owner. Once owned, the owner is free to do what they please with the token.
- No image data is stored off-chain, in fact no image ever really exists. The contract returns an SVG on demand and is built at request time.
- Image returned will always be the same due to the data used to generate.

![Reserves](/assets/images/nft_pages/reserve_collection.png)

Upcoming features!

A range of features have been planned as the project moves forward, including (but not limited to):

- a secondary marketplace for holders to buy, sell and bid on Scribbles
- a collection of data including all traits, their counts and the rarer Scribbles that exist (unlikely to exist until Scribbles sell out)
- airdrops to holders in the form of upcoming projects, and access to programming resources