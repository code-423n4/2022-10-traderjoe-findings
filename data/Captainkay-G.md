1. use calldata on LBTOKEN.sol ---> balanceOfBatch functions(address[] calldata _accounts, uint256[] calldata _ids) -> 1726 gas saved

2. LBTokeb.sol -> function safeBatchTransferFrom(address _from,address _to,uint256[] calldata _ids,uint256[] calldata _amounts) --->11112 gas saved

3. LBPair.sol ->function mint(uint256[] calldata _ids,uint256[] calldata _distributionX,uint256[] calldata _distributionY,address _to) --->1886 saved