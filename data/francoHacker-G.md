*[G-01]Cheaper For Loops - 25 to 80 gas per instance 
You can get cheaper for loops (at least 25 gas, however can be up to 80 gas under certain conditions), by rewriting:

''' for (uint256 j; j < LBPairsAvailable.length; j++) {
                // Do the thing
                // Unchecked pre-increment is cheapest
                unchecked { ++i; }
        }       '''

instances:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L771

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L75-L100-L154-L177


*[G-02] <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)

Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset.

INSTANCES:

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L274-L496-L623-L701-L929

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L100

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L674-L711-L778-L831-L878-L951

https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L90-L163


*[G-03]USE A MORE RECENT VERSION OF SOLIDITY

Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

INSTANCES:

ALL CONTRACTS IN SCOPE OF THIS CONTEST