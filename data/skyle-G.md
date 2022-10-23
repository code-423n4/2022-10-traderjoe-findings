G-01 <array>.length should not be looked up in every loop of a for-loop
LBQuoter.sol
  Ln100  for (uint256 j; j < LBPairsAvailable.length; j++) {
  Ln177 for (uint256 j; j < LBPairsAvailable.length; j++) {
LBRouter.sol
  Ln778 for (uint256 i; i < _pairs.length; ++i) {
  Ln831 for (uint256 i; i < _pairs.length; ++i) {
  Ln951 for (uint256 i; i < pairs.length; ++i) {

G-02 ++i costs less gas compared to i++ or i += 1 (same for --i vs i-- or i -= 1)
LBQuoter.sol
  Ln75 for (uint256 i; i < swapLength; i++) {
  Ln100 for (uint256 j; j < LBPairsAvailable.length; j++) {
  Ln154 for (uint256 i = swapLength; i > 0; i--) {
LBRouter.sol
  Ln 711 for (uint256 i = _pairs.length; i != 0; i--) {

G-03 Increments/decrements can be unchecked in for-loops
LBQuoter.sol
  Ln75 for (uint256 i; i < swapLength; i++) {
  Ln100 for (uint256 j; j < LBPairsAvailable.length; j++) {
  Ln154 for (uint256 i = swapLength; i > 0; i--) {

[G-04] DIVISION BY TWO SHOULD USE BIT SHIFTING
libraries/Oracle.sol
  Ln162                 _middle = (_low + _high) / 2;