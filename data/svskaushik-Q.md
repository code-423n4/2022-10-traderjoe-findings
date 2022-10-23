## Low Severity Findings
### [L-01] Unspecific Compiler Version Pragma
#### Impact
Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.
#### Findings:
```solidity
2022-10-traderjoe\src\LBErrors.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\LBFactory.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\LBPair.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\LBQuoter.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\LBRouter.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\LBToken.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\IJoeFactory.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\IJoePair.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\IJoeRouter01.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\IJoeRouter02.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\ILBFactory.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\ILBFlashLoanCallback.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\ILBPair.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\ILBRouter.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\ILBToken.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\IPendingOwnable.sol::3 => pragma solidity ^0.8.0;
2022-10-traderjoe\src\interfaces\IWAVAX.sol::3 => pragma solidity ^0.8.0;
```
#### Recommendation
Avoid floating pragmas for non-library contracts. It is recommended to pin to a concrete compiler version.

### [L-02] `_safeMint()` should be used rather than `_mint()` wherever possible.
#### Impact
`_mint()` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of `_safeMint()` which ensures that the recipient is either an EOA or implements `IERC721Receiver`.
#### Findings:
```solidity
2022-10-traderjoe\src\LBPair.sol::579 => _mint(_to, _mintInfo.id, _liquidity);
```
#### Recommendation
Use either [OpenZeppelin's](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) or [solmate's](https://github.com/transmissions11/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) version of this function.

## Non-Critical Findings
### [N-01] Declare uint as uint256
#### Findings:
```solidity
2022-10-traderjoe\src\libraries\SafeCast.sol::9 => /// @notice Helper contract used for converting uint values safely
```
#### Recommendation
To favor explicitness, all instances of uint should be declared as uint256.