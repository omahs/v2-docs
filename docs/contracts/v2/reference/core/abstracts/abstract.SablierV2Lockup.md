# SablierV2Lockup
[Git Source](https://github.com/sablierhq/v2-core/blob/8b6a851f4185bd5af0e89a0f6a6eb2fed069cd10/docs/contracts/v2/reference/core/abstracts)

**Inherits:**
[SablierV2Config](/docs/contracts/v2/reference/core/abstracts/abstract.SablierV2Config.md), [ISablierV2Lockup](/docs/contracts/v2/reference/core/interfaces/abstract.SablierV2FlashLoan.md)

*Abstract contract that implements the {ISablierV2Lockup} interface and other common logic.*


## State Variables
### _nftDescriptor
*Contract that generates the non-fungible token URI.*


```solidity
ISablierV2NftDescriptor internal immutable _nftDescriptor;
```


### nextStreamId
Counter for stream ids.


```solidity
uint256 public override nextStreamId;
```


## Functions
### isActiveStream

*Checks that `streamId` points to an active stream.*


```solidity
modifier isActiveStream(uint256 streamId);
```

### isAuthorizedForStream

Checks that `msg.sender` is the sender of the stream, the recipient of the stream (also known as
the owner of the NFT), or an approved operator.


```solidity
modifier isAuthorizedForStream(uint256 streamId);
```

### onlySenderOrRecipient

Checks that `msg.sender` is either the sender of the stream or the recipient of the stream (also known
as the owner of the NFT).


```solidity
modifier onlySenderOrRecipient(uint256 streamId);
```

### constructor


```solidity
constructor(
    address initialAdmin,
    ISablierV2Comptroller initialComptroller,
    ISablierV2NftDescriptor nftDescriptor,
    UD60x18 maxFee
) SablierV2Config(initialAdmin, initialComptroller, maxFee);
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`initialAdmin`|`address`|The address of the initial contract admin.|
|`initialComptroller`|`ISablierV2Comptroller`|The address of the initial comptroller.|
|`nftDescriptor`|`ISablierV2NftDescriptor`|The address of the NFT descriptor contract.|
|`maxFee`|`UD60x18`|The maximum fee that can be charged by either the protocol or a broker, as an UD60x18 number where 100% = 1e18.|


### getRecipient

Queries the recipient of the stream.


```solidity
function getRecipient(uint256 streamId) public view virtual override returns (address recipient);
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`streamId`|`uint256`|The id of the stream to make the query for.|


### getStatus

Queries the status of the stream.


```solidity
function getStatus(uint256 streamId) public view virtual override returns (Lockup.Status status);
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`streamId`|`uint256`|The id of the stream to make the query for.|


### withdrawableAmountOf

Calculates the amount that the recipient can withdraw from the stream, in units of the asset's decimals.


```solidity
function withdrawableAmountOf(uint256 streamId) public view virtual override returns (uint128 withdrawableAmount);
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`streamId`|`uint256`|The id of the stream to make the query for.|


### isCancelable

Checks whether the stream is cancelable or not.
Notes:
- Always returns `false` if the stream is not active.


```solidity
function isCancelable(uint256 streamId) public view virtual override returns (bool result);
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`streamId`|`uint256`|The id of the stream to make the query for.|


### burn

Burns the NFT associated with the stream.

*Emits a {Transfer} event.
Notes:
- The purpose of this function is to make the integration of Sablier V2 easier. Third-party contracts don't
have to constantly check for the existence of the NFT. They can decide to burn the NFT themselves, or not.
Requirements:
- `streamId` must point to a stream that is either canceled or depleted.
- The NFT must exist.
- `msg.sender` must be either an approved operator or the owner of the NFT.*


```solidity
function burn(uint256 streamId) external override;
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`streamId`|`uint256`|The id of the stream NFT to burn.|


### cancel

Cancels the stream and transfers any remaining assets to the sender and the recipient.

*Emits a {CancelLockupStream} event.
Notes:
- This function will attempt to call a hook on either the sender or the recipient, depending upon who the
`msg.sender` is, and if the resolved address is a contract.
Requirements:
- `streamId` must point to an active stream.
- `msg.sender` must be either the sender of the stream or the recipient of the stream (also known as the
the owner of the NFT).
- The stream must be cancelable.*


```solidity
function cancel(uint256 streamId) external override isActiveStream(streamId);
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`streamId`|`uint256`|The id of the stream to cancel.|


### cancelMultiple

Cancels multiple streams and transfers any remaining assets to the sender and the recipient.

*Emits multiple {CancelLockupStream} events.
Notes:
- Does not revert if one of the stream ids points to a stream that is not active or is active but is
not cancelable.
- This function will attempt to call a hook on either the sender or the recipient of each stream.
Requirements:
- Each stream id in `streamIds` must point to an active stream.
- `msg.sender` must be either the sender of the stream or the recipient of the stream (also known as the
owner of the NFT) of every stream.
- Each stream must be cancelable.*


```solidity
function cancelMultiple(uint256[] calldata streamIds) external override;
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`streamIds`|`uint256[]`|The ids of the streams to cancel.|


### renounce

Makes the stream non-cancelable.

*Emits a {RenounceLockupStream} event.
Notes:
- This is an irreversible operation.
- This function will attempt to call a hook on the recipient of the stream, if the recipient is a contract.
Requirements:
- `streamId` must point to an active stream.
- `msg.sender` must be the sender.
- The stream must not be already non-cancelable.*


```solidity
function renounce(uint256 streamId) external override isActiveStream(streamId);
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`streamId`|`uint256`|The id of the stream to renounce.|


### withdraw

Withdraws the provided amount of assets from the stream to the provided address `to`.

*Emits a {WithdrawFromLockupStream} and a {Transfer} event.
Notes:
- This function will attempt to call a hook on the recipient of the stream, if the recipient is a contract.
Requirements:
- `streamId` must point to an active stream.
- `msg.sender` must be the sender of the stream, an approved operator, or the owner of the NFT (also known
as the recipient of the stream).
- `to` must be the recipient if `msg.sender` is the sender of the stream.
- `amount` must not be zero and must not exceed the withdrawable amount.*


```solidity
function withdraw(uint256 streamId, address to, uint128 amount)
    public
    override
    isActiveStream(streamId)
    isAuthorizedForStream(streamId);
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`streamId`|`uint256`|The id of the stream to withdraw.|
|`to`|`address`|The address that receives the withdrawn assets.|
|`amount`|`uint128`|The amount to withdraw, in units of the asset's decimals.|


### withdrawMax

Withdraws the maximum withdrawable amount from the stream to the provided address `to`.

*Emits a {WithdrawFromLockupStream} and a {Transfer} event.
Notes:
- All from {withdraw}.
Requirements:
- All from {withdraw}.*


```solidity
function withdrawMax(uint256 streamId, address to) external override;
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`streamId`|`uint256`|The id of the stream to withdraw.|
|`to`|`address`|The address that receives the withdrawn assets.|


### withdrawMultiple

Withdraws assets from multiple streams to the provided address `to`.

*Emits multiple {WithdrawFromLockupStream} and {Transfer} events.
Notes:
- Does not revert if one of the stream ids points to a stream that is not active.
- This function will attempt to call a hook on the recipient of each stream.
Requirements:
- The count of `streamIds` must match the count of `amounts`.
- `msg.sender` must be either the recipient of the stream (a.k.a the owner of the NFT) or an approved operator.
- Each amount in `amounts` must not be zero and must not exceed the withdrawable amount.*


```solidity
function withdrawMultiple(uint256[] calldata streamIds, address to, uint128[] calldata amounts) external override;
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`streamIds`|`uint256[]`|The ids of the streams to withdraw.|
|`to`|`address`|The address that receives the withdrawn assets, if the `msg.sender` is not the stream sender.|
|`amounts`|`uint128[]`|The amounts to withdraw, in units of the asset's decimals.|


### _isApprovedOrOwner

Checks whether the spender is authorized to interact with the stream.

*Unlike the ERC-721 implementation, this function does not check whether the owner is the zero address.*


```solidity
function _isApprovedOrOwner(uint256 streamId, address spender) internal view virtual returns (bool isApprovedOrOwner);
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`streamId`|`uint256`|The id of the stream to make the query for.|
|`spender`|`address`|The spender to make the query for.|


### _isCallerStreamSender

Checks whether `msg.sender` is the sender of the stream or not.


```solidity
function _isCallerStreamSender(uint256 streamId) internal view virtual returns (bool result);
```
**Parameters**

|Name|Type|Description|
|----|----|-----------|
|`streamId`|`uint256`|The id of the stream to make the query for.|

**Returns**

|Name|Type|Description|
|----|----|-----------|
|`result`|`bool`|Whether `msg.sender` is the sender of the stream or not.|


### _burn

*See the documentation for the public functions that call this internal function.*


```solidity
function _burn(uint256 tokenId) internal virtual;
```

### _cancel

*See the documentation for the public functions that call this internal function.*


```solidity
function _cancel(uint256 streamId) internal virtual;
```

### _renounce

*See the documentation for the public functions that call this internal function.*


```solidity
function _renounce(uint256 streamId) internal virtual;
```

### _withdraw

*See the documentation for the public functions that call this internal function.*


```solidity
function _withdraw(uint256 streamId, address to, uint128 amount) internal virtual;
```
