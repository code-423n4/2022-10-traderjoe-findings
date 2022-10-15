
## [G001] Cache Array Length Outside of Loops

#### Impact

Cache the length of arrays in loops. **(~6 gas per iteration)**
The overheads outlined below are PER LOOP, excluding the first loop
 - storage arrays incur a Gwarmaccess (100 gas)
 - memory arrays use MLOAD (3 gas)
 - calldata arrays use CALLDATALOAD (3 gas)

```solidity
// from
for (uint256 i; i < array.length; ++i) {

// to
uint256 length = array.length;
for (uint256 i; i < length; ++i) {
```

#### Findings:
```solidity
src/LBPair.sol::274 => for (uint256 i; i < _ids.length; ++i) {
src/LBPair.sol::496 => for (uint256 i; i < _ids.length; ++i) {
src/LBPair.sol::623 => for (uint256 i; i < _ids.length; ++i) {
src/LBPair.sol::701 => for (uint256 i; i < _ids.length; ++i) {
src/LBQuoter.sol::100 => for (uint256 j; j < LBPairsAvailable.length; j++) {
src/LBQuoter.sol::177 => for (uint256 j; j < LBPairsAvailable.length; j++) {
src/LBRouter.sol::674 => for (uint256 i; i < depositIds.length; ++i) {
src/LBRouter.sol::711 => for (uint256 i = _pairs.length; i != 0; i--) {
src/LBRouter.sol::778 => for (uint256 i; i < _pairs.length; ++i) {
src/LBRouter.sol::831 => for (uint256 i; i < _pairs.length; ++i) {
src/LBRouter.sol::878 => for (uint256 i; i < _pairs.length; ++i) {
src/LBRouter.sol::951 => for (uint256 i; i < pairs.length; ++i) {
src/LBToken.sol::90 => for (uint256 i; i < _accounts.length; ++i) {
src/LBToken.sol::163 => for (uint256 i; i < _ids.length; ++i) {
```

## [G002] Functions guaranteed to revert when called by normal users can be marked as payable

#### Impact

If a function modifier suck as onlyOwner | onlyAdmin is used, the function will revert if a normal user tries to pay the function.
Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.
The extra opcodes avoided are:
CALLVALUE (2 gas), DUP1(3 gas), ISZERO (3 gas), PUSH2(3 gas), JUMPI (10 gas), PUSH1(3 gas), DUPI(3 gas), REVERT(0), JUMPDEST (1 gas), POP (2 gas)
Total: **30 gas saved per call** approx to the function, in addition to the extra deployment cost.

```solidity
// from
function foo() external onlyOwner {

// to
function foo() external payable onlyOwner {
```

#### Findings:
```solidity
src/LBFactory.sol::215 => function setLBPairImplementation(address _LBPairImplementation) external override onlyOwner {
src/LBFactory.sol::317 => ) external override onlyOwner {
src/LBFactory.sol::350 => ) external override onlyOwner {
src/LBFactory.sol::396 => function removePreset(uint16 _binStep) external override onlyOwner {
src/LBFactory.sol::434 => ) external override onlyOwner {
src/LBFactory.sol::468 => function setFeeRecipient(address _feeRecipient) external override onlyOwner {
src/LBFactory.sol::474 => function setFlashLoanFee(uint256 _flashLoanFee) external override onlyOwner {
src/LBFactory.sol::485 => function setFactoryLockedState(bool _locked) external override onlyOwner {
src/LBFactory.sol::493 => function addQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {
src/LBFactory.sol::502 => function removeQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {
src/LBFactory.sol::520 => function forceDecay(ILBPair _LBPair) external override onlyOwner {
src/libraries/PendingOwnable.sol::59 => function setPendingOwner(address pendingOwner_) public override onlyOwner {
src/libraries/PendingOwnable.sol::68 => function revokePendingOwner() public override onlyOwner {
src/libraries/PendingOwnable.sol::84 => function renounceOwnership() public override onlyOwner {
```

## [G003] ++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)

#### Impact

Use `++i` instead of `i++`, specially in loops. Find searching `i++` (~5 gas saved per iteration)
This is especially useful in for loops but this optimization can be used anywhere in your code. You can also use `unchecked{++i;}` for even more gas savings but this will not check to see if `i` overflows, which I do not recommend. For extra safety if you are worried about this, you can add a require statement after the loop checking if `i` is equal to the final incremented value. For best gas savings, use inline assembly, however this limits the functionality you can achieve. For example you cant use Solidity syntax to internally call your own contract within an assembly block and external calls must be done with the `call()` or `delegatecall()` instruction. However when applicable, inline assembly will save much more gas.

```solidity
// from
for (uint256 i; i < 100; i++) {

// to
for (uint256 i; i < 100; ++i) {
```

#### Findings:
```solidity
src/LBQuoter.sol::75 => for (uint256 i; i < swapLength; i++) {
src/LBQuoter.sol::100 => for (uint256 j; j < LBPairsAvailable.length; j++) {
src/LBQuoter.sol::177 => for (uint256 j; j < LBPairsAvailable.length; j++) {
```

## [G004] Use assembly to check for balance

#### Impact

Checking for balance can be done with assembly, which is cheaper.

```solidity
// from
function addressExternalBalance(address addr) public view returns (uint256) {
  return address(addr).balance;
}

// to
function assemblyExternalBalance(address addr) public view returns (uint256) {
  assembly {
    let bal := balance(addr)
      mstore(0x00, bal)
      return(0x00, 0x20)
  }
}

// from
function addressInternalBalance() public view returns (uint256) {
  return address(this).balance;
}

// to
function assemblyInternalBalance() public view returns (uint256) {
  assembly {
    let c := selfbalance()
    mstore(0x00, c)
    return(0x00, 0x20)
  }
}
```

#### Findings:
```solidity
src/LBRouter.sol::628 => if (_amount == type(uint256).max) _amount = address(this).balance;
```


