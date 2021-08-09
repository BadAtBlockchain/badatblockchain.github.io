---
layout: post
title: "Interacting With Contract A From Contract B"
author: "Bad"
tags: misc
---

## Interacting With Contract A From Contract B (part 1)

#### Interact With An Already Deployed Contract

Let's get back to the technical stuff. Something I have been dabbling with recently is interacting with target contracts from another contract. My main drive for this was studying and researching NFT's. If you aren't familiar with what an NFT is, then I won't bore you but go look. Quick. Honestly, you're still early and there are a tonne of great projects already available.

For my explanation I am going to be using my personal favourite NFT project and their code rather than rolling out my own. I figured this is probably easier as it's already live and I can link to examples that the reader can also check out themselves to fact check my work (this is 100% important. Doing your own research (DYOR) is the only thing that will keep you going in crypto/NFTs in my opinion. Without it, we are just gambling... so don't do that).

#### Contract A - Wicked Craniums

First off, I want to introduce you to the Wicked Craniums contract code:

```
pragma solidity 0.7.0;

/**
 * @title WickedCraniums contract
 * @dev Extends ERC721 Non-Fungible Token Standard basic implementation
 */
contract WickedCraniums is ERC721, Ownable {
    using SafeMath for uint256;

    string public PROV = "b80d60a4defcca5af3ed6526d8c0f86089b9400659c89da2b2725b32f8686d4a";
    uint256 public constant craniumPrice = 60000000000000000; // 0.06 ETH
    uint public constant maxCraniumPurchase = 100;
    uint256 public MAX_CRANIUMS = 10762;
    bool public saleIsActive = false;

    constructor() ERC721("TheWickedCraniums", "TWC") {
    }

    function withdraw() public onlyOwner {
        uint balance = address(this).balance;
        msg.sender.transfer(balance);
    }

    function reserveCraniums() public onlyOwner {        
        uint supply = totalSupply();
        uint i;
        for (i = 0; i < 40; i++) {
            _safeMint(msg.sender, supply + i);
        }
    }
    
    function flipSaleState() public onlyOwner {
        saleIsActive = !saleIsActive;
    }
    
    function setBaseURI(string memory baseURI) public onlyOwner {
        _setBaseURI(baseURI);
    }

    function mintCraniums(uint numberOfTokens) public payable {
        require(saleIsActive, "Sale must be active to mint Craniums");
        require(numberOfTokens <= maxCraniumPurchase, "Can only mint 100 tokens at a time");
        require(totalSupply().add(numberOfTokens) <= MAX_CRANIUMS, "Purchase would exceed max supply of Craniums");
        require(craniumPrice.mul(numberOfTokens) <= msg.value, "Ether value sent is not correct");
        
        for(uint i = 0; i < numberOfTokens; i++) {
            uint mintIndex = totalSupply();
            if (totalSupply() < MAX_CRANIUMS) {
                _safeMint(msg.sender, mintIndex);
            }
        }
    }

}
```

Ref: https://etherscan.io/address/0x85f740958906b317de6ed79663012859067E745B#code

Pretty small right? Just note the classes the contract is inheriting - there is more code in the compiled solution, however this is just the code I want to focus on as this is their actual ERC721 contract. We can break this down a little but most of it is self explanatory:

- ERC721 token, following standards using OpenZeppelin contracts as a base. 

- Token is Ownable to allow privileged functions only.

- A range of contract variables help set up the environment (hard limit cap, static price, provenance hash for integrity and holder trust, etc).

- The contract constructor passes in the name and symbol for the collection, initialising the ERC721 token.

- The first function included exists to allow the contract owner to withdraw funds from the contract when ready. Some quick math tells us that: 

  ```
  10762 * 0.06 = 645.72
  ```

  So, with a balance of 654.72 ETH on sellout, we definitely want a withdraw function up and running!

- Next, a reserve function can be seen. Fairly straight forward, when live, before the sale, the owner can run through and set aside 40 tokens for themselves. Pretty common approach, some projects do more, some do less. In this case, the contract can get 0.37% of total supply, free of charge. These were used for airdrops, marketing and the owners can just retain some value for future sales.

  The only thing I will note here, is the owner could have run this many times. There isn't a bool or state variable to say this has been run. Perhaps an oversite, but looking at the owners wallet, they don't own enough craniums themselves to have abused this - so don't over think it! Not to mention, the Cranium developers love nothing more than giving back to the community, so there was likely 0 intent of using this function more than once - regardless of the code.

- A function to flip the sale state from true to false, or false to true. Simply used to trigger a fair launch on time.

- Common approach to ERC721 tokens is using URI's for hosting metadata. This function is used to set the values called by the OpenSea marketplace to pull down each tokens data to populate their page. Ref: https://docs.opensea.io/docs/metadata-standards

  By calling the baseURI function of the contract, we can see the base is set too: https://raw.githubusercontent.com/recklesslabs/wickedcraniums/main
  If we then append a token ID to this (in a similar manner to OpenSea calling tokenURI(0) or tokenURI(1)) we see: https://raw.githubusercontent.com/recklesslabs/wickedcraniums/main/0

  ```
  {
    "name": "Wicked Cranium #0",
    "external_link": "https://wickedcranium.com/",
    "image": "https://bafkreicpcfvpxmu5kphjw67lvcvfqj543axqwgo65an7pucvgzg6l3cpwe.ipfs.dweb.link",
    "attributes": [
      {
        "trait_type": "Background",
        "value": "Scrub"
      },
      {
        "trait_type": "Body",
        "value": "LeopardO"
      },
      {
        "trait_type": "Eyes",
        "value": "Trippy"
      },
      {
        "trait_type": "Clothes",
        "value": "VarsityJacket"
      },
      {
        "trait_type": "Head",
        "value": "Leprechaun"
      },
      {
        "trait_type": "Mouth",
        "value": "Smoking"
      }
    ]
  }
  ```

  Now we have our metadata, we can see all the Cranium traits and of course the image that is forever hosted on IPFS: https://bafkreicpcfvpxmu5kphjw67lvcvfqj543axqwgo65an7pucvgzg6l3cpwe.ipfs.dweb.link

  <img src="https://bafkreicpcfvpxmu5kphjw67lvcvfqj543axqwgo65an7pucvgzg6l3cpwe.ipfs.dweb.link/" alt="img" style="zoom: 15%;" />

   

- Finally, we have our mint function. Pretty straight forward. Checks a couple of conditions, makes sure enough money has been sent for the target amount of craniums the user wants to mint. It loops this count and grants the address ownership of that value.

Now the contract has been launched and we have a quick understanding of the code within the contract - we can now look at talking to it. 

Contract: 0x85f740958906b317de6ed79663012859067E745B

#### Contract B - Wicked Stallions

Craniums did an airdrop to allow all holders of a cranium token to mint a companion token, the Wicked Stallions. These tokens were free and simply cost the minter the price of gas. I was able to mint 6 stallions to accompany my craniums whilst they play poker and stay wicked in the metaverse! In similar fashion to the above, let's have a quick look at the source code:

```
pragma solidity ^0.8.0;

abstract contract TWC {
  function ownerOf(uint256 tokenId) public virtual view returns (address);
  function tokenOfOwnerByIndex(address owner, uint256 index) public virtual view returns (uint256);
  function balanceOf(address owner) external virtual view returns (uint256 balance);
}

contract WickedStallions is ERC721Enumerable, Ownable {  
  
  TWC private twc = TWC(0x85f740958906b317de6ed79663012859067E745B); 
  string public WickedStallionsProvenance;
  bool public saleIsActive = false;
  uint256 public maxStallions = 10762;
  string private baseURI;

  constructor() ERC721("TheWickedStallions", "TWS") {
  }

  function setProvenanceHash(string memory provenanceHash) public onlyOwner {
    WickedStallionsProvenance = provenanceHash;
  }

  function isMinted(uint256 tokenId) external view returns (bool) {
    require(tokenId < maxStallions, "tokenId outside collection bounds");
    return _exists(tokenId);
  }

  function _baseURI() internal view override returns (string memory) {
    return baseURI;
  }
  
  function setBaseURI(string memory uri) public onlyOwner {
    baseURI = uri;
  }

  function flipSaleState() public onlyOwner {
    saleIsActive = !saleIsActive;
  }

  function mintStallions(uint256 startingIndex, uint256 totalStallionsToMint) public {
    require(saleIsActive, "Sale must be active to mint a Stallion");
    require(totalStallionsToMint > 0, "Must mint at least one Stallion");
    uint balance = twc.balanceOf(msg.sender);
    require(balance > 0, "Must hold at least one Cranium to mint a Stallion");
    require(balance >= totalStallionsToMint, "Must hold at least as many Craniums as the number of Stallions you intend to mint");
    require(balance >= startingIndex + totalStallionsToMint, "Must hold at least as many Craniums as the number of Stallions you intend to mint");

    for(uint i = 0; i < balance && i < totalStallionsToMint; i++) {
      require(totalSupply() < maxStallions, "Cannot exceed max supply of Stallions");
      uint tokenId = twc.tokenOfOwnerByIndex(msg.sender, i + startingIndex);
      if (!_exists(tokenId)) {
        _safeMint(msg.sender, tokenId);
      }
    }
  }
}
```

Ref: https://etherscan.io/address/0x45d8f7db9b437efbc74ba6a945a81aaf62dceda7#code

From the jump we can instantly see some similarities with the contract but everything appears to be structured and written in a slightly different way. Perhaps the developers learnt some new tricks since the Cranium launch, maybe they used a different base project as their start point. I don't know, but the code acts and works in a similar way - just with a few more requirements! I recommend the reader to have a little read of the above and see how much you recognise after I went over the above points. Some of the contract code should now be understandable and hopefully give you a partial understanding of how the stallions work at contract level. I am going to focus on the code used to interact with the Cranium contract. This is where my interest lies in this post.

There are a few areas that jump out to me for this interaction. For each one I will post the snippet and a brief explanation of my understanding and why it is required.

```
abstract contract TWC {
  function ownerOf(uint256 tokenId) public virtual view returns (address);
  function tokenOfOwnerByIndex(address owner, uint256 index) public virtual view returns (uint256);
  function balanceOf(address owner) external virtual view returns (uint256 balance);
}
```

Abstract contract used as a form of declaration for the target contract. As calls are done without compiling the original contract source, the contract needs a form of declaration in order to actual interact and call specific functions. The functions that have been declared all reside in the ERC721 base contract of the Cranium code, so you won't have noticed them when dissecting the source code earlier in the post.

```
TWC private twc = TWC(0x85f740958906b317de6ed79663012859067E745B); 
```

Using the abstract contract declaration above, the contract can be referenced correctly in the stallions contract via it's address. This address is hard coded as it will never change. Once a reference to the craniums contract has been made, the abstract contracts functions can be used to can data from the real contract.

```
uint balance = twc.balanceOf(msg.sender);
require(balance > 0, "Must hold at least one Cranium to mint a Stallion");
require(balance >= totalStallionsToMint, "Must hold at least as many Craniums as the number of Stallions you intend to mint");
```

The above checks exist in the mint() function of the stallions contract. These ensure that the rules of minting a stallion are met, which were specifically:

"For every 1 cranium a user owns, they may mint a stallion for free (+ gas)". As we can see above, this does exactly that. We get the balance of the current transaction caller from the craniums contract. If this returned value is greater than 0 and also greater than the requested amount of stallions to mint, then the function continues.

```
require(balance >= startingIndex + totalStallionsToMint, "Must hold at least as many Craniums as the number of Stallions you intend to mint");

for(uint i = 0; i < balance && i < totalStallionsToMint; i++) {
    require(totalSupply() < maxStallions, "Cannot exceed max supply of Stallions");
    uint tokenId = twc.tokenOfOwnerByIndex(msg.sender, i + startingIndex);
    if (!_exists(tokenId)) {
	    _safeMint(msg.sender, tokenId);
    }
}
```

This bit of code initially tripped me up and to be honest, it still sort of does. I believe the web app that was used to mint the stallions might of done some client side logic to help populate this call and dictate how many stallions were to be minted as there wasn't a slider or input field to dictate it. That being said, this utilises a starting index value as well as the count. Now, the stallions minted with the same token ID's as the wallet's craniums - so if the web app worked these out first, then this code does make sense. If the web app didn't do anything before hand... then I am legitimately confused. A good developer would go and check the web app code, but I didn't name myself BadAtBlockchain because I'm good... you know? 

Anyway, that being said. If the pass checks and the contract is allowed to mint the target amount, it loops through, ensures the next token won't breach the maximum amount allowed before finally receiving the token ID itself (important, forces the stallion to match the cranium). On confirming this token does not exist, it is minted. 

Okay, so I have just realised how this works. I think. The starting index is in relation to the twc.tokenOfOwnerByIndex(), which is a function in the ERC721Enumerable contract. This allows a target addresses collection of owned tokens to be iterated through with an index. This function takes the iteration count of the for loop (i) and the starting index (should be 0 unless an edge case where someone has already minted the first one at a different time?). So then it basically says... get me the tokenID of the first index (0+0) of a addresses tokens, then get the second (1+0) until we run out. So for me personally I minted 6 stallions in one go, so it would have looped 6 times, starting from 0 and ending at 5 (5 in this case equals the 6th cranium token ID because n-1).

Okay, I am now renaming myself "NotAsBadAtBlockchain". Hope y'all learnt something or I helped reinforce something, maybe I even just helped confuse you more?