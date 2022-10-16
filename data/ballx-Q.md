
### Unspecific Compiler Version Pragma

#### Impact
Issue Information: [L003](https://github.com/byterocket/c4-common-issues/blob/main/2-Low-Risk.md#l003---unspecific-compiler-version-pragma)

#### Findings:
```
src/LBErrors.sol::3 => pragma solidity ^0.8.0;
src/LBFactory.sol::3 => pragma solidity ^0.8.0;
src/LBPair.sol::3 => pragma solidity ^0.8.0;
src/LBQuoter.sol::3 => pragma solidity ^0.8.0;
src/LBRouter.sol::3 => pragma solidity ^0.8.0;
src/LBToken.sol::3 => pragma solidity ^0.8.0;
src/interfaces/IJoeFactory.sol::3 => pragma solidity ^0.8.0;
src/interfaces/IJoePair.sol::3 => pragma solidity ^0.8.0;
src/interfaces/IJoeRouter01.sol::3 => pragma solidity ^0.8.0;
src/interfaces/IJoeRouter02.sol::3 => pragma solidity ^0.8.0;
src/interfaces/ILBFactory.sol::3 => pragma solidity ^0.8.0;
src/interfaces/ILBFlashLoanCallback.sol::3 => pragma solidity ^0.8.0;
src/interfaces/ILBPair.sol::3 => pragma solidity ^0.8.0;
src/interfaces/ILBRouter.sol::3 => pragma solidity ^0.8.0;
src/interfaces/ILBToken.sol::3 => pragma solidity ^0.8.0;
src/interfaces/IPendingOwnable.sol::3 => pragma solidity ^0.8.0;
src/interfaces/IWAVAX.sol::3 => pragma solidity ^0.8.0;
src/libraries/BinHelper.sol::3 => pragma solidity ^0.8.0;
src/libraries/BitMath.sol::3 => pragma solidity ^0.8.0;
src/libraries/Buffer.sol::3 => pragma solidity ^0.8.0;
src/libraries/Constants.sol::3 => pragma solidity ^0.8.0;
src/libraries/Decoder.sol::3 => pragma solidity ^0.8.0;
src/libraries/Encoder.sol::3 => pragma solidity ^0.8.0;
src/libraries/FeeDistributionHelper.sol::3 => pragma solidity ^0.8.0;
src/libraries/FeeHelper.sol::3 => pragma solidity ^0.8.0;
src/libraries/JoeLibrary.sol::3 => pragma solidity ^0.8.0;
src/libraries/Math128x128.sol::3 => pragma solidity ^0.8.0;
src/libraries/Math512Bits.sol::3 => pragma solidity ^0.8.0;
src/libraries/Oracle.sol::3 => pragma solidity ^0.8.0;
src/libraries/PendingOwnable.sol::3 => pragma solidity ^0.8.0;
src/libraries/ReentrancyGuardUpgradeable.sol::3 => pragma solidity ^0.8.0;
src/libraries/SafeCast.sol::3 => pragma solidity ^0.8.0;
src/libraries/SafeMath.sol::3 => pragma solidity ^0.8.0;
src/libraries/Samples.sol::3 => pragma solidity ^0.8.0;
src/libraries/SwapHelper.sol::3 => pragma solidity ^0.8.0;
src/libraries/TokenHelper.sol::3 => pragma solidity ^0.8.0;
src/libraries/TreeMath.sol::3 => pragma solidity ^0.8.0;
```



### ReEntrancy

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L207-L223
https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L230-L259 


#### Impact

In a Re-entrancy attack, a malicious contract calls back into the calling contract before the first invocation of the function is finished. This may cause the different invocations of the function to interact in undesirable ways, especially in cases where the function is updating state variables after the external calls.
This may lead to loss of funds, improper value updates, token loss, etc.
