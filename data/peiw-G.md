- Division/Multiplication by two should use bit shifting.
	- `<x> / 2` is the same as `<x> >> 1`. The DIV opcode costs 5 gas, whereas SHR only costs 3 gas.
	- Instance:
		- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L162

- Use a more recent version of Solidity.
	- Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining; Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads; Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings; Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.


- `<ARRAY>.length` should not be looked up in every loop of a for-loop.
	-  The overheads PER LOOP, excluding the first loop: 1). storage arrays incur a Gwarmaccess (100 gas); 2). memory arrays use MLOAD (3 gas); 3). calldata arrays use CALLDATALOAD (3 gas). Caching the length changes each of these to a `DUP<N>` (3 gas), and gets rid of the extra `DUP<N>` needed to store the stack offset.
	- Instances:
		- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L274
		- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L496
		- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L623
		- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L701
		- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBQuoter.sol#L177
		- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L674
		- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L778
		- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L831
		- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L878
		- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L951
		- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L90
		- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L163

- `ABI.ENCODE()` is less efficient than ABI.ENCODEPACKED().
	- Instance:
		- https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L265


