---
cip: 7
title: Fine-Grained Access Control Over Operators
status: Living
type: Standards Track
category: Protocol
created: 2022-12-5
---

A general design to get fine-grained access control over operators.

## Abstract

On Crossbell, operators are authorities that users trust and set for their characters to send transactions on behalf of themselves so as to avoiding the hassle of signing transactions every time. But it goes both ways, when you add an address as your operator, this operator can perform a range of social acts in your name, which means if operators become evil someday(which is a virtual impossibility, but we always make the worst assumptions in the dark forest), your social wealth and reputation will be at stake. Considering this problem, we should restrict operators’ access to the minimum extent in every different scenarios.

## Motivation
Defining operators’ permission for each of social actions in Web3Entry for different use cases in order to limit operators’ access to the minimum extent and avoid over delegation or abuse of authority.

## Specification

In the [release v2.2.0](https://github.com/Crossbell-Box/Crossbell-Contracts/blob/v2.2.0/contracts/Web3EntryBase.sol#L123), you can simply add or remove operators using these two methods below:

```solidity
function addOperator(uint256 characterId, address operator) external;

function removeOperator(uint256 characterId, address operator) external;
```

With CIP-7, users should specify operators’ competence using a bitmap. Every bit of the bitmap stands for a corresponding method in Web3Entry. If the bit on the position is set to 1 that means the operator have access, and if it’s set to 0, that means users want to reserve this action for their own. 

```solidity
function grantOperatorPermissions(
    uint256 characterId,
    address operator,
    uint256 permissionBitMap
) external;
```
With the `grantOperatorPermissions` method, users can freely enable or disable operators' permissions for each method. For example, users on xSync can grant operators the permission to `postNote`, and disable all irrelevant permissions.

Furthermore, blocklists and allowlists allow users to customize permissions for their individual notes. Addresses in allowlists are allowed to edit(`setNoteUri`) the note and addresses in blocklist are forbidden. Users can manage their note operator allowlists and blocklists by calling 2 methods below:

```solidity
// add addresses into blocklists and allowlists
function grantOperators4Note(
        uint256 characterId,
        uint256 noteId,
        address[] calldata blocklist,
        address[] calldata allowlist
    ) external;

```

To clarify the relationship between `OperatorPermissions` and `Operators4Note`, the level of `Operators4Note` is above operator permissions, which means when both `OperatorPermissions` and `Operators4Note` exist at the same time, `Operators4Note` prevails, and for `Operators4Note`, blocklists prevail, which means operators can't edit notes if they're in both blocklists and allowlists. If you want to learn more details, you can check our [test cases](https://github.com/Crossbell-Box/Crossbell-Contracts/blob/main/test/Operator.t.sol#L196) or [contract](https://github.com/Crossbell-Box/Crossbell-Contracts/blob/main/contracts/Web3Entry.sol#L106).

Imagine a scenario where you’re a KOL having millions of followers on Crossbell and you’ve made a rough outlined draft about your vision of ‘Crossbell is the future’ but you don’t really had the time to take care of things here and there, so you have to ask 2 of your employees as to polish your draft. With CIP-7, you can tackle with this easily: 
1. Post your draft as a note. 
   ```
   web3Entry.postNote(<your draft note data>);
   ```
2. Add your employee into the allowlist of your draft note by calling `grantOperators4Note`.
   ```
   web3Entry.grantOperators4Note(
            <your character ID>,
            <your draft note ID>,
            <allowlist addresses array>
            <blocklist addresses array>
        );
   ```

After these two calls, your collaborators will be granted with permissions to modify your note by calling [setNoteUri](https://github.com/Crossbell-Box/Crossbell-Contracts/blob/852a6a4a81407906adc0f4bce151691ab2f41810/docs/libraries/PostLogic.md#setnoteuri)

For xSync and xLog, we have preset some suggested permission bitmaps. When user interacts with xSync and xLog, these permissions will be used by default, but always remember that you can set them freely by calling Web3Entry directly.

### Operator Permission Bitmap Layout
| Permission ID | Method                        | Suggested Permission Type |
|---------------|-------------------------------|---------------------------|
| 0             | SET_HANDLE                    | Owner Reserve             |
| 1             | SET_SOCIAL_TOKEN              | Owner Reserve             |
| 2             | GRANT_OPERATOR_PERMISSIONS    | Owner Reserve             |
| 3             | GRANT_OPERATORS_FOR_NOTE      | Owner Reserve             |
| ···           |                               | Owner Reserve             |
|  [21, 175]    | reserved for future           |                           |
| 176           | SET_CHARACTER_URI             | Operator Sign             |
| 177           | SET_LINKLIST_URI              | Operator Sign             |
| 178           | LINK_CHARACTER                | Operator Sign             |
| 179           | UNLINK_CHARACTER              | Operator Sign             |
| 180           | CREATE_THEN_LINK_CHARACTER    | Operator Sign             |
| 181           | LINK_NOTE                     | Operator Sign             |
| 182           | UNLINK_NOTE                   | Operator Sign             |
| 183           | LINK_ERC721                   | Operator Sign             |
| 184           | UNLINK_ERC721                 | Operator Sign             |
| 185           | LINK_ADDRESS                  | Operator Sign             |
| 186           | UNLINK_ADDRESS                | Operator Sign             |
| 187           | LINK_ANYURI                  | Operator Sign             |
| 188           | UNLINK_ANYURI                | Operator Sign             |
| 189           | LINK_LINKLIST                | Operator Sign             |
| 190           | UNLINK_LINKLIST              | Operator Sign             |
| 191           | SET_LINK_MODULE_FOR_CHARACTER | Operator Sign             |
| 192           | SET_LINK_MODULE_FOR_NOTE      | Operator Sign             |
| 193           | SET_LINK_MODULE_FOR_LINKLIST | Operator Sign             |
| 194           | SET_MINT_MODULE_FOR_NOTE      | Operator Sign             |
| 195           | SET_NOTE_URI                  | Operator Sign             |
| 196           | LOCK_NOTE                     | Operator Sign             |
| 197           | DELETE_NOTE                   | Operator Sign             |
| 198           | POST_NOTE_FOR_CHARACTER       | Operator Sign             |
| 199           | POST_NOTE_FOR_ADDRESS         | Operator Sign             | 
| 200           | POST_NOTE_FOR_LINKLIST       | Operator Sign             |
| 201           | POST_NOTE_FOR_NOTE            | Operator Sign             |
| 202           | POST_NOTE_FOR_ERC721          | Operator Sign             |
| 203           | POST_NOTE_FOR_ANYURI         | Operator Sign             |
| ···           |                               | Operator Sign             |
| 236           | POST_NOTE                     | Operator Sync             |
| ···           |                               | Operator Sync             |



| :point_up:  Suggested Permission Type is for references only, users can customize freely   |
|-----------------------------------------|

## Backwards Compatibility

We have done some necessary compatibility work for this upgrade. All operators previously set using the `set operator` interface have been migrated and granted default Operator Sign permissions. For apps that uses old interfaces, we will keep the old interface for a while until all apps have successfully upgraded, giving apps more time to adjust.

migrate interface:

```solidity
    /**
     * @notice Migrates operators permissions to operatorsSignBitMap
     * @param characterIds List of characters to migrate.
     * @dev `addOperator`, `removeOperator`, `setOperator` will all be deprecated soon. We recommend to use
     *  `migrateOperator` to grant OPERATOR_SIGN_PERMISSION_BITMAP to all previous operators.
     */
    function migrateOperator(uint256[] calldata characterIds) external {
        // set default permissions bitmap
        for (uint256 i = 0; i < characterIds.length; ++i) {
            uint256 characterId = characterIds[i];
            address[] memory operators = _operatorsByCharacter[characterId].values();
            for (uint256 j = 0; j < operators.length; ++j) {
                _setOperatorPermissions(
                    characterId,
                    operators[j],
                    OP.OPERATOR_SYNC_PERMISSION_BITMAP
                );
            }
        }
    }
```