# ISablierV2Adminable
[Git Source](https://github.com/sablierhq/v2-core/blob/8b6a851f4185bd5af0e89a0f6a6eb2fed069cd10/docs/contracts/v2/reference/core/interfaces)

Contract module that provides a basic access control mechanism, with an admin that can be
granted exclusive access to specific functions. The inheriting contract must set the admin in the
constructor.


## Functions
### admin

The address of the admin account or contract.


```solidity
function admin() external view returns (address);
```

### transferAdmin

Transfers the admin of the contract to a new address.
Notes:
- Does not revert if the admin is the same.
- This function can potentially leave the contract without an admin, thereby removing any
functionality that is only available to the admin.
Requirements:
- The caller must be the current contract admin.


```solidity
function transferAdmin(address newAdmin) external;
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`newAdmin`|`address`|The address of the new admin.|


## Events
### TransferAdmin
Emitted when the admin is transferred.


```solidity
event TransferAdmin(address indexed oldAdmin, address indexed newAdmin);
```
