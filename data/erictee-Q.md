### [L-01] ```require()``` should be used instead of ```assert()```


#### Impact
Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as ```require()```/```revert()``` do. ```assert()``` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.


#### Findings:
```
src/LBFactory.sol:L141        assert(_binStep == _preset.decode(type(uint16).max, _shift));

```

### [L-02] Avoid floating pragmas for non-library contracts.


#### Impact
While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### Findings:
```
src/LBToken.sol:L3     pragma solidity ^0.8.0;

src/LBQuoter.sol:L3     pragma solidity ^0.8.0;

src/LBErrors.sol:L3     pragma solidity ^0.8.0;

src/LBFactory.sol:L3     pragma solidity ^0.8.0;

src/LBRouter.sol:L3     pragma solidity ^0.8.0;

src/LBPair.sol:L3     pragma solidity ^0.8.0;

src/interfaces/IPendingOwnable.sol:L3     pragma solidity ^0.8.0;

src/interfaces/ILBPair.sol:L3     pragma solidity ^0.8.0;

src/interfaces/ILBRouter.sol:L3     pragma solidity ^0.8.0;

src/interfaces/IJoePair.sol:L3     pragma solidity ^0.8.0;

src/interfaces/ILBFlashLoanCallback.sol:L3     pragma solidity ^0.8.0;

src/interfaces/ILBFactory.sol:L3     pragma solidity ^0.8.0;

src/interfaces/IWAVAX.sol:L3     pragma solidity ^0.8.0;

src/interfaces/IJoeRouter02.sol:L3     pragma solidity ^0.8.0;

src/interfaces/IJoeRouter01.sol:L3     pragma solidity ^0.8.0;

src/interfaces/ILBToken.sol:L3     pragma solidity ^0.8.0;

src/interfaces/IJoeFactory.sol:L3     pragma solidity ^0.8.0;

```

### [L-03] ```_safemint()``` should be used rather than ```_mint()``` wherever possible


#### Impact
```_mint()``` is [discouraged](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L271) in favor of ```_safeMint()``` which ensures that the recipient is either an EOA or implements ```IERC721Receiver```. Both [OpenZeppelin](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d4d8d2ed9798cc3383912a23b5e8d5cb602f7d4b/contracts/token/ERC721/ERC721.sol#L238-L250) and [solmate](https://github.com/transmissions11/solmate/blob/4eaf6b68202e36f67cab379768ac6be304c8ebde/src/tokens/ERC721.sol#L180) have versions of this function


#### Findings:
```
src/LBToken.sol:L202    function _mint(

src/LBPair.sol:L579                _mint(_to, _mintInfo.id, _liquidity);

```
