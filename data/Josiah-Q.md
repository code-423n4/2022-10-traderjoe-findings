## SETTING TO DEFAULT VALUES
As documented in the link:

https://docs.soliditylang.org/en/v0.8.17/types.html?highlight=delete#delete

It is recommended using `delete` whenever there is a need to set state variable to its default value, which has a good side effect of saving gas. Here is one instance found.

[Line 68](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/FeeHelper.sol#L68)

## REQUIRE VERSUS ASSERT

As documented in the link below:

https://docs.soliditylang.org/en/v0.8.17/control-structures.html?highlight=ASSERT#panic-via-assert-and-error-via-require

Assert should only be used to test for internal errors, and to check invariants. Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix. Language analysis tools can evaluate your contract to identify the conditions and function calls which will cause a Panic.

Since the following instance relates to user input validation, `require()' should be used instead of `assert()`:

[Line 141](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L141)

## LACK OF EVENT EMISSION AFTER SENSITIVE ACTIONS
In `LBPair.sol`, setting the fee parameters of the pair in `_setFeesParameters()` does not emit relevant event after executing this sensitive action. Consider emitting events after sensitive changes take place, to facilitate tracking and notify off-chain clients following the contract’s activity.

[Line 905](https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L905)

## USE OF BLOCK.TIMESTAMP
Some contract code uses the block.timestamp as part of the calculations and time checks. Nevertheless, timestamps can be slightly altered by miners/validators to favor them in contracts that have logics that depend strongly on them.

Consider taking into account this issue and warning the users that such a scenario could happen. If the alteration of timestamps cannot affect the protocol in any way, consider documenting the reasoning and writing tests enforcing that these guarantees will be preserved even if the code changes in the future. Here are some instances found.

[Line 208](https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L208)
[Line 394](https://github.com/code-423n4/2022-10-traderjoe/blob/main//src/LBPair.sol#L394)
[Line38](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L38)

## CAPITALIZATION OF CONSTANTS
As documented in the link below:

https://docs.soliditylang.org/en/v0.8.17/style-guide.html?highlight=capitalized

Constants should be named with all capital letters with underscores separating words. There are two instances found.

[Line 29](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L29)
[Line 30](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBToken.sol#L30)

## LACK OF CONTRACT EXISTENCE CHECK
The low-level call, `call` will return success if the called account is non-existent, e.g. a contract that is already self-destructed or pending for deployment, as part of the design of EVM. Existence must be checked prior to calling if desired. Here is one instance found.

[Lines 963 - 966](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBRouter.sol#L963-L966)

## COMMENT AND CODE LINE LENGTH
Try limit the length of comments and/or code lines to 80 - 100 character long for readability sake. There are two instances found.

[Line 69](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L69)
[Line 70](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/libraries/Oracle.sol#L70)

## UNCOMMENTED ASSEMBLY BLOCK
Assembly is a low-level language that is harder to parse by readers. Consider including extensive documentation regarding the rationale behind its use, clearly explaining what every single assembly instruction does. This will make it easier for users to trust the code, for reviewers to verify it, and for developers to build on top of it or update it. Note that the use of assembly discards several important safety features of Solidity, which may render the code unsafer and more error-prone. Consider also implementing thorough tests to cover all potential use cases of these functions to ensure they behave as expected.

Here are some instances found.

[Lines 654 - 661](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L654-L661)
[Lines 980 - 987](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L980-L987)
[Lines 1028 - 1031](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBPair.sol#L1028-L1031)

## THRESHOLD LIMIT CHECKS
Certain parameters of the contracts can be configured to invalid values, causing a variety of issues and breaking expected interactions between contracts. `setFlashLoanFee()` allows the owner of `LBFactory.sol` to re-parameterize critical parameter that lacks sanity/threshold/limit checks.

[Lines 474 - 481](https://github.com/code-423n4/2022-10-traderjoe/blob/main/src/LBFactory.sol#L474-L481)

## USE OF HIGHER VERSION OF SOLIDITY
Consider using solidity ^0.8.12 that has added features like `string.concat()`, gas reduction for external calls with returned values, etc