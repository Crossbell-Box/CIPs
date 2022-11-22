---
CIP: 4
title: Contract Specification
status: Living
type: Standards Track CIP(Protocol)
created: 2022-7-26
---

# Web3Entry.sol

This is the entry point contract to all social interactions on Crossbell. It is an upgradeable contract via the standard OpenZeppelin [TransparentUpgradeablilityProxy](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/proxy/transparent/TransparentUpgradeableProxy.sol), which means all methods defined in Web3Entry.sol should be called via the proxy. This document elaborates on how the interfaces are designed to be called by the third parties.

## State Changing Methods

### createCharacter()

```solidity
function createCharacter(DataTypes.CreateCharacterData calldata vars) external;
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| to | address | The address to mint the character to |
| handle | string | The handle to set for the new character |
| uri | string | The URI to set for the new character’s metadata |
| linkModule | address | The link module to set for the new character or the zero address |
| linkModuleInitData | bytes | Arbitrary data to be decoded in the link module for initialization |

This method creates a character.

Specifically, the `uri` passed to the method 

It is expected:

1. The handle is not already taken.
2. The handle matches the regex  `/^[a-z0-9_\\-]{3,31}$/`

### setHandle()

```solidity
function setHandle(uint256 characterId, string calldata newHandle) external;
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The character id to set new handle for |
| newHandle | string | New handle to set |

This function sets new handle for a given character.

### setSocialToken()

```jsx
function setSocialToken(uint256 characterId, address tokenAddress) external;
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The characterId to set social token for |
| tokenAddress | address | Token address to be set |

This function sets a social token for a given character.

### setCharacterUri()

```jsx
function setCharacterUri(uint256 characterId, string calldata newUri) external;
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The characterId to to be set |
| newUri | string | New URI to be set |

This function sets a new URI for a given character.

### setPrimaryCharacterId()

```jsx
function setPrimaryCharacterId(uint256 characterId) external
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The character id to to be set |

This function sets a given character as primary.

### attachLinklist()

```jsx
function attachLinklist(uint256 linklistId, uint256 characterId) external
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| linklistId | uint256 | The linklist id to attach |
| characterId | uint256 | The character id to attach for |

This function attaches a linklist to a given character.

Per type only one linklist addressed can be attached.

### detachLinklist()

```jsx
function detachLinklist(uint256 linklistId, uint256 characterId) external
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| linklistId | uint256 | The linklist id to detach |
| characterId | uint256 | The character id to detach from |

This function detaches a linklist from a given character.

### setLinklistUri()

```jsx
function setLinklistUri(uint256 linklistId, string calldata uri) external
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| linklistId | uint256 | The linklist id to set for |
| uri | string | The metadata uri to set |

This function sets a new metadataURI for a given link list.

### linkAddress()

```jsx
function linkAddress(DataTypes.linkAddressData calldata vars) external
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The character id to init the link |
| ethAddress | address | The address to link |
| linkType | bytes32 | The link type |
| data | bytes | The data to pass to the link module for ethAddress, if any |

This function create a link to a given address.

### unlinkAddress()

```jsx
function unlinkAddress(DataTypes.unlinkAddressData calldata vars) external
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The character id to init the unlink |
| ethAddress | address | The address to unlink |
| linkType | bytes32 | The link type |

This function unlinks a given address.

### linkCharacter()

```jsx
function linkCharacter(DataTypes.linkCharacterData calldata vars) external;
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The characterId to sponsor a link action |
| toCharacterId | uint256 | The characterId to be linked |
| linkType | bytes32 | linkType, like “follow” |
| data | bytes | The data to pass to the link module, if any |

This function can link any characterId more than primary characterId..

### unlinkCharacter()

```jsx
function unlinkCharacter(DataTypes.unlinkCharacterData calldata vars) external;
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The characterId to sponsor a unlink action |
| toCharacterId | uint256 | The characterId to be unlinked |
| linkType | bytes32 | linkType, like “follow” |

This function unlinks a given character.

### createThenLinkCharacter()

```jsx
function createThenLinkCharacter(DataTypes.createThenLinkCharacterData calldata vars) external;
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The characterId to sponsor a link action |
| to | address | The address to be linked |
| linkType | bytes32 | linkType, like “follow” |
| data | bytes | The data to pass to the link module, if any |

createThenLinkCharacter first creates a character for “to”, and then link it..

### linkNote()

```jsx
function linkNote(DataTypes.linkNoteData calldata vars) external
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The characterId to sponsor a link action |
| toCharacterId | uint256 | The characterId of note |
| toNoteId | uint256 | The id of note |
| linkType | bytes32 | linkType, like “follow” |
| data | bytes | The data to pass to the link module, if any |

This function links a given note.

### unlinkNote()

```jsx
function unlinkNote(DataTypes.unlinkNoteData calldata vars) external
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The character Id to sponsor an unlink action  |
| toCharacterId | uint256 | The characterId of note to unlink |
| toNoteId | uint256 | The id of note to unlink |
| linkType | bytes32 | linkType, like “follow” |

This function unlinks a given note.

### linkERC721()

```jsx
function linkERC721(DataTypes.linkERC721Data calldata vars) external
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The character Id to sponsor an link action  |
| tokenAddress | addres | The token address of ERC721 to link |
| tokenId | uint256 | The token id of ERC721 to link |
| linkType | bytes32 | linkType, like “follow” |
| data | bytes | The data to pass to the link module, if any |

This function links an ERC721 token.

### unlinkERC721()

```jsx
function unlinkERC721(DataTypes.unlinkERC721Data calldata vars) external
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The character Id to sponsor an unlink action  |
| tokenAddress | addres | The token address of ERC721 to unlink |
| tokenId | uint256 | The token id of ERC721 to unlink |
| linkType | bytes32 | linkType, like “follow” |

This function unlinks an ERC721 token.

### linkAnyUri()

```jsx
function linkAnyUri(DataTypes.linkAnyUriData calldata vars) external
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The character Id to sponsor an link action  |
| toUri | string | The uri to link |
| linkType | bytes32 | linkType, like “follow” |
| data | bytes | The data to pass to the link module, if any |

This function links any uri.

### unlinkAnyUri()

```jsx
function unlinkAnyUri(DataTypes.unlinkAnyUriData calldata vars) external
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The character Id to sponsor an unlink action  |
| toUri | string | The uri to unlink |
| linkType | bytes32 | linkType, like “follow” |

Thin function unlinks any uri.

### linkCharacterLink()

```jsx
function linkCharacterLink(
        uint256 fromCharacterId,
        DataTypes.CharacterLinkStruct calldata linkData,
        bytes32 linkType
    ) external
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The character Id to sponsor an link action  |
| linkData | DataTypes.CharacterLinkStruct | The linkData to link |
| linkType | bytes32 | linkType, like “follow” |

DataTypes.CharacterLinkStruct

| Struct Member Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The from character id of characterLink |
| toCharacterId | uint256 | The to character id of characterLink |
| linkType | bytes32 | The linkType of characterLink |

This function links a characterLink.

### unlinkCharacterLink()

```jsx
function unlinkCharacterLink(
        uint256 fromCharacterId,
        DataTypes.CharacterLinkStruct calldata linkData,
        bytes32 linkType
    ) external
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The character Id to sponsor an unlink action  |
| linkData | DataTypes.CharacterLinkStruct | The linkData to unlink |
| linkType | bytes32 | linkType, like “follow” |

DataTypes.CharacterLinkStruct

| Struct Member Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The from character id of characterLink |
| toCharacterId | uint256 | The to character id of characterLink |
| linkType | bytes32 | The linkType of characterLink |

This function unlinks a characterLink.

### linkLinklist()

```jsx

function linkLinklist(DataTypes.linkLinklistData calldata vars) external
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The character id to sponsor an link action  |
| toLinkListId | uint256 | The linklist if to link |
| linkType | bytes32 | linkType, like “follow” |
| data | bytes | The data to pass to the link module, if any |

This function links a linklist.

### unlinkLinklist()

```jsx
function unlinkLinklist(DataTypes.unlinkLinklistData calldata vars) external
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The character id to sponsor an unlink action  |
| toLinkListId | uint256 | The linklist if to unlink |
| linkType | bytes32 | linkType, like “follow” |

This function unlinks a linklist.

### setLinkModule4Character()

```jsx
function setLinkModule4Character(DataTypes.setLinkModule4CharacterData calldata vars) external
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The character id to set link module for |
| linkModule | bytes32 | The link module to set |
| linkModuleInitData | bytes | The data to pass to the link module for initialization, if any |

This function sets link module for a given character.

### setLinkModule4Note()

```jsx
function setLinkModule4Note(DataTypes.setLinkModule4NoteData calldata vars) external
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The character id to set link module for |
| noteId | uint256 | The note id to set link module for |
| linkModule | bytes32 | The link module to set |
| linkModuleInitData | bytes | The data to pass to the link module for initialization, if any |

This function sets link module for a given note.

### setLinkModule4Linklist()

```jsx
function setLinkModule4Linklist(DataTypes.setLinkModule4LinklistData calldata vars) external
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| linklistId | uint256 | The linklist id to set link module for |
| linkModule | bytes32 | The link module to set |
| linkModuleInitData | bytes | The data to pass to the link module for initialization, if any |

This function sets link module for a given linklist.

### setLinkModule4ERC721()

```jsx
function setLinkModule4ERC721(DataTypes.setLinkModule4ERC721Data calldata vars) external
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| tokenAddress | address | The token address of erc721 to set link module for |
| tokenId | uint256 | The token id of erc721 to set link module for |
| linkModule | bytes32 | The link module to set |
| linkModuleInitData | bytes | The data to pass to the link module for initialization, if any |

This function sets link module for a given ERC721 token.

### setLinkModule4Address()

```jsx
function setLinkModule4Address(DataTypes.setLinkModule4AddressData calldata vars) external
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| account | address | The  address to set link module for |
| linkModule | bytes32 | The link module to set |
| linkModuleInitData | bytes | The data to pass to the link module for initialization, if any |

This function sets link module for a given address.

### mintNote()

```jsx
function mintNote(DataTypes.MintNoteData calldata vars) external returns (uint256)
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The characterId of note  to mint |
| noteId | uint256 | The note id to mint  |
| to | address | The receiver of mint nft |
| mintModuleData | bytes | The data to pass to the mint module, if any |

### setMintModule4Note()

```jsx
function setMintModule4Note(DataTypes.setMintModule4NoteData calldata vars) external
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The character id of note to set the mint module for |
| noteId | uint256 | The note id of note  |
| mintModule | address | The mint module to set for note |
| mintModuleInitData | bytes | The data to pass to the mint module |

This method sets the mint module for a given note.

### postNote()

```solidity
function postNote(DataTypes.PostNoteData calldata vars) external returns (uint256);
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The characterId to post the new note |
| contentUri | string | The URI to set for the new note |
| linkModule | address | The link module to set for the new note or the zero address |
| linkModuleInitData | bytes | Arbitrary data to be decoded in the link module for initialization |
| mintModule | address | The link module to set for the new note or the zero address |
| mintModuleInitData | bytes | Arbitrary data to be decoded in the mint module for initialization |
| locked | bool | note status to set |

This function posts a new note.

### deleteNote()

```jsx
function deleteNote(uint256 characterId, uint256 noteId) external;
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The characterId of note |
| noteId | uint256 | Id of the note to be deleted |

This function deletes a given note.

### postNote4Character()

```jsx
function postNote4Character(DataTypes.PostNoteData calldata postNoteData, uint256 toCharacterId)
        external
        returns (uint256)
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The characterId to post the new note |
| contentUri | string | The URI to set for the new note |
| linkModule | address | The link module to set for the new note or the zero address |
| linkModuleInitData | bytes | Arbitrary data to be decoded in the link module for initialization |
| mintModule | address | The link module to set for the new note or the zero address |
| mintModuleInitData | bytes | Arbitrary data to be decoded in the mint module for initialization |
| locked | bool | Note status to set |
| toCharacterId | uint256 | The character id to post note on |

This function posts a note on a given character.

### postNote4Address()

```jsx
function postNote4Address(DataTypes.PostNoteData calldata noteData, address ethAddress)
        external
        returns (uint256)
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The characterId to post the new note |
| contentUri | string | The URI to set for the new note |
| linkModule | address | The link module to set for the new note or the zero address |
| linkModuleInitData | bytes | Arbitrary data to be decoded in the link module for initialization |
| mintModule | address | The link module to set for the new note or the zero address |
| mintModuleInitData | bytes | Arbitrary data to be decoded in the mint module for initialization |
| locked | bool | note status to set |
| ethAddress | address | The address to post note on |

This function posts a note on a given address.

### postNote4Linklist()

```jsx
function postNote4Linklist(DataTypes.PostNoteData calldata noteData, uint256 toLinklistId)
        external
        returns (uint256)
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The characterId to post the new note |
| contentUri | string | The URI to set for the new note |
| linkModule | address | The link module to set for the new note or the zero address |
| linkModuleInitData | bytes | Arbitrary data to be decoded in the link module for initialization |
| mintModule | address | The link module to set for the new note or the zero address |
| mintModuleInitData | bytes | Arbitrary data to be decoded in the mint module for initialization |
| locked | bool | Note status to set |
| toLinklistId | uint256 | The linklist id to post note on |

This function posts a note on a given linklist.

### postNote4Note()

```jsx
function postNote4Note(
        DataTypes.PostNoteData calldata postNoteData,
        DataTypes.NoteStruct calldata note
    ) external returns (uint256)
```

| Struct Member Name of PostNoteData | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The characterId to post the new note |
| contentUri | string | The URI to set for the new note |
| linkModule | address | The link module to set for the new note or the zero address |
| linkModuleInitData | bytes | Arbitrary data to be decoded in the link module for initialization |
| mintModule | address | The link module to set for the new note or the zero address |
| mintModuleInitData | bytes | Arbitrary data to be decoded in the mint module for initialization |
| locked | bool | Note status to set |

| Struct Member Name of NoteSruct | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The character id of note to post note on |
| noteId | uint256 | The note id of note to post note on |

This function posts a note on a given note.

### postNote4ERC721()

```jsx
function postNote4ERC721(
        DataTypes.PostNoteData calldata postNoteData,
        DataTypes.ERC721Struct calldata erc721
    ) external returns (uint256)
```

| Struct Member Name of PostNoteData | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The characterId to post the new note |
| contentUri | string | The URI to set for the new note |
| linkModule | address | The link module to set for the new note or the zero address |
| linkModuleInitData | bytes | Arbitrary data to be decoded in the link module for initialization |
| mintModule | address | The link module to set for the new note or the zero address |
| mintModuleInitData | bytes | Arbitrary data to be decoded in the mint module for initialization |
| locked | bool | Note status to set |

| Struct Member Name of ERC721Struct | Type | Description |
| --- | --- | --- |
| tokenAddress | address | The token address of erc721 to post note on |
| erc721TokenId | uint256 | The token id of erc721 to post note on |

This function posts a note on a given ERC721.

### postNote4AnyUri()

```jsx
function postNote4AnyUri(DataTypes.PostNoteData calldata postNoteData, string calldata uri)
        external
        returns (uint256)
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The characterId to post the new note |
| contentUri | string | The URI to set for the new note |
| linkModule | address | The link module to set for the new note or the zero address |
| linkModuleInitData | bytes | Arbitrary data to be decoded in the link module for initialization |
| mintModule | address | The link module to set for the new note or the zero address |
| mintModuleInitData | bytes | Arbitrary data to be decoded in the mint module for initialization |
| locked | bool | Note status to set |
| uri | string | The uri to post note on |

This function posts a note on any uri.

### setOperator()

```jsx
function setOperator(uint256 characterId, address operator) external 
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The characterId to post the new note |
| operator | address | The operator address you want to authorize |

This function sets an address as a character's operator.
| WARNING: `setOperator` has been deprecated, use `addOperator` and `removeOperator` instead! |
| --- |

### addOperator()

```jsx
function addOperator(uint256 characterId, address operator) external
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The characterId to post the new note |
| operator | address | The operator address you want to authorize |

This function add an address to a character's operator list.

### removeOperator()

```jsx
function removeOperator(uint256 characterId, address operator) external
```

| Struct Member Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The characterId to post the new note |
| operator | address | The operator address you want to authorize |

This function remove an address from a character's operator list.

## View Functions

### getPrimaryCharacterId()

```jsx
function getPrimaryCharacterId(address account) external view returns (uint256)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| account | address | The account to query the primary character id for |

Returns

| Type | Description |
| --- | --- |
| uint256 | The primary character id |

This function returns the primary character id for the given account.

### isPrimaryCharacter()

```jsx
function isPrimaryCharacter(uint256 characterId) external view returns (bool)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The character id to query for |

Returns

| Type | Description |
| --- | --- |
| bool | Whether is character id is primary |

This function returns whether a character id is primary.

### getCharacter()

```jsx
function getCharacter(uint256 characterId) external view returns (DataTypes.Character memory)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The character id to query for |

Returns

| Struct Member Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The character id to query for |
| handle | string | The handle of this character |
| uri | string | The uri of this character |
| noteCount | uint256 | The note count of this character |
| socialToken | address | The social token address of this character |
| linkModule | address | The link module of this character |

This function returns the character struct for a given character.

### getCharacterByHandle()

```jsx
function getCharacterByHandle(string calldata handle)
        external
        view
        returns (DataTypes.Character memory)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| handle | string | The character handle to query for |

Returns

The return result is the same as getCharacter.

This function returns the character struct for a given character by character handle.

### getHandle()

```jsx
function getHandle(uint256 characterId) external view returns (string memory)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The character id to query handle for |

Returns

| Type | Description |
| --- | --- |
| string | The handle of the given character |

### getCharacterUri()

```jsx
function getCharacterUri(uint256 characterId) external view returns (string memory)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The character id to query uri for |

Returns

| Type | Description |
| --- | --- |
| string | The character uri of the given character |

This function returns the character URI for the given character.

### getOperator()

```jsx
function getOperator(uint256 characterId) external view returns (address)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The character id to query operator for |

Returns

| Type | Description |
| --- | --- |
| address | The character operator of the given character |

This function returns the operator for the given character. 

It returns zero address if the character does not exist or it was set as zero address.

### getLinklistUri()

```jsx
function getLinklistUri(uint256 tokenId) external view returns (string memory);
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| tokenId | uint256 | The token id of linklist to query uri for |

Returns

| Type | Description |
| --- | --- |
| string | The token uri of the given linklist |

This function returns the URI for the given linklist.

### getLinklistId()

```jsx
function getLinklistId(uint256 characterId, bytes32 linkType) 
external view returns (uint256)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The character id to query linklist id for |
| linkType | bytes32 | The linkType associated with the linklist |

Returns

| Type | Description |
| --- | --- |
| uint256 | The associated linklist token id |

This function returns the linklist token id for the given character and given linkType.

### getLinklistType()

```jsx
function getLinklistType(uint256 linkListId) external view returns (bytes32);
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| linklistId | uint256 | The linklist id to query linkType for |

Returns

| Type | Description |
| --- | --- |
| bytes32 | The associated linkType for the given linklist token id |

This function returns the associated linkType for the given linklist token id.

### getNote()

```jsx
function getNote(uint256 characterId, uint256 noteId)
        external
        view
        returns (DataTypes.Note memory)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The characterId of note |
| noteId | uint256 | Id of the note |

**Returns**

| Struct Member Name | Type | Description |
| --- | --- | --- |
| linkItemType | bytes32 | The linkItemType of note with link |
| linkKey | bytes32 | If linkKey is not empty, then it is a note with link |
| contentUri | string | The content URI of this note |
| linkModule | address | The link module of this note |
| mintModule | address | The mint module of this note |
| mintNFT | address | The mint nft contract address of this note |
| deleted | bool | Whether the note is deleted |
| locked | bool | Whether the note is locked |

This function returns the detailed note by given characterId and noteId.

There are 6 linkItemTypes:

```jsx
		// "Character"
    bytes32 constant NoteLinkTypeCharacter =
        0x50726f66696c6500000000000000000000000000000000000000000000000000;
    // "Address"
    bytes32 constant NoteLinkTypeAddress =
        0x4164647265737300000000000000000000000000000000000000000000000000;
    // "Linklist"
    bytes32 constant NoteLinkTypeLinklist =
        0x4c696e6b6c697374000000000000000000000000000000000000000000000000;
    // "Note"
    bytes32 constant NoteLinkTypeNote =
        0x4e6f746500000000000000000000000000000000000000000000000000000000;
    // "ERC721"
    bytes32 constant NoteLinkTypeERC721 =
        0x4552433732310000000000000000000000000000000000000000000000000000;
    // "AnyUri"
    bytes32 constant NoteLinkTypeAnyUri =
        0x416e795572690000000000000000000000000000000000000000000000000000;
```

### getLinkModule4Address()

```jsx
function getLinkModule4Address(address account) external view returns (address)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| account | address | The  address to query link module for |

Returns

| Type | Description |
| --- | --- |
| address | The link module for the given address |

This function returns the link module for the given address.

### getLinkModule4Linklist()

```jsx
function getLinkModule4Linklist(uint256 tokenId) external view returns (address)
```

Returns

| Type | Description |
| --- | --- |
| address | The link module for the given linklist |

This function returns the link module for the given linklist.

### getLinkModule4ERC721()

```jsx
function getLinkModule4ERC721(address tokenAddress, uint256 tokenId)
        external
        view
        returns (address)
```

Returns

| Type | Description |
| --- | --- |
| address | The link module for the given ERC721 |

This function returns the link module for the given ERC721 token.

### getLinklistContract()

```jsx
function getLinklistContract() external view returns (address)
```

This function returns the linklist contract address.

### isOperator()

```jsx
function isOperator(uint256 characterId, address operator)
    external
    view
    override
    returns (bool)
```

Returns

| Type | Description |
| --- | --- |
| bool | Whether an address is an operator of the character |

| NOTICE: `isOperator()` is compatible with `setOperator()`, which means checking addresses set by `setOperator` will also returns true. |
| --- |

### getOperators()

```jsx
function getOperators(uint256 characterId) 
    external 
    view 
    override 
    returns (address[] memory) 
```

Returns

| Type | Description |
| --- | --- |
| address[] | Operator list of the character |

| NOTICE: `getOperators()` is NOT compatible with `setOperator()`, which means `getOperators()` doesn't return addresses set by `setOperator()`. |
| --- |

# Periphery.sol

 Periphery contracts interact with the web3Entry core contracts, but are not part of the core.

| Periphery | 0x96e96b7AF62D628cE7eb2016D2c1D2786614eA73 |
| --- | --- |

## State Changing Methods

### linkCharactersInBatch()

```jsx
function linkCharactersInBatch(DataTypes.linkCharactersInBatchData calldata vars) external;
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The characterId to sponsor a link |
| toCharacterIds | uint256[] | The charactersIds to be linked |
| data | bytes[] | The data to pass to the link module |
| toAdresses | address[] | The addresses to be linked |
| linkType | bytes32 | linkType, like “follow” |

This function links characters in bulk, if the linked address has no character, create first.

### createCharacterThenPostNote()

```jsx
function createCharacterThenPostNote(DataTypes.createCharacterThenPostNoteData calldata vars)
        external;
```

| Struct Name | Type | Description |
| --- | --- | --- |
| handle | string | The character handle to set |
| uri | string | The character metadata uri to set |
| characterLinkModule | address | The link module of character to set |
| characterLinkModuleInitData | bytes | The init data for character link module |
| contentUri | string | The metadata uri of note to set |
| noteLinkModule | address | The link module of note |
| noteLinkModuleInitData | bytes | The init data for note link module |
| mintModule | address | The mint module of note |
| mintModuleInitData | bytes | The init data for note mint module |
| locked | bool | Whether the note is locked |

This function first creates a character and then posts a note.

## View Functions

### getNotesByCharacterId()

```jsx
function getNotesByCharacterId(
        uint256 characterId,
        uint256 offset,
        uint256 limit
    ) external view returns (DataTypes.Note[] memory results)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| characterId | uint256 | The character id of the character to query for |
| offset | uint256 | offset |
| limit | uint256 | limit |

**Returns**

| Struct Member Name | Type | Description |
| --- | --- | --- |
| linkItemType | bytes32 | linkItemType of note with link |
| linkKey | bytes32 | If linkKey is not empty, then it is a note with link |
| contentUri | string | The contentUri of note |
| linkModule | address | The link module of note |
| mintModule | address | The mint module of note |
| mintNFT | address | The mint nft contract address |
| deleted | bool | Whether the note is deleted |
| locked | bool | Whether the note is locked |

This function returns the notes for the given characterId.

### getLinkingCharacterIds()

```jsx
function getLinkingCharacterIds(uint256 fromCharacterId, bytes32 linkType)
        external
        view
        returns (uint256[] memory results)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The character Id to sponsor an link action  |
| linkType | bytes32 | linkType, like “follow” |

Returns

| Type | Description |
| --- | --- |
| uint256[] | The linking character ids |

This function returns the linking character ids for the given character id and linkType.

### getLinkingCharacterId()

```jsx
function getLinkingCharacterId(bytes32 linkKey) external view returns (uint256 characterId)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| linkKey | bytes32 | linkKey of note struct |

This function returns a linking character id by given linkKey.

### getLinkingNotes()

```jsx
function getLinkingNotes(uint256 fromCharacterId, bytes32 linkType)
        external
        view
        returns (DataTypes.Note[] memory results)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The character Id to sponsor an link action  |
| linkType | bytes32 | linkType, like “follow” |

**Returns**

| Struct Member Name | Type | Description |
| --- | --- | --- |
| linkItemType | bytes32 | The linkItemType of note with link |
| linkKey | bytes32 | If linkKey is not empty, then it is a note with link |
| contentUri | string | The content URI of this note |
| linkModule | address | The link module of this note |
| mintModule | address | The mint module of this note |
| mintNFT | address | The mint nft contract address of this note |
| deleted | bool | Whether the note is deleted |
| locked | bool | Whether the note is locked |

This function returns the linking notes for the given character id and linkType.

### getLinkingNote()

```jsx
function getLinkingNote(bytes32 linkKey) external view returns (DataTypes.NoteStruct memory)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| linkKey | bytes32 | linkKey of note struct |

This function returns a note by given linkKey.

### getLinkingERC721s()

```jsx
function getLinkingERC721s(uint256 fromCharacterId, bytes32 linkType)
        external
        view
        returns (DataTypes.ERC721Struct[] memory results)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The character Id to sponsor an link action  |
| linkType | bytes32 | linkType, like “follow” |

**Returns**

| Struct Member Name | Type | Description |
| --- | --- | --- |
| tokenAddress | address | token address |
| erc721TokenId | uint256 | token id |

This function returns the linking ERC721s for the given character id and linkType.

### getLinkingERC721()

```jsx
function getLinkingERC721(bytes32 linkKey)
        external
        view
        returns (DataTypes.ERC721Struct memory)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| linkKey | bytes32 | linkKey of note struct |

This function returns a linking ERC721 by given linkKey.

### getLinkingAnyUris()

```jsx
function getLinkingAnyUris(uint256 fromCharacterId, bytes32 linkType)
        external
        view
        returns (string[] memory results)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The character Id to sponsor an link action  |
| linkType | bytes32 | linkType, like “follow” |

This function returns the linking Uris for the given character id and linkType.

### getLinkingAnyUri()

```jsx
function getLinkingAnyUri(bytes32 linkKey) external view returns (string memory)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| linkKey | bytes32 | linkKey of note struct |

This function returns a linking Uri by given linkKey.

### getLinkingAddresses()

```jsx
function getLinkingAddresses(uint256 fromCharacterId, bytes32 linkType)
        external
        view
        returns (address[] memory)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The character Id to sponsor an link action  |
| linkType | bytes32 | linkType, like “follow” |

This function returns the linking addresses for the given character id and linkType.

### getLinkingAddress()

```jsx
function getLinkingAddress(bytes32 linkKey) external view returns (address)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| linkKey | bytes32 | linkKey of note struct |

This function returns a linking address by given linkKey.

### getLinkingLinklistIds()

```jsx
function getLinkingLinklistIds(uint256 fromCharacterId, bytes32 linkType)
        external
        view
        returns (uint256[] memory linklistIds)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| fromCharacterId | uint256 | The character Id to sponsor an link action  |
| linkType | bytes32 | linkType, like “follow” |

This function returns the linking linklist ids for the given character id and linkType.

### getLinkingLinklistId()

```jsx
function getLinkingLinklistId(bytes32 linkKey) external view returns (uint256 linklistId)
```

| Parameter Name | Type | Description |
| --- | --- | --- |
| linkKey | bytes32 | linkKey of note struct |

This function returns a linking linklist by given linkKey.