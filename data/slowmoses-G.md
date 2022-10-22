## Use Pre Increment Instead of Post Increment

Pre-increment uses a little less gas.

***

for (uint256 i; i < swapLength; i++) {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L75

for (uint256 j; j < LBPairsAvailable.length; j++) {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L100

for (uint256 i = swapLength; i > 0; i--) {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L154

for (uint256 j; j < LBPairsAvailable.length; j++) {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L177

for (uint256 i = _pairs.length; i != 0; i--) {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L711

## Cache Array Length

Array length should be cached instead of requesting it over and over in a for loop.
The cost per iteration depends on the array type, 100 gas for storage, 3 each for memory and calldata.
Caching the value costs only 3 gas.

***

for (uint256 i; i < _ids.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L274

for (uint256 i; i < _ids.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L496

for (uint256 i; i < _ids.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L623

for (uint256 i; i < _ids.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBPair.sol#L701

for (uint256 j; j < LBPairsAvailable.length; j++) {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L100

for (uint256 j; j < LBPairsAvailable.length; j++) {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBQuoter.sol#L177

for (uint256 i; i < depositIds.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L674

for (uint256 i; i < _pairs.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L778

for (uint256 i; i < _pairs.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L831

for (uint256 i; i < _pairs.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L878

for (uint256 i; i < pairs.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBRouter.sol#L951

for (uint256 i; i < _accounts.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L90

for (uint256 i; i < _ids.length; ++i) {
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/LBToken.sol#L163

## Do not Initialize to Default Value

Non-const variables should not be initialized to their default value.
Just using the default costs less gas.
For example use: uint256 abc;
Instead of: uint256 abc=0;

***

uint256 private constant _OFFSET_INITIALIZED = 0;
https://github.com/code-423n4/2022-10-traderjoe/tree/main/src/libraries/Samples.sol#L19

