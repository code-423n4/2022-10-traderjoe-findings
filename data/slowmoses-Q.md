## Consider Uniform SPDX License

Not all source files specify the same license.

***

// SPDX-License-Identifier: MIT
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L1

// SPDX-License-Identifier: GPL-3.0
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeFactory.sol#L1

## Solidity Version

Version ^0.8.0 is specified in all files. A fixed version of solidity should be specified in all non-library files.

***

pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBErrors.sol#L3

## Sensitive Terms

Avoid the use of sensitive terms in favor of neutral ones. Use allowlist rather than whitelist.

***

Searching for white:
    error LBFactory__QuoteAssetNotWhitelisted(IERC20 quoteAsset);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBErrors.sol#L52

    error LBFactory__QuoteAssetAlreadyWhitelisted(IERC20 quoteAsset);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBErrors.sol#L53

    EnumerableSet.AddressSet private _quoteAssetWhitelist;
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L54

    /// @notice View function to return the number of quote assets whitelisted
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L76

    return _quoteAssetWhitelist.length();
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L79

    /// @notice View function to return the quote asset whitelisted at index `index`
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L82

    return IERC20(_quoteAssetWhitelist.at(_index));
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L86

    return _quoteAssetWhitelist.contains(address(_token));
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L93

    if (!_quoteAssetWhitelist.contains(address(_tokenY))) revert LBFactory__QuoteAssetNotWhitelisted(_tokenY);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L247

    /// @notice Function to add an asset to the whitelist of quote assets
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L491

    if (!_quoteAssetWhitelist.add(address(_quoteAsset)))
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L494

    revert LBFactory__QuoteAssetAlreadyWhitelisted(_quoteAsset);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L495

    /// @notice Function to remove an asset to the whitelist of quote assets
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L500

    if (!_quoteAssetWhitelist.remove(address(_quoteAsset))) revert LBFactory__QuoteAssetNotWhitelisted(_quoteAsset);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L503

## Missing Indexed Fields in Events

Each event should use three indexed fields if there are three or more fields in the event.

***

event PairCreated(address indexed token0, address indexed token1, address pair, uint256);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeFactory.sol#L8

event Burn(address indexed sender, uint256 amount0, uint256 amount1, address indexed to);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L50

event Swap(
    address indexed sender,
    uint256 amount0In,
    uint256 amount1In,
    uint256 amount0Out,
    uint256 amount1Out,
    address indexed to
);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L51-L58

event FeeParametersSet(
    address indexed sender,
    ILBPair indexed LBPair,
    uint256 binStep,
    uint256 baseFactor,
    uint256 filterPeriod,
    uint256 decayPeriod,
    uint256 reductionFactor,
    uint256 variableFeeControl,
    uint256 protocolShare,
    uint256 maxVolatilityAccumulated
);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBFactory.sol#L38-L49

event PresetSet(
    uint256 indexed binStep,
    uint256 baseFactor,
    uint256 filterPeriod,
    uint256 decayPeriod,
    uint256 reductionFactor,
    uint256 variableFeeControl,
    uint256 protocolShare,
    uint256 maxVolatilityAccumulated,
    uint256 sampleLifetime
);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBFactory.sol#L57-L67

event FlashLoan(
    address indexed sender,
    address indexed recipient,
    uint256 amountX,
    uint256 amountY,
    uint256 feesX,
    uint256 feesY
);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBPair.sol#L111-L118

event FeesCollected(address indexed sender, address indexed recipient, uint256 amountX, uint256 amountY);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBPair.sol#L148

event ProtocolFeesCollected(address indexed sender, address indexed recipient, uint256 amountX, uint256 amountY);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/ILBPair.sol#L150

## Avoid Magic Numbers

Checking against magic numbers should be avoided.
Consider using a properly named constant instead.

***

_avLBPairBinSteps = bytes32(uint256(_avLBPairBinSteps) + (1 << 248));
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L285

(uint256(_packedFeeParameters) & type(uint144).max) | (uint256(_sampleLifetime) << 240)
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L364

_avPresets = bytes32(uint256(_avPresets) + (1 << 248));
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L375

_avPresets = bytes32(uint256(_avPresets) - (1 << 248));
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L403

_unclaimedFees[_user] = bytes32((amountY << 128) | amountX);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L899

## Avoid Overlong Lines

Limit line length to 164 characters.
Longer lines will need scrollbars in github and are hard to read anyways.

***

                    uint256 _weightPrev = _next.timestamp() - _lookUpTimestamp; // _next.timestamp() - _sample.timestamp() - (_lookUpTimestamp - _sample.timestamp())

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol#L69

                    uint256 _weightNext = _lookUpTimestamp - _sample.timestamp(); // _next.timestamp() - _sample.timestamp() - (_next.timestamp() - _lookUpTimestamp)

https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Oracle.sol#L70

## Missing NatSpec

Every function should have a short textual description.

In addition to the textual description, there should be
a formal documentation for every parameter (@param).

A lot of documentation is missing in this project. Some undocumented functions are listed below. There are many more.

***

Undocumented function:

    function forceDecay(ILBPair _LBPair) external override onlyOwner {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBFactory.sol#L520-L520

***

Undocumented function:

    function forceDecay() external override onlyFactory {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L792-L792

***

Undocumented function:

    function _getPairs(uint256[] memory _pairBinSteps, IERC20[] memory _tokenPath)
    private
    view
    returns (address[] memory pairs)
    {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L941-L945

***

Undocumented function:

    function feeTo() external view returns (address);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeFactory.sol#L10-L10

***

Undocumented function:

    function feeToSetter() external view returns (address);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeFactory.sol#L12-L12

***

Undocumented function:

    function migrator() external view returns (address);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeFactory.sol#L14-L14

***

Undocumented function:

    function getPair(address tokenA, address tokenB) external view returns (address pair);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeFactory.sol#L16-L16

***

Undocumented function:

    function allPairs(uint256) external view returns (address pair);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeFactory.sol#L18-L18

***

Undocumented function:

    function allPairsLength() external view returns (uint256);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeFactory.sol#L20-L20

***

Undocumented function:

    function createPair(address tokenA, address tokenB) external returns (address pair);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeFactory.sol#L22-L22

***

Undocumented function:

    function setFeeTo(address) external;
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeFactory.sol#L24-L24

***

Undocumented function:

    function setFeeToSetter(address) external;
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeFactory.sol#L26-L26

***

Undocumented function:

    function setMigrator(address) external;
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoeFactory.sol#L28-L28

***

Undocumented function:

    function name() external pure returns (string memory);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L11-L11

***

Undocumented function:

    function symbol() external pure returns (string memory);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L13-L13

***

Undocumented function:

    function decimals() external pure returns (uint8);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L15-L15

***

Undocumented function:

    function totalSupply() external view returns (uint256);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L17-L17

***

Undocumented function:

    function balanceOf(address owner) external view returns (uint256);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L19-L19

***

Undocumented function:

    function allowance(address owner, address spender) external view returns (uint256);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L21-L21

***

Undocumented function:

    function approve(address spender, uint256 value) external returns (bool);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L23-L23

***

Undocumented function:

    function transfer(address to, uint256 value) external returns (bool);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L25-L25

***

Undocumented function:

    function transferFrom(
    address from,
    address to,
    uint256 value
    ) external returns (bool);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L27-L31

***

Undocumented function:

    function DOMAIN_SEPARATOR() external view returns (bytes32);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L33-L33

***

Undocumented function:

    function PERMIT_TYPEHASH() external pure returns (bytes32);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L35-L35

***

Undocumented function:

    function nonces(address owner) external view returns (uint256);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L37-L37

***

Undocumented function:

    function permit(
    address owner,
    address spender,
    uint256 value,
    uint256 deadline,
    uint8 v,
    bytes32 r,
    bytes32 s
    ) external;
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L39-L47

***

Undocumented function:

    function MINIMUM_LIQUIDITY() external pure returns (uint256);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L61-L61

***

Undocumented function:

    function factory() external view returns (address);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L63-L63

***

Undocumented function:

    function token0() external view returns (address);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L65-L65

***

Undocumented function:

    function token1() external view returns (address);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L67-L67

***

Undocumented function:

    function getReserves()
    external
    view
    returns (
    uint112 reserve0,
    uint112 reserve1,
    uint32 blockTimestampLast
    );
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L69-L76

***

Undocumented function:

    function price0CumulativeLast() external view returns (uint256);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L78-L78

***

Undocumented function:

    function price1CumulativeLast() external view returns (uint256);
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/interfaces/IJoePair.sol#L80-L80


