1)++i costs less gas than i++, especially when it’s used in for-loops (--i/i-- too)

Saves 6 gas PER LOOP

File: 2022-10-traderjoe\src\LBQuoter.sol
  75,9:         for (uint256 i; i < swapLength; i++) {
  100,17:                 for (uint256 j; j < LBPairsAvailable.length; j++) {
  154,9:         for (uint256 i = swapLength; i > 0; i--) {
  177,17:                 for (uint256 j; j < LBPairsAvailable.length; j++) {

File: 2022-10-traderjoe\src\LBRouter.sol

  711,9:         for (uint256 i = _pairs.length; i != 0; i--) {
  
2) ++i/i++ should be unchecked{++i}/unchecked{++i} when it is not possible for them to overflow,
as is the case when used in for- and while-loops 

File: 2022-10-traderjoe\src\LBFactory.sol
  165,17:                 for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {
  195,17:                 for (uint256 i = MIN_BIN_STEP; i <= MAX_BIN_STEP; ++i) {

File: 2022-10-traderjoe\src\LBPair.sol
  274,13:             for (uint256 i; i < _ids.length; ++i) {
  496,13:             for (uint256 i; i < _ids.length; ++i) {
  623,13:             for (uint256 i; i < _ids.length; ++i) {
  701,13:             for (uint256 i; i < _ids.length; ++i) {
  929,13:             for (uint256 _id = _oracleSize; _id < _newSize; ++_id) {

File: 2022-10-traderjoe\src\LBQuoter.sol
  75,9:         for (uint256 i; i < swapLength; i++) {
  100,17:                 for (uint256 j; j < LBPairsAvailable.length; j++) {
  154,9:         for (uint256 i = swapLength; i > 0; i--) {
  177,17:                 for (uint256 j; j < LBPairsAvailable.length; j++) {

File: 2022-10-traderjoe\src\LBRouter.sol
  674,13:             for (uint256 i; i < depositIds.length; ++i) {
  711,9:         for (uint256 i = _pairs.length; i != 0; i--) {
  778,13:             for (uint256 i; i < _pairs.length; ++i) {
  831,13:             for (uint256 i; i < _pairs.length; ++i) {
  878,13:             for (uint256 i; i < _pairs.length; ++i) {
  951,13:             for (uint256 i; i < pairs.length; ++i) {

File: 2022-10-traderjoe\src\LBToken.sol
  90,13:             for (uint256 i; i < _accounts.length; ++i) {
  163,13:             for (uint256 i; i < _ids.length; ++i) {

3)<ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP
The overheads outlined below are PER LOOP, 

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset

File: 2022-10-traderjoe\src\LBPair.sol
  274,13:             for (uint256 i; i < _ids.length; ++i) {
  496,13:             for (uint256 i; i < _ids.length; ++i) {
  623,13:             for (uint256 i; i < _ids.length; ++i) {
  701,13:             for (uint256 i; i < _ids.length; ++i) {

File: 2022-10-traderjoe\src\LBQuoter.sol

  100,17:                 for (uint256 j; j < LBPairsAvailable.length; j++) {

  177,17:                 for (uint256 j; j < LBPairsAvailable.length; j++) {

File: 2022-10-traderjoe\src\LBRouter.sol
  674,13:             for (uint256 i; i < depositIds.length; ++i) {
  711,9:         for (uint256 i = _pairs.length; i != 0; i--) {
  778,13:             for (uint256 i; i < _pairs.length; ++i) {
  831,13:             for (uint256 i; i < _pairs.length; ++i) {
  878,13:             for (uint256 i; i < _pairs.length; ++i) {
  951,13:             for (uint256 i; i < pairs.length; ++i) {
  
File: 2022-10-traderjoe\src\LBToken.sol
  90,13:             for (uint256 i; i < _accounts.length; ++i) {
  163,13:             for (uint256 i; i < _ids.length; ++i) {


4)X = X + Y IS CHEAPER THAN X += Y

File: 2022-10-traderjoe\src\LBFactory.sol
  143,62:         baseFactor = _preset.decode(type(uint16).max, _shift += 16);
  144,64:         filterPeriod = _preset.decode(type(uint16).max, _shift += 16);
  145,63:         decayPeriod = _preset.decode(type(uint16).max, _shift += 16);
  146,67:         reductionFactor = _preset.decode(type(uint16).max, _shift += 16);
  147,70:         variableFeeControl = _preset.decode(type(uint24).max, _shift += 16);
  148,65:         protocolShare = _preset.decode(type(uint16).max, _shift += 24);
  149,76:         maxVolatilityAccumulated = _preset.decode(type(uint24).max, _shift += 16);

File: 2022-10-traderjoe\src\LBPair.sol
  227,30:                 cumulativeId += _activeId * _deltaT;
  228,49:                 cumulativeVolatilityAccumulated += uint256(_fp.volatilityAccumulated) * _deltaT;
  287,29:                     amountX += _amountX;
  288,29:                     amountY += _amountY;
  337,28:                 _amountOut += _amountOutOfBin;
  452,38:         _bins[_id].accTokenXPerShare += _feesX.getTokenPerShare(_totalSupply);
  453,38:         _bins[_id].accTokenYPerShare += _feesY.getTokenPerShare(_totalSupply);
  508,51:                     (_mintInfo.totalDistributionX += _mintInfo.distributionX) > Constants.PRECISION ||
  509,51:                     (_mintInfo.totalDistributionY += _mintInfo.distributionY) > Constants.PRECISION
  540,50:                             _mintInfo.activeFeeX += _fees.total;
  551,50:                             _mintInfo.activeFeeY += _fees.total;
  572,32:                 _pair.reserveX += uint112(_mintInfo.amountX);
  573,32:                 _pair.reserveY += uint112(_mintInfo.amountY);
  575,46:                 _mintInfo.amountXAddedToPair += _mintInfo.amountX;
  576,46:                 _mintInfo.amountYAddedToPair += _mintInfo.amountY;
  639,29:                     amountY += _amountY;
  646,29:                     amountX += _amountX;
  711,29:                     amountX += _amountX;
  712,29:                     amountY += _amountY;
  896,22:             (amountX += _amountX).safe128();
  897,22:             (amountY += _amountY).safe128();

File: 2022-10-traderjoe\src\LBRouter.sol
  128,26:                 amountIn += _amountInWithFees;
  129,24:                 feesIn += _fee;
  174,24:                 feesIn += _fees.total;
  175,27:                 amountOut += _amountOutOfBin;

File: 2022-10-traderjoe\src\LBToken.sol
  211,29:         _totalSupplies[_id] += _amount;

File: 2022-10-traderjoe\src\libraries\BitMath.sol
  77,21:                 msb += 64;
  81,21:                 msb += 32;
  85,21:                 msb += 16;
  89,21:                 msb += 8;
  93,21:                 msb += 4;
  97,21:                 msb += 2;
  100,21:                 msb += 1;
  116,21:                 lsb += 64;
  120,21:                 lsb += 32;
  124,21:                 lsb += 16;
  128,21:                 lsb += 8;
  132,21:                 lsb += 4;
  136,21:                 lsb += 2;
  139,21:                 lsb += 1;

File: 2022-10-traderjoe\src\libraries\FeeDistributionHelper.sol
  42,32:             _pairFees.protocol += _fees.protocol;

File: 2022-10-traderjoe\src\libraries\Math128x128.sol
  77,32:                         result += delta;

File: 2022-10-traderjoe\src\libraries\Math512Bits.sol
  71,24:                 result += prod1 << (256 - offset);
  99,56:             if (mulmod(x, y, 1 << offset) != 0) result += 1;
  159,66:             if (mulmod(x, 1 << offset, denominator) != 0) result += 1;

File: 2022-10-traderjoe\src\libraries\SwapHelper.sol
  88,24:         pairFees.total += fees.total;
  91,31:             pairFees.protocol += fees.protocol;
  95,35:             bin.accTokenXPerShare += fees.getTokenPerShare(totalSupply);
  97,35:             bin.accTokenYPerShare += fees.getTokenPerShare(totalSupply);
  115,26:             bin.reserveX += amountInToBin;
  119,31:                 pair.reserveX += uint136(amountInToBin);
  123,26:             bin.reserveY += amountInToBin;
  128,31:                 pair.reserveY += uint136(amountInToBin);  
  
5)USE A MORE RECENT VERSION OF SOLIDITY

Use a solidity version of at least 0.8.2 to get compiler automatic inlining

Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads

Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings

Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value



File: 2022-10-traderjoe\src\LBErrors.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\LBFactory.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\LBPair.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\LBQuoter.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\LBRouter.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\LBToken.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\interfaces\IJoeFactory.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\interfaces\IJoePair.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\interfaces\IJoeRouter01.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\interfaces\IJoeRouter02.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\interfaces\ILBFactory.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\interfaces\ILBFlashLoanCallback.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\interfaces\ILBPair.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\interfaces\ILBRouter.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\interfaces\ILBToken.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\interfaces\IPendingOwnable.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\interfaces\IWAVAX.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\libraries\BinHelper.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\libraries\BitMath.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\libraries\Buffer.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\libraries\Constants.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\libraries\Decoder.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\libraries\Encoder.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\libraries\FeeDistributionHelper.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\libraries\FeeHelper.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\libraries\JoeLibrary.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\libraries\Math128x128.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\libraries\Math512Bits.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\libraries\Oracle.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\libraries\PendingOwnable.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\libraries\ReentrancyGuardUpgradeable.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\libraries\SafeCast.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\libraries\SafeMath.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\libraries\Samples.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\libraries\SwapHelper.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\libraries\TokenHelper.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

File: 2022-10-traderjoe\src\libraries\TreeMath.sol:
  2  
  3: pragma solidity ^0.8.0;
  4  

6)Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code.
Savings are due to the compiler not having to create non-payable getter
functions for deployment calldata, and not adding another entry to the method ID table

File: 2022-10-traderjoe\src\LBFactory.sol
  25,13:     uint256 public constant override MAX_FEE = 0.1e18; // 10%
  27,13:     uint256 public constant override MIN_BIN_STEP = 1; // 0.01%
  28,13:     uint256 public constant override MAX_BIN_STEP = 100; // 1%, can't be greater than 247 for indexing reasons
  30,13:     uint256 public constant override MAX_PROTOCOL_SHARE = 2_500; // 25%


7)STATE VARIABLES CAN BE PACKED INTO FEWER STORAGE SLOTS
If variables occupying the same slot are both written the same function or by the constructor,
avoids a separate Gsset (20000 gas). Reads of the variables are also cheaper


File: 2022-10-traderjoe\src\interfaces\IJoePair.sol
 
40:        address owner,
41:        address spender,
42:        uint256 value,
43:        uint256 deadline,
44:        uint8 v,
45:        bytes32 r,
46:        bytes32 s

		
Variable ordering with 6 slots instead of the current 7: 
        uint8(1):v, address(20):owner, address(20):spender,uint256(32):value, uint256(32):deadline, 
          bytes32(32):r,bytes32(32):s		

File: 2022-10-traderjoe\src\interfaces\IJoeRouter01.sol


65:        address tokenA,
66:        address tokenB,
67:        uint256 liquidity,
68:        uint256 amountAMin,
69:        uint256 amountBMin,
70:        address to,
71:        uint256 deadline,
72:        bool approveMax,
73:        uint8 v,
74:        bytes32 r,
75:        bytes32 s

Variable ordering with 9 slots instead of the current 10: 
bool(1):approveMax,uint8(1):v,address(20):tokenA, address(20):tokenB, uint256(32):liquidity, uint256(32):amountAMin,
uint256(32):amountBMin, address(20):to, uint256(32):deadline,
bytes32(32):r,bytes32(32):s   

 
79:        address token,
80:        uint256 liquidity,
81:        uint256 amountTokenMin,
82:        uint256 amountAVAXMin,
83:        address to,
84:        uint256 deadline,
85:        bool approveMax,
86:        uint8 v,
87:        bytes32 r,
88:        bytes32 s

Variable ordering with 8 slots instead of the current 9: 
bool(1):approveMax,uint8(1):v,address(20):token, uint256(32):liquidity, uint256(32):amountTokenMin,
uint256(32):amountAVAXMin, address(20):to, uint256(32):deadline,
bytes32(32):r,bytes32(32):s  

File: 2022-10-traderjoe\src\interfaces\IJoeRouter02.sol
 
20:       address token,
21:       uint256 liquidity,
22:       uint256 amountTokenMin,
23:       uint256 amountAVAXMin,
24:       address to,
25:       uint256 deadline,
26:       bool approveMax,
27:       uint8 v,
28:       bytes32 r,
29:       bytes32 s


Variable ordering with 8 slots instead of the current 9: 
bool(1):approveMax,uint8(1):v,address(20):token, uint256(32):liquidity, uint256(32):amountTokenMin,
uint256(32):amountAVAXMin, address(20):to, uint256(32):deadline,
bytes32(32):r,bytes32(32):s  