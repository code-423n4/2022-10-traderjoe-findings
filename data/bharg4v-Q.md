# Quality Assurance Report

## Issues 


|   |      Issue     |  Instances |
|----------|-------------|:------:|
| 1 | Missing checks for address(0x0) when assigning values to address state variables | 5 |
| 2  |_safeMint() should be used rather than _mint() wherever possible | 1 |


### 1. Missing checks for address(0x0) when assigning values to address state variables
Zero address should be checked for state variables and some parameters in functions like mints, withdrawals... A zero address can lead into problems.

### POC 

```solidity
45┆ routerV2 = _routerV2;                                                                                                                                                                                                                     
```
### Instances
```
code4rena/2022-10-traderjoe/contracts/LBFactory.sol  L223 
code4rena/2022-10-traderjoe/contracts/LBFactory.sol  L516
code4rena/2022-10-traderjoe/contracts/LBQuoter.sol L45
code4rena/2022-10-traderjoe/contracts/LBQuoter.sol L46
code4rena/2022-10-traderjoe/contracts/LBQuoter.sol L47
```

### Mitigation 
Check zero address for state variables before assigning to them a value





### 2. _safeMint() should be used rather than _mint() wherever possible

_mint() is discouraged in favor of _safeMint() which ensures that the recipient is either an EOA or implements IERC721Receiver. Both open OpenZeppelin and solmate have versions of this function so that NFTs aren’t lost if they’re minted to contracts that cannot transfer them back out.

### POC 

```solidity
579┆ _mint(_to, _mintInfo.id, _liquidity); 
```
### Instances
```
code4rena/2022-10-traderjoe/contracts/LBPair.sol 
```
### Mitigation
 “Unless there is a compelling reason, abi.encode should be preferred”. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.


