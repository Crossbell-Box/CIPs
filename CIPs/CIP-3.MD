---
CIP: 3
title: Detailed Definition of Crossbell Protocol
status: Living
type: Standards Track CIP(Protocol)
created: 2022-7-26
---
# Instance

## Character

Like many existing social platforms, the user typically starts by creating a character, which will be the entity participating in all the following social activities. The difference here is that each character is a sequentially ID'd NFT owned by the creator. There’s no limitation on how many characters each address could create or hold. But holding one character NFT doesn’t mean using that character to socialize definitely. So to eliminate unnecessary confusion, each address could select one of his or her characters as the primary character. By default, the first created character is the user’s primary character.

When a character is created, the user could set a human-readable handle as the unique identifier for that character, and also an optional link module that will be triggered once that character is linked in the future. Besides, the nickname, bio, avatars, or some other useful information can be set within a URI that points to such character metadata. Crossbell doesn’t care about whether that URI uses ipfs protocol or https protocol, which is a free choice for applications or users. Just for better data interoperability, Crossbell proposes a suggested character metadata specification [here](https://github.com/Crossbell-Box/Crossbell-Contracts/wiki/05-URI-and-Metadata-Specification#character-metadata).

All of this initialized information could all be updated later at any time.

## Note

A note is one piece of content created and posted under the character. When a note is created, the user could set a content URI for that note, and also set the link module and mint module for the note. So when that note is linked or minted in the future, the corresponding module can be triggered. 

Similar to character metadata, the content of one note is set by a URI that points to the content metadata. That URI can be ipfs protocol or https protocol or whatever else, which could be freely chosen by applications or users. Crossbell just proposes a suggested note metadata specification [here](https://github.com/Crossbell-Box/Crossbell-Contracts/wiki/05-URI-and-Metadata-Specification#note-metadata).

Besides, when a note is posted, the user could decide whether the content URI, link module, and mint module can be modified using a `locked` flag. With the `locked` set as false, that content URI, link module, and mint module can be modified at any time. Otherwise, they cannot be modified anymore.

That flag is an indicator for others interested in minting this note. Typically a note with `locked` set as true and with content in non-editable storage, e.g. content URI in `ipfs` protocol, could be more persuasive and fair for those who want to mint.  

There’s the delete option for a note. But we all know the essentiality of a decentralized world is that nothing can be deleted completely, as long as there’s a node willing to hold that copy. So, the delete operation on Crossbell is just an indicative flag to provide more convenience for both content creators and content consumers. Deleted notes cannot be linked or minted anymore. But the content URI will not be emptied.

# Interaction

As we have mentioned, we use links to describe different interactions in social activities. Each link has a link type, which can be an arbitrary string defined by the user. Link is emitted from either a character or a note, and points to an object, which can be an Ethereum address, character, note, ERC721 asset on Crossbell, any URI, or a Linklist instance. 

Linklist is the group of all linking objects with the **same link type** and emitted from a **character,** which natively is an NFT**.** Each time a character makes a link with a new type, correspondingly a new linklist will be created and attached to that character by default.

# Operator

All interactions on Crossbell are essentially transactions requiring the user’s signature, which can be an experience barrier for traditional Web2 users. To provide more convenience, each wallet address could set an operator account on behalf of him to make posts, or follow anyone else. That permission can be granted or revoked at any time.

# Capitalization

As we have mentioned, character and linklist are natively NFTs, and a piece of note can be mint as an NFT.

All character NFTs are generated from the same contract. The token URI of a character NFT is the same as the URI of the character metadata. When a character NFT is transferred, it is just decoupled with the previous owner, the existing notes, existing followers of that character, and the link modules previously set have no reason to be affected. And the new owner of that character could continue to post notes under it or reset the link modules if he wants. So typically, exchanging characters is to sell the branding of that character along with its traffic.

Similarly, all linklist NFTs are generated from the same contract. The token URI of a linklist NFT can be set by that NFT owner. A linklist NFT is bound to the character it is attached. It can only be transferred along with its attaching character. The new owner of that NFT could continue to modify the objects in that list. 

The Mint note mechanism is that, if a note is never minted, a new contract will be created first before a new token is minted, otherwise a new token can be minted on that contract straightforwardly. The token URI of the note NFT is the content URI of the original note. 

As we have mentioned before, a note can be deleted. But that’s only a flag variable in the contract, which will not affect the content URI of the original note. So it means deleting a note is not a problem for the minted note NFT.

# Some Highlighted Takeaways

## Character and Note

- Each character is created by an Ethereum address, aka, the creator, and can be mint to any Ethereum address.
- Each note is only able to be posted under a character that the note creator owns.
- Each character contains multiple notes.
- Each Ethereum address can own multiple characters.
- Each Ethereum address can set a primary character for itself.

## Link

- All Crossbell instances(Ethereum address, character, note, general asset, single link item, link list) have an instance URI.
- Link can be emitted from either character or note. Link can point to any instance on Crossbell or any external URI. Each link has a link type.
- Character owner and note owner can set a link module for his character and each of his notes. The link module will be triggered during each link action.
- All linking objects with the **same link type** and emitted from a **character** make up a link list.
- All linking objects with the **same link type** and emitted from a **note** don’t make up a link list.
- Each Ethereum address can own multiple linklists.

## Capitalization

- Each character is natively an NFT. Each note is natively not.
- Each link list is natively an NFT. Every single link is not.
- Linklist NFT is bound to the character NFT.
- The character owner is the owner of that character NFT. It suggests that the character owner isn’t necessary to be the character creator since character NFT can be transferred.
- Note owner is the owner of the character to which belongs. It suggests that the note owner isn’t necessary to be the note creator since character NFT can be transferred.
- Mint can be acted on a note.
- Mint can be acted from any Ethereum address to some selected Ethereum address.
- Note owner can set a mint module, which will be triggered during each mint action.
- Error emitting from the link module will be omitted. Error emitting from the mint module will be reverted.