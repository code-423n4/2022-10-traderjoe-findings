### Non-Critical Issues List
| Number |Issues Details|Context|
|:--:|:-------|:--:|
| [N-01 ]| Insufficient coverage file | |
| [N-02] |Argument assignment architecture of setFactoryLockedState function may cause error|1|
| [N-03] | `0` address check | 6 |
| [N-04] | Use `require` instead of `assert` | 1 |
| [N-05] | For modern and more readable code; update import usages | All contracts |
| [N-06] | `Empty blocks` should be _removed_ or _Emit_ something | 1 |
| [N-07] | `Function writing` that does not comply with the `Solidity Style Guide`| 5 |
| [N-08] | Compliance with Solidity Style rules in Constant expressions | 2 |
| [N-09] | Omissions in Events |  |
| [N-10] | Need Fuzzing test |  |
| [N-11] |Use a more recent version of Solidity | All contracts |
| [N-12] | Solidity compiler optimizations can be problematic | 1 |

Total 12 issues


### Low Risk Issues List
| Number |Issues Details|Context
|:--:|:-------|:--:|
|[L-01]| Use ```safeTransferOwnership``` instead of ```transferOwnership``` function | 1 |
|[L-02]| Owner can renounce Ownership| 1 |
|[L-03]| Use a more recent version of OpenZeppelin dependencies | All contracts |
|[L-04]| WAWAX address definition can be use directly | 1 |
|[L-05]| 2 step changes for privileged contract addresses | 1 |

Total 5 issues

### Suggestions
| Number | Suggestion Details |
|:--:|:-------|
| [S-01] |Add to _blacklist function_ |
| [S-02] | Generate perfect code headers every time |

Total 2 suggestions



### [N-01] Insufficient coverage

**Description:**
The test coverage rate of the project is 63%. Testing all functions is best practice in terms of security criteria.
```js
+-------------+-------------------+--------------------+-------------------+------------------+
| File        | % Lines           | % Statements       | % Branches        | % Funcs          |
+=============================================================================================+
| Total       | 62.23% (850/1366) | 63.67% (1069/1679) | 49.09% (296/603)  | 55.42% (138/249) |
+-------------+-------------------+--------------------+-------------------+------------------+ 
```
Due to its capacity, test coverage is expected to be 100%

### [N-02] Argument assignment architecture of setFactoryLockedState function may cause error

**Context:**
[LBFactory.sol#L485](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L485)

**Description:**
The setFactoryLockedState function replaces the "creationUnlocked bool" value. But it reverses the result with value input

For example ;
When you set the _locked argument to true , creationUnlocked gets false
When you set the _locked argument to false , creationUnlocked gets true

The opposite of the desired value and the required value may cause an error.

```js
 function setFactoryLockedState(bool _locked) external override onlyOwner {
        if (creationUnlocked != _locked) revert LBFactory__FactoryLockIsAlreadyInTheSameState();
        creationUnlocked = !_locked;
        emit FactoryLockedStatusUpdated(_locked);
    }
```

**Recommendation:**
Change the architecture so that the input and output are the same

```js
 function setFactoryLockedState(bool _locked) external override onlyOwner {
        if (creationUnlocked = _locked) revert LBFactory__FactoryLockIsAlreadyInTheSameState();
        creationUnlocked = _locked;
        emit FactoryLockedStatusUpdated(_locked);
    }
```

### [N-03] `0 address` check

**Context:**
[LBQuoter.sol#L45](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L45)
[LBQuoter.sol#L46](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L46)
[LBQuoter.sol#L47](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L47)
[LBRouter.sol#L57](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L57)
[LBRouter.sol#L58](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L58)
[LBRouter.sol#L59](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L59)

**Description:**
Also check of the address to protect the code from 0x0 address  problem just in case. This is best practice or instead of suggesting that they verify address != 0x0, you could add some good NatSpec comments explaining what is valid and what is invalid and what are the implications of accidentally using an invalid address.

**Recommendation:**
like this;
`if (routerV2== address(0)) revert ADDRESS_ZERO();`


### [N-04] Use `require` instead of `assert`

**Context:**
[LBFactory.sol#L141](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L141)

**Description:**
Assert should not be used except for tests, `require` should be used

Prior to Solidity 0.8.0, pressing a confirm consumes the remainder of the process's available gas instead of returning it, as request()/revert() did.

assert() and ruqire();
The big difference between the two is that the `assert()`function when false, uses up all the remaining gas and reverts all the changes made.
Meanwhile, a  `require()` function when false, also reverts back all the changes made to the contract but does refund all the remaining gas fees we offered to pay.This is the most common Solidity function used by developers for debugging and error handling.

Assertion() should be avoided even after solidity version 0.8.0, because its documentation states "The Assert function generates an error of type Panic(uint256). Code that works properly should never Panic, even on invalid external input. If this happens, you need to fix it in your contract. there's a mistake".

### [N-05] For modern and more readable code; update import usages

**Context:**
All contracts

**Description:**
Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct `polluted the source code` with an unnecessary object we were not using because we did not need it. 
This was breaking the rule of modularity and modular programming: `only import what you need` Specific imports with curly braces allow us to apply this rule better.

**Recommendation:**
`import {contract1 , contract2} from "filename.sol";`

A good example from the ArtGobblers project;
```js
import {Owned} from "solmate/auth/Owned.sol";
import {ERC721} from "solmate/tokens/ERC721.sol";
import {LibString} from "solmate/utils/LibString.sol";
import {MerkleProofLib} from "solmate/utils/MerkleProofLib.sol";
import {FixedPointMathLib} from "solmate/utils/FixedPointMathLib.sol";
import {ERC1155, ERC1155TokenReceiver} from "solmate/tokens/ERC1155.sol";
import {toWadUnsafe, toDaysWadUnsafe} from "solmate/utils/SignedWadMath.sol";
```

### [N-06] `Empty blocks` should be _removed_ or _Emit_ something

**Context:**
[LBToken.sol#L326](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L326)

**Description:**
Code contains empty block

```js
 function _beforeTokenTransfer(
        address from,
        address to,
        uint256 id,
        uint256 amount
    ) internal virtual {}
}
```

**Recommendation:**
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

If the code is not upgradeable and this function is not used, remove it directly

### [N-07] `Function writing` that does not comply with the `Solidity Style Guide`

**Context:**
[LBRouter.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol)
[LBFactory.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol)
[LBPair.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol)
[LBRouter.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol)
[LBToken.sol](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol)

**Description:**
Order of Functions; ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.

https://docs.soliditylang.org/en/v0.8.17/style-guide.html

Functions should be grouped according to their visibility and ordered:

- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private
- within a grouping, place the view and pure functions last

### [N-08] Compliance with Solidity Style rules in Constant expressions

**Context:**
[LBToken.sol#L29](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L29)
[LBToken.sol#L30](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L30)


**Recommendation:**
Variables are declared as constant utilize the UPPER_CASE_WITH_UNDERSCORES format.

### [N-09] Omissions in Events

Throughout the codebase, events are generally emitted when sensitive changes are made to the contracts. However, some events are missing important parameters

The events should include the new value and old value where possible:

Events with no old value;
[PendingOwnable.sol#L103](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L103)

### [N-10] Need Fuzzing test

**Context:**
35 results - 9 files
Project uncheckeds list:

```js

39 results - 9 files

src/LBFactory.sol:
  162:         unchecked {
  190:         unchecked {
src/LBPair.sol:
  224:         unchecked {
  273:         unchecked {
  398:         unchecked {
  495:         unchecked {
  622:         unchecked {
  694:         unchecked {
  736:         unchecked {
  793:         unchecked {
  814:         unchecked {
  887:         unchecked {
  928:         unchecked {
src/LBRouter.sol:
  661:         unchecked {
  777:         unchecked {
  830:         unchecked {
  877:         unchecked {
  950:         unchecked {
src/LBToken.sol:
   89:          unchecked {
  162:         unchecked {
  189:         unchecked {
  214:         unchecked {
  239:         unchecked {
src/libraries/BinHelper.sol:
  23:           unchecked {
  41:           unchecked {
  54:           unchecked {
src/libraries/BitMath.sol:
   40:          unchecked {
   57:          unchecked {
   70:          unchecked {
  109:         unchecked {
src/libraries/FeeDistributionHelper.sol:
  41:           unchecked {
  54:           unchecked {
src/libraries/Math128x128.sol:
  46:          unchecked {
src/libraries/Math512Bits.sol:
   70:         unchecked {
  129:        unchecked {
```

**Description:**
In total 9 contracts, 39 unchecked are used, the functions used are critical. For this reason, there must be fuzzing tests in the tests of the project. Not seen in tests.

**Recommendation:**
Use should fuzzing test like Echidna.

As Alberto Cuesta Canada said:
_Fuzzing is not easy, the tools are rough, and the math is hard, but it is worth it. Fuzzing gives me a level of confidence in my smart contracts that I didn’t have before. Relying just on unit testing anymore and poking around in a testnet seems reckless now._

https://medium.com/coinmonks/smart-contract-fuzzing-d9b88e0b0a05

### [N-11] Use a more recent version of Solidity

**Context:**
All contracts

**Description:**
For security, it is best practice to use the latest Solidity version.
For the security fix list in the versions;
https://github.com/ethereum/solidity/blob/develop/Changelog.md


**Recommendation:**
Old version of Solidity is used `(0.8.0)`, newer version can be used `(0.8.17)`

### [N-12] Solidity compiler optimizations can be problematic

**Context:**
[foundry.toml#L6](https://github.com/code-423n4/2022-10-traderjoe/blob/main/foundry.toml#L6)

```js
main/ foundry.toml#L6:

[profile.default]
src = 'src'
out = 'out'
libs = ['lib']
optimizer = true
optimizer_runs = 800
```

**Description:**
Protocol has enabled optional compiler optimizations in Solidity.
There have been several optimization bugs with security implications. Moreover, optimizations are actively being developed. Solidity compiler optimizations are disabled by default, and it is unclear how many contracts in the wild actually use them. 

Therefore, it is unclear how well they are being tested and exercised.
High-severity security issues due to optimization bugs have occurred in the past. A high-severity bug in the emscripten-generated solc-js compiler used by Truffle and Remix persisted until late 2018. The fix for this bug was not reported in the Solidity CHANGELOG. 

Another high-severity optimization bug resulting in incorrect bit shift results was patched in Solidity 0.5.6. More recently, another bug due to the incorrect caching of keccak256 was reported.
A compiler audit of Solidity from November 2018 concluded that the optional optimizations may not be safe.
It is likely that there are latent bugs related to optimization and that new bugs will be introduced due to future optimizations.

Exploit Scenario
A latent or future bug in Solidity compiler optimizations—or in the Emscripten transpilation to solc-js—causes a security vulnerability in the contracts.

**Recommendation:**
Short term, measure the gas savings from optimizations and carefully weigh them against the possibility of an optimization-related bug.
Long term, monitor the development and adoption of Solidity compiler optimizations to assess their maturity.



### [L-01] Use ```safeTransferOwnership``` instead of ```transferOwnership``` function

**Context:**
[PendingOwnable.sol#L91-L95](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L91-L95)

**Description:**
```transferOwnership``` function is used to change Ownership

Use a 2 structure transferOwnership which is safer. 
```safeTransferOwnership```,  use it is more secure due to 2-stage ownership transfer.

**Recommendation:**
Use ``Ownable2Step.sol``
[Ownable2Step.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable2Step.sol)

```js
 /**
     * @dev The new owner accepts the ownership transfer.
     */
    function acceptOwnership() external {
        address sender = _msgSender();
        require(pendingOwner() == sender, "Ownable2Step: caller is not the new owner");
        _transferOwnership(sender);
    }
}
```
### [L-02] Owner can renounce Ownership

**Context:**
[PendingOwnable.sol#L84](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/PendingOwnable.sol#L84)

**Description:**
Typically, the contract’s owner is the account that deploys the contract. As a result, the owner is able to perform certain privileged activities.

The Trader Joe’s Ownable used in this project contract implements renounceOwnership. This can represent a certain risk if the ownership is renounced for any other reason than by design. Renouncing ownership will leave the contract without an owner, thereby removing any functionality that is only available to the owner.


`onlyOwner` functions;
```js
14 results - 2 files

src/LBFactory.sol:
  220:     function setLBPairImplementation(address _LBPairImplementation) external override onlyOwner {
  322:     ) external override onlyOwner {
  355:     ) external override onlyOwner {
  401:     function removePreset(uint16 _binStep) external override onlyOwner {
  439:     ) external override onlyOwner {
  473:     function setFeeRecipient(address _feeRecipient) external override onlyOwner {
  479:     function setFlashLoanFee(uint256 _flashLoanFee) external override onlyOwner {
  490:     function setFactoryLockedState(bool _locked) external override onlyOwner {
  498:     function addQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {
  507:     function removeQuoteAsset(IERC20 _quoteAsset) external override onlyOwner {
  525:     function forceDecay(ILBPair _LBPair) external override onlyOwner {

src/libraries/PendingOwnable.sol:
  59:     function setPendingOwner(address pendingOwner_) public override onlyOwner {
  68:     function revokePendingOwner() public override onlyOwner {
  84:     function renounceOwnership() public override onlyOwner {

```

**Recommendation:**
We recommend to either reimplement the function to disable it or to clearly specify if it is part of the contract design.

### [L-03] Use a more recent version of OpenZeppelin dependencies

**Context:**
All contracts

**Description:**
For security, it is best practice to use the latest OZ version.

[package.json#L4](https://github.com/code-423n4/2022-10-traderjoe/blob/main/lib/openzeppelin-contracts/package.json#L4)

```js
"name": "openzeppelin-solidity",
  "description": "Secure Smart Contract library for Solidity",
  "version": "4.6.0",
```

For the security fix list in the versions;
https://github.com/ethereum/solidity/blob/develop/Changelog.md


**Recommendation:**
Old version of OZ is used `(4.6.0)`, newer version can be used `(4.7.3)` 

https://github.com/OpenZeppelin/openzeppelin-contracts/releases/tag/v4.7.3


### [L-04] WAWAX address definition can be use directly

**Context:**
[LBRouter.sol#L59](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L59)

**Description:**
Wawax is a wrap avax contract with a specific address in the Avalanche network, giving the option to define it may cause false recognition, it is healthier to define it directly.

Advantages of defining a specific contract directly:
- It saves gas 
- Prevents incorrect argument definition 
- Prevents execution on a different chain and re-signature issues

WAWAX Address : 0xB31f66AA3C1e785363F0875A1B74E27b85FD66c7

```js
constructor(
        ILBFactory _factory,
        IJoeFactory _oldFactory,
        IWAVAX _wavax
    ) {
        factory = _factory;
        oldFactory = _oldFactory;
        wavax = _wavax;
    }
```

**Recommendation:**
```js
address private constant wavax = 0xc02aaa39b223fe8d0a0e5c4f27ead9083c756cc2;

constructor(
        ILBFactory _factory,
        IJoeFactory _oldFactory,
     ) {
        factory = _factory;
        oldFactory = _oldFactory;
    }
```


### [L-05] 2 step changes for privileged contract addresses

**Context:**
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L468


**Description:**
Some contracts in the project have setters for privileged addresses that control the contract logic, such as the keeper.It would be best to do a two step change for those addresses. 
First, nominate the address, and second accept the nomination from that address ensuring that the access is indeed secured

```
src/LBFactory.sol:
  530:     function setFeeRecipient(address _feeRecipient) external override onlyOwner {
  531          _setFeeRecipient(_feeRecipient);
```

**Recommendation:**

```js
///  _feeRecipient will only be changed if the new _feeRecipient accepts it. It will be pending till then.
function setFeeRecipient(address _feeRecipient) external override onlyOwner {
    pendingFeeRecipient = _feeRecipient;
}

function acceptFeeRecipient() public {
    require(msg.sender == pendingFeeRecipient, "INVALID_ADDRESS");
    _setFeeRecipient(pendingFeeRecipient);
}
```


### [S-01] Add to _blacklist function_

**Description:**
Cryptocurrency mixing service, Tornado Cash, has been blacklisted in the OFAC.
A lot of blockchain companies, token projects, NFT Projects have ```blacklisted``` all Ethereum addresses owned by Tornado Cash listed in the US Treasury Department's sanction against the protocol.
https://home.treasury.gov/policy-issues/financial-sanctions/recent-actions/20220808

Some of these Projects;
 - USDC 
 - Aave 
 - Uniswap
 - Balancer
 - Infura
 - Alchemy 
 - Opensea
 - dYdX 

For this reason, every project in the Avalanche network must have a blacklist function, this is a good method to avoid legal problems in the future, apart from the current need.

Transactions from the project by an account funded by Tonadocash or banned by OFAC can lead to legal problems.Especially American citizens may want to get addresses to the blacklist legally, this is not an obligation

If you think that such legal prohibitions should be made directly by validators, this may not be possible:
https://www.paradigm.xyz/2022/09/base-layer-neutrality

```The ban on Tornado Cash makes little sense, because in the end, no one can prevent people from using other mixer smart contracts, or forking the existing ones. It neither hinders cybercrime, nor privacy.```

Here is the most beautiful and close to the project example; Manifold

Manifold Contract
https://etherscan.io/address/0xe4e4003afe3765aca8149a82fc064c0b125b9e5a#code

```js
     modifier nonBlacklistRequired(address extension) {
         require(!_blacklistedExtensions.contains(extension), "Extension blacklisted");
         _;
     }
```
Recommended Mitigation Steps add to Blacklist function and modifier.


### [S-02] Generate perfect code headers every time

**Description:**
I recommend using header for Solidity code layout and readability

https://github.com/transmissions11/headers

```js
/*//////////////////////////////////////////////////////////////
                           TESTING 123
//////////////////////////////////////////////////////////////*/
```