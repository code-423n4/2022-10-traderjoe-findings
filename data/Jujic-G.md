Gas report ( 9 findings with 31 instances )

## 1. Free gas savings for using solidity 0.8.10+ (Updating to the newer version of solc)
Solidity 0.8.10 has a useful change which reduced gas costs of external calls which expect a return value: https://blog.soliditylang.org/2021/11/09/solidity-0.8.10-release-announcement/
Updating to the newer version of solc will allow TraderJoe to take advantage of these lower costs for external calls.

```
pragma solidity ^0.8.0;
```
In all scope files.

## 2. Change  encode to encodePacked
In the contracts, change abi.encode to abi.encodePacked can save gas.
```
bytes32 _salt = keccak256(abi.encode(_tokenA, _tokenB, _binStep));
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L265

## 3. Unnecessary msg.sender cache
Its unnecessary to store msg.sender in the _spender. Just pass msg.sender  on the emit TransferSingle
```
function safeTransferFrom(
        address _from,
        address _to,
        uint256 _id,
        uint256 _amount
    ) public virtual override checkAddresses(_from, _to) checkApproval(_from, msg.sender) {
        address _spender = msg.sender;

        _transfer(_from, _to, _id, _amount);

        emit TransferSingle(_spender, _from, _to, _id, _amount);
    }
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L141      
## 4. > 0 can be replaced with != 0 for gas optimisation
*There are 7 instances of this issue:* 
```
if (_nbAvailable > 0) 
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L191
```
if (quote.pairs[i] != address(0) && quote.amounts[i] > 0) 
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L79
```
if (reserveIn > 0 && reserveOut > 0) {
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L82
```
if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) {
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L99
```
for (uint256 i = swapLength; i > 0; i--)
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L154
```
if (quote.pairs[i - 1] != address(0) && quote.amounts[i] > 0)
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L157
```
if (LBPairsAvailable.length > 0 && quote.amounts[i] > 0) 
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L176


## 5. Add unchecked {} for subtractions where the operands cannot underflow because of a previous require() or if-statement
*There are 4 instances of this issue:* 
```
// Can't overflow as the max value is `max(uint24) * (max(uint24) * max(uint16)) ** 2 < max(uint104)`
            // It returns 18 decimals as:
            // decimals(variableFeeControl * (volatilityAccumulated * binStep)**2 / 100) = 4 + (4 + 4) * 2 - 2 = 18
            uint256 _prod = uint256(_maxVolatilityAccumulated) * _binStep;
            uint256 _maxVariableFee = (_prod * _prod * _variableFeeControl) / 100;

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L560-L564
```
uint256 swapLength = _route.length — 1;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L65
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBQuoter.sol#L144

```
if (msg.value > amountsIn[0]) _safeTransferAVAX(_to, amountsIn[0] - msg.value);
```     
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L520


## 6.Using bools for storage incurs overhead
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27
Use uint256(1) and uint256(2) for true/false to avoid for the extra SLOAD, and to avoid Gsset (20000 gas) when changing from 'false' to 'true', after having been 'true' in the past.
*There are _ instances of this issue:* 
```
bool public override creationUnlocked;
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBFactory.sol#L39

## 7. Cashe  wavax
*There are 2 instances of this issue:* 
```
function removeLiquidityAVAX(
        IERC20 _token,
        uint16 _binStep,
        uint256 _amountTokenMin,
        uint256 _amountAVAXMin,
        uint256[] memory _ids,
        uint256[] memory _amounts,
        address payable _to,
        uint256 _deadline
    ) external override ensure(_deadline) returns (uint256 amountToken, uint256 amountAVAX) {
        ILBPair _LBPair = _getLBPairInformation(_token, IERC20(wavax), _binStep);

        bool _isAVAXTokenY = IERC20(wavax) == _LBPair.tokenY();
        {
            if (!_isAVAXTokenY) {
                (_amountTokenMin, _amountAVAXMin) = (_amountAVAXMin, _amountTokenMin);
            }

            (uint256 _amountX, uint256 _amountY) = _removeLiquidity(
                _LBPair,
                _amountTokenMin,
                _amountAVAXMin,
                _ids,
                _amounts,
                address(this)
            );

            (amountToken, amountAVAX) = _isAVAXTokenY ? (_amountX, _amountY) : (_amountY, _amountX);
        }

        _token.safeTransfer(_to, amountToken);

        wavax.withdraw(amountAVAX);
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L308-L342
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L561-L585

 ## 8. Cashe array length in loops
*There are 11 instances of this issue:* 
```
for (uint256 i; i < _ids.length; ++i) {
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L274
```
for (uint256 i; i < _ids.length; ++i)
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L496
```
for (uint256 i; i < _ids.length; ++i) 
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L623
```
for (uint256 i; i < _ids.length; ++i)
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L701

```
for (uint256 i = _pairs.length; i != 0; i--) {
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L711
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L778
```
for (uint256 i; i < _pairs.length; ++i) {
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L831
```
for (uint256 i; i < _pairs.length; ++i) 
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L878
```
for (uint256 i; i < pairs.length; ++i)
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBRouter.sol#L951
```
for (uint256 i; i < _accounts.length; ++i) {
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L90

```
for (uint256 i; i < _ids.length; ++i) 
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBToken.sol#L163


## 9. Using calldata instead of memory for read-only arguments in external functions saves gas
*There are 3 instances of this issue:* 

```

function pendingFees(address _account, uint256[] memory _ids)
```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L261

```
function mint(
        uint256[] memory _ids,
        uint256[] memory _distributionX,
        uint256[] memory _distributionY,
        address _to
    )

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L467

```
function collectFees(address _account, uint256[] memory _ids)

```
https://github.com/code-423n4/2022-10-traderjoe/blob/79f25d48b907f9d0379dd803fc2abc9c5f57db93/src/LBPair.sol#L688






