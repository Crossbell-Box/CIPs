---
cip: 6
title: Decouple Operate and Transfer Authorization
status: Living
type: Standards Track
category: Protocol
created: 2022-11-15
---

A general design to decouple the authorization to operate and transfer onto the character.

## Abstract

This CIP designed a contract to keep characters on behalf of the character's owner, which is only authorized to transfer in or out the character. Whereas another operator is authorized for other social activities(e.g. post a note).

## Motivation

If new comers without setting up a wallet wants to explore on Crossbell, an obvious approach is to setup an admin on a centralized server holding the character and making the transactions as the user's will. Controversial point here is that this admin natively has the full power on all characters he/she holds.

So here we propose a solution to decouple the permission to "operate"(post/comment/like...) and "transfer", so that to alleviate one single centralized party's power: A specific contract with the contract admin is designed to only hold the power to receive and transfer out the character. Whereas another admin(either EA or EOA) is only permitted as the "operator", so that this admin's permission is bound to the operator's permission.

## Specification

1. Contract is supposed to implement the [interface](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v4.8.0/contracts/token/ERC721/IERC721Receiver.sol) `IERC721Receiver` to setup the operator once a character is transferred in:
  ```
      function onERC721Received(
          address operator,
          address,
          uint256,
          bytes calldata data
      ) external override returns (bytes4) {
          ......
          address selectedOperator = abi.decode(data, (address));
          IWeb3Entry(web3Entry).addOperator(tokenId, selectedOperator);
          ......
          return IERC721Receiver.onERC721Received.selector;
      }
  ```

2. Besides, a `withdraw` method is used to transfer the character out:
  ```
  interface NewbieVilla {
  	function withdraw(
          address to,
          uint256 characterId,
          uint256 nonce,
          uint256 expires,
          bytes memory proof
      ) external;   
  }
  ```

The proof is the signature signed by the contract admin. The message to sign is the packed data of this ``contract's address``, ``characterId``, ``nonce`` and ``expires``.

Here's an example in JavaScript to generate a proof:
  ```
    digest = ethers.utils.arrayify(
      ethers.utils.solidityKeccak256(
          ["address", "uint256", "uint256", "uint256"],
          [contractAddress, characterId, nonce, expires]
      )
  );
  proof = await owner.signMessage(digest);
  ``` 

## Rationale

Let's assume there's a newbie villa service playing the admin role of the contract. And this service uses some authentication verification approaches to help transfer the character out to the specific "user".  

Typically, the case could be that users using email login to the service, and the admin creates characters for the email users and keep the mappings of the emails and the character Ids.

Then from the perspective of an application, let's assume the goal is to post notes on behalf of the user. The workflow of the app is:
  1. Firstly, the app needs to prepare an operator address.  
  2. Secondly, the app redirects the user to the newbie villa service.
  3. On that service, user tells the service the operator address he/she wants to use(the previous operator address prepared by the app).
  4. Newbie villa service could create a new character with that operator address.  
  5. When the user wants to claim his character, he could email logins the service and ask the admin to generate a proof.
  6. With that proof, the user could interact with the contract to withdraw the character to any receiver selected.

By such a practice, the operator could not transfer the character and the contract could not execute transaction on behalf of the character. Also the user could verify all permission has been setup before actually devoting to that app.

## Reference Implementation

A reference implementation is here: https://scan.crossbell.io/address/0xcE9950c48d0E955833d1297a35F5579Cf4E1a6c2/contracts  

## Security Considerations

Generally the proof should be transferred in a secure communication channel between the user and the server. 

The ``nonce`` should be unique for each generated signature.

The admin could generate a signature with a large ``expires``, which means user could use that proof to claim the character any time he/she wants in the future. In such case, user has the responsibility to not leak that proof.

## Copyright

Copyright and related rights waived via [CC0](../LICENSE.md).
