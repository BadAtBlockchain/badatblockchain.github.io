---
layout: post
title: "Launching My First HRC721 Contract"
author: "Bad"
tags: misc
---

## Launching My First HRC721 Contract

The 4th of August, 2021 marked a milestone in my Solidity development process. It was the day I released my first custom smart contract on the Harmony network. The contract was written entirely by me (following ERC721 standards of course) and gives users of the mainnet the opportunity to mint a randomly generated, 100% on-chain NFT to add to their collection. Alongside this release, I released a platform to host all of my upcoming projects, ultimately centralizing my decentralized work around a brand, this brand has been named Bad Projects (but don't worry, I'll put in my love and effort to make them good, of course)!

The current progress of Bad Projects can be seen on the website [here](https://badprojects.one). Please note, I am not a web designer and my web development skillset was established 14 years ago - so practices may be out dated but it's functional! This is the first experience I have had with web3 and interacting with smart contracts via a UI so this is also a great learning experience for me alongside the Solidity development.

This post isn't to shill the newly launched project itself, however I have dedicated a page to the new NFT [here](https://badatblockchain.github.io/nfts/scribbles) so please go check it out and let me know what you think! I did want to write up some notes regarding the minting process of my contract and hopefully report some "good" and "bad" points about the process for the reader to hopefully assist them in reaching their goals too...

### So... First Off, the Good!

Ultimately, the best thing is I launched a contract and built a custom minting website. How cool is that!! But some other specifics, in terms of good learning experiences are:

- I learnt how to launch to the mainnet using a metamask wallet and Truffle!
- Created a web app using web3, raw html + JS, bootstrap for visuals and implemented a range of features to interact with the smart contract.
- I can communicate with the smart contract to display a holders token collection as well as the entire minted collection so far!
- Although listed in the bad section below, I learnt what happens when you mint buggy code and how potentially devastating the replacement process can be (in this case, I was lucky and it was easy to manage).
- I dived deeper into writing logic tests for every aspect of my contract (except one bug, silly me... keep reading).
- I unlocked a hidden part of myself which believed in my work enough to launch something into the public eye. This is a huge step for me personally and one I hope to run with in the future! Honestly, it feels great knowing I set out to learn Solidity and smart contracts and I am now here, less than 3 months later with a minted contract on the Harmony mainnet! I'm so stoked.
- The final thing, and something important to raise... the gas price of minting a contract to harmony was literally 0.005 ONE. It was ridiculously cheap. I would highly recommend looking into this network if you haven't already. I love the ETH network, but knowing I can dev, mint and trade for less than a penny per transaction, what's not to love AND it was all done in 4 blocks, ~13 seconds at the time!

![Gas for mint](/assets/images/20210805/gas_price_truffle.png)

### And Now, the Bad

Sadly, not everything was super smooth and flawless. Despite the result looking positive there were a few hiccups:

- The worst issue was a bug in my Solidity code. After minting to the mainnet I realised I had a logic bug in a function that allows the owner to mint tokens for free. Now, the contract is coded to prevent the owner minting more than 32 tokens for free (providing the total supply is less than max (1024)). Now, naturally, each time this function is called it checks a counter value to ensure the checks pass. I had forgotten to increment the counter for each mint, so technically the owner could mint an unlimited amount (whilst still adhering to the 1024 limit). Great for free airdrops, however, doesn't make sense when building a community and attempting attract holders.
- Once the bug was noticed, I had to patch the code and redeploy. Luckily for me, the launch was relatively quiet and managing the minted tokens was easy - so no one lost out on data or tokens. Once replaced, I updated the website to point at the new contract and away I went again! Although frustrating and potentially damaging, the outcome was purely a good learning experience and just made me realised one thing "read and read and read your smart contract code". I thought I had caught pretty much all of the main logic cases in unit testing, however, I missed this one which could have potentially been devastating and trigger a huge amount of FUD for the project and upcoming releases.

Thank you for getting this far. I have no doubt missed some good and bad points about the overall process but as a basic TLDR:

- read your contract code, minting is permanent.
- believe in yourself - you can do this too!

Thanks again. 