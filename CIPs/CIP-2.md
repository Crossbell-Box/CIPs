---
CIP: 2
title: Protocol Overview
status: Living
type: Standards Track CIP(Protocol)
created: 2022-7-26
---

All social activities can be described as different **interactions** among different **instances**. 

# Instance

Since Crossbell is EVM-compatible, two types of instances are inherited directly:

- Crypto address
- Asset (NFT/Cryptocurrency/...)

Though a crypto address is theoretically enough to recognize different identities. We always want a more well-rounded image in social activities, e.g, with a special avatar and more distinctive characters. Then we introduce another two instances in the Crossbell world:

- Character
- Note

A character is created and owned by some crypto address. The character owner is free to post any notes under one of her characters. For more details on `Character` and `Note`, please refer to the [Detailed Design](https://github.com/Crossbell-Box/Crossbell-Contracts/wiki/03-Detailed-Design) sections.

# Interaction

The most straightforward interaction that happens in social activities we can imagine might be “follow”. But there are numerous different types of interactions. For example, 

- character1 “super follows” character2
- character3 “dates” with character4
- note2 “comments” on note1
- character1 “shares” note1 (yes, interactions among different types of instances could also happen)
- character1 “blocks” address3
- ......

All the above examples are common interactions in social activities. We generalize such interaction as “**link**” on Crossbell. 

- The link could be emitted from either character or a note
- The link could point to any instance on Crossbell, or any arbitrary ones as long as with a URI.
- Each link has a type(e.g. `follow`, `comment`, ... ), suggesting the real meaning underneath.

|  | Ethereum address | Character | Note | Asset on Crossbell(NFT/Cryptocurrency/...) | Any URI |
| --- | --- | --- | --- | --- | --- |
| Character | L | L | L | L | L |
| Note | L | L | L | L | L |

# Capitalization

Capitalization is always the twin of ownership. But it’s never easy to clearly draw a line between those supposed to be the native asset on Crossbell and those not. With multiple considerations, generally, we propose two types of information as native assets and one manual approach to capitalizing non-asset information.

**Character**

Each character is natively created to be an NFT.

**Linklist**

All the linking objects with the **same link type** and emitted from a **character**, natively aggregate as an NFT. Specifically, linklist can only be transferred along with the character NFT.

Capitalization principles should serve human intuitions. With this in mind, we exclude the linking objects emitted from a **note** as native NFTs, which don’t aggregate as same meaningful and valuable information as those from a character. 

~~It’s worth noticing that our capitalization principle here on `link` is not so radical. That’s because we still hold the romantic belief that some core values on social interactions should not be capitalized and `link` itself should just describe these interactions as it is.~~ 

**Mint**

Besides the two native assets on Crossbell, there’s another approach called `Mint` to convert the non-NFT information to an NFT. 

As we mentioned before, each character as a whole is natively an NFT, but the note posted under the character is not. To provide a manual approach to capitalize those valuable or memorable single notes, each crypto address could mint a note to receive an NFT. 

One analogy can be that note itself is like the manual draft by the author. And when someone else mints it, the content is published as a book. The more users mint the notes, the more books are printed and sold.

# Modularity

As we discussed above, `instance`, `link`, and `mint` are created to help sketch the basics of social activities. For further flexibility and composability, there are two types of modules that can be configured on the action object.

- Link Module
- Mint Module

Each module is a standalone contract that adheres to a specific interface.<!-- with more details in the [Specification](https://www.notion.so/Specification-5521456b59864faf9a808f0a9ec0418e) section.  --> The undetermined states within the contract hold unlimited potential. Once an instance is linked, the corresponding link module will be triggered. Once a note is minted, the corresponding mint module will be triggered. Take some examples, 

- Each character could set a link module for itself: the first 1000 followers could get a special NFT.
- Each character owner could set a mint module for any of her notes: the minter should pay a specific amount of tokens.

More implementation details and examples can be found in Creating a Module section. But here we just want to highlight one important difference between the link module and the mint module:

- Link Module can not revert the link interaction.
- Mint Module can revert the mint interaction.

`Link` is essentially to deliver some information within the context of social activities. How that information is finally disseminated could be the compromised result of complicated interactions. But the information self could whatever be permissionless published. So Link Module can not revert the link interaction.

Whereas mint is to generate a new asset, involving property rights or copyright, or whatever more complicated things. No means no in this case.

# Deployed Blockchain and Contract Address

Currently, the protocol has been deployed on Crossbell network. The explorer and faucet could be found here:

- [Faucet](https://faucet.crossbell.io)
- [Explorer](https://scan.crossbell.io)

The contract has been deployed on:

| Web3Entry Proxy(Use this one for interactions only!) | 0xa6f969045641Cf486a747A2688F3a5A6d43cd0D8 |
| --- | --- |
| Web3Entry Implementation | 0x4dd1ed784b0C2bdF58a16e6311ad8E3f533401e8 |
| Linklist Token Proxy | 0xFc8C75bD5c26F50798758f387B698f207a016b6A |
| Linklist Token Implementation | 0x7A74557aC8b06D5f13d15BF7444D0c524eB820D0 |
| InteractionLogic | 0x315f6A340441878A09692d0D59CeE826ff57CDBb |
| LinkModuleLogic | 0xFC63B6Af4b5436d7Cf0e77782dFf8756f42F71AD |
| CharacterLogic | 0xf9eC2A9338C81DF2a771e0BBC98C8F43787CA805 |
| PostLogic | 0xEA971E76e375438ae6E48EBF90D8c6635475a613 |
| LinkLogic | 0x9BAdE758A6F1A72fecc9922516fa75C8A624D990 |
| MintNFT | 0x7bB160FAbC629a8B288499cE9B48eEabD46CF3e1 |
| Periphery | 0x96e96b7AF62D628cE7eb2016D2c1D2786614eA73 |
| Periphery Implementation | 0x59702aB0abcD5aE5F5e25AF664B93dab59401C32 |
| Resolver | 0xa5fa5302Be191fA9f8e7C35Cf5758D8bfDF4C90f |